# Sequence Diagrams

## Phase 1 — MVP

### ① SSO 로그인 · JWT 발급

Google OAuth2 로그인 성공 시 JWT 발급 흐름. 신규 사용자는 users 테이블에 자동 저장됩니다.

```mermaid
sequenceDiagram
  autonumber
  actor User as 사용자
  participant Browser as 브라우저
  participant Server as Spring Boot Server
  participant Google as Google OAuth2
  participant DB as MySQL (users)

  User->>Browser: "Google로 로그인" 클릭
  Browser->>Server: GET /oauth2/authorization/google
  Server->>Google: OAuth2 인증 요청 (redirect)
  Google->>User: Google 로그인 페이지 표시
  User->>Google: 계정 선택 및 동의
  Google->>Server: Authorization Code 전달 (callback)
  Server->>Google: Access Token 교환 요청
  Google-->>Server: Access Token + 사용자 정보 반환
  Server->>DB: findByEmail() 조회
  alt 신규 사용자
    DB-->>Server: 없음 (Optional.empty)
    Server->>DB: save(User, role=USER)
    DB-->>Server: 저장 완료
  else 기존 사용자
    DB-->>Server: User 엔티티 반환
  end
  Server->>Server: JWT Access Token 생성 (jjwt)
  Server-->>Browser: Response Header: Authorization: Bearer {JWT}
  Browser-->>User: 로그인 완료 → 메인 화면 이동
```

---

### ② 퀴즈 시작 · 제출 · 채점

퀴즈 세션 생성 → 전체 제출 → equalsIgnoreCase+trim() 채점 → 오답 자동 기록 흐름입니다.

```mermaid
sequenceDiagram
  autonumber
  actor User as 사용자
  participant Client as 클라이언트
  participant QC as QuizController
  participant QS as QuizService
  participant DB as MySQL

  User->>Client: 난이도·방향 선택 후 퀴즈 시작
  Client->>QC: POST /api/quiz/start {difficulty, direction}
  QC->>QS: startQuiz(userId, difficulty, direction)
  QS->>DB: words 랜덤 조회 (difficulty 조건)
  DB-->>QS: Word 목록 반환
  QS->>DB: quiz_sessions INSERT (status=IN_PROGRESS)
  DB-->>QS: sessionId 반환
  QS-->>QC: QuizStartResponse {sessionId, questions[]}
  QC-->>Client: 201 {sessionId, questions[]}
  Client-->>User: 퀴즈 문항 화면 표시

  User->>Client: 전체 답안 작성 후 제출
  Client->>QC: POST /api/quiz/{sessionId}/submit {answers[]}
  QC->>QS: submitQuiz(sessionId, answers)
  loop 각 문항 채점
    QS->>QS: userAnswer.trim().equalsIgnoreCase(correct)
  end
  QS->>DB: quiz_answers saveAll()
  QS->>DB: status → DONE 업데이트
  QS->>DB: 오답 → wrong_notes INSERT (중복 무시)
  DB-->>QS: 저장 완료
  QS-->>QC: QuizResult
  QC-->>Client: 200 {correctCount, results[], wrongWords[]}
  Client-->>User: 결과 화면 표시
```

---

### ③ 관리자 단어 등록

관리자 비밀 키 검증 후 단어 등록. @PreAuthorize("hasRole('ADMIN')")으로 보호됩니다.

```mermaid
sequenceDiagram
  autonumber
  actor Admin as 관리자
  participant Client as 클라이언트
  participant Filter as JwtAuthFilter
  participant AC_AUTH as AuthController
  participant AUTH_SVC as AuthService
  participant AC_WORD as AdminWordController
  participant AS as AdminWordService
  participant DB as MySQL

  Admin->>Client: 관리자 등록 요청
  Client->>Filter: POST /api/auth/admin/register {email, adminSecretKey}
  Filter->>Filter: JWT 검증
  Filter->>AC_AUTH: 요청 전달
  AC_AUTH->>AUTH_SVC: registerAdmin(email, secretKey)
  AUTH_SVC->>AUTH_SVC: secretKey == ENV(ADMIN_SECRET_KEY)?
  alt 키 불일치
    AUTH_SVC-->>AC_AUTH: 403 Forbidden
    AC_AUTH-->>Client: 403 인증 실패
  else 키 일치
    AUTH_SVC->>DB: UPDATE users SET role=ADMIN
    DB-->>AUTH_SVC: 완료
    AUTH_SVC-->>AC_AUTH: 성공
    AC_AUTH-->>Client: 200 {message: "관리자 등록 완료"}
  end

  Admin->>Client: 단어 등록
  Client->>Filter: POST /api/admin/words {word, meaning, example, difficulty}
  Filter->>Filter: JWT 검증 + role=ADMIN 확인 (@PreAuthorize)
  Filter->>AC_WORD: 요청 전달
  AC_WORD->>AS: createWord(req)
  AS->>DB: words INSERT
  DB-->>AS: Word 엔티티 반환
  AS-->>AC_WORD: WordResponse
  AC_WORD-->>Client: 201 {id, word, createdAt}
  Client-->>Admin: 등록 완료
```

---

## Phase 2 — 고도화

### ① 엑셀 업로드

Apache POI 파싱 → 미리보기 → 일괄 저장. 난이도 오류 행은 skip 후 행 번호 반환합니다.

```mermaid
sequenceDiagram
  autonumber
  actor Admin as 관리자
  participant Client as 클라이언트
  participant EC as ExcelUploadController
  participant ES as ExcelUploadService
  participant POI as Apache POI
  participant DB as MySQL (words)

  Admin->>Client: .xlsx 파일 선택
  Client->>EC: POST /api/admin/words/preview (MultipartFile)
  EC->>ES: preview(file)
  ES->>ES: 파일 크기 검증 (max 5MB)
  ES->>POI: WorkbookFactory.create(file)
  POI-->>ES: Workbook 객체
  loop 각 행 파싱 (A=단어, B=뜻, C=예문, D=난이도)
    ES->>ES: difficulty 유효성 검사 (LOW/MEDIUM/HIGH)
    alt 유효하지 않은 난이도
      ES->>ES: skippedRows에 행 번호 추가
    else 정상
      ES->>ES: PreviewItem 목록에 추가
    end
  end
  ES-->>EC: {preview[], skippedRows[]}
  EC-->>Client: 200 미리보기 데이터
  Client-->>Admin: 미리보기 표시

  Admin->>Client: 업로드 확정
  Client->>EC: POST /api/admin/words/upload (MultipartFile)
  EC->>ES: upload(file)
  ES->>POI: 파싱 재실행
  POI-->>ES: Word 목록
  ES->>DB: wordRepository.saveAll(words)
  DB-->>ES: 저장 완료
  ES-->>EC: {savedCount, skippedRows[]}
  EC-->>Client: 201 {savedCount, skippedRows, message}
  Client-->>Admin: 업로드 완료 알림
```

---

### ② 퀴즈 이어풀기

기존 IN_PROGRESS 세션 감지 → ABANDONED 처리 or 이어풀기 선택 흐름.
컨트롤러: QuizResumeController, 서비스: QuizResumeService.

```mermaid
sequenceDiagram
  autonumber
  actor User as 사용자
  participant Client as 클라이언트
  participant QRC as QuizResumeController
  participant QRS as QuizResumeService
  participant QC as QuizController
  participant QS as QuizService
  participant DB as MySQL

  User->>Client: 퀴즈 시작 버튼 클릭
  Client->>QRC: GET /api/quiz/resume
  QRC->>QRS: getInProgressSession(userId)
  QRS->>DB: findByUserIdAndStatus(IN_PROGRESS)
  DB-->>QRS: 기존 세션 조회

  alt 진행 중 세션 존재
    QRS-->>QRC: 기존 세션 정보 반환
    QRC-->>Client: 200 {sessionId, lastAnsweredIndex, totalCount}
    Client-->>User: "이전 퀴즈 이어풀기" 다이얼로그 표시
    alt 이어풀기 선택
      User->>Client: 이어풀기
      Client-->>User: lastAnsweredIndex+1 번 문항부터 표시
    else 새로 시작
      Client->>QRC: PATCH /api/quiz/{sessionId}/abandon
      QRC->>QRS: 기존 세션 status → ABANDONED
      QRS->>DB: UPDATE status=ABANDONED
      DB-->>QRS: 완료
      Client->>QC: POST /api/quiz/start {difficulty, direction}
      QC->>QS: startQuiz(userId, difficulty, direction)
      QS->>DB: quiz_sessions INSERT (status=IN_PROGRESS)
      DB-->>QS: sessionId 반환
      QS-->>QC: QuizStartResponse {sessionId, questions[]}
      QC-->>Client: 201 새 세션
    end
  else 세션 없음
    QRS-->>QRC: null
    QRC-->>Client: 200 null
    Client->>QC: POST /api/quiz/start {difficulty, direction}
    QC->>QS: startQuiz(userId, difficulty, direction)
    QS->>DB: quiz_sessions INSERT (status=IN_PROGRESS)
    DB-->>QS: sessionId 반환
    QS-->>QC: QuizStartResponse {sessionId, questions[]}
    QC-->>Client: 201 새 세션
  end

  User->>Client: 문항 답변
  Client->>QRC: POST /api/quiz/{sessionId}/answer {wordId, userAnswer}
  QRC->>QRS: saveAnswer(sessionId, wordId, answer)
  QRS->>DB: quiz_answers INSERT
  QRS->>DB: last_answered_index 갱신
  DB-->>QRS: 완료
  QRS-->>QRC: {isCorrect, lastAnsweredIndex}
  QRC-->>Client: 200
```

---

### ④ 랭킹 자동 갱신

@Scheduled cron "0 0 0 \* \* \*" — 매일 자정 wrong_notes 집계 후 word_ranking 갱신합니다.

```mermaid
sequenceDiagram
  autonumber
  participant Scheduler as RankingScheduler
  participant RS as RankingService
  participant WNR as WrongNoteRepository
  participant WRR as WordRankingRepository
  participant Client as 클라이언트
  participant RC as RankingController

  Note over Scheduler: @Scheduled(cron="0 0 0 * * *")<br/>매일 자정 자동 실행
  Scheduler->>RS: refreshDailyRanking()
  Note over RS: refreshDailyRanking() 내부에서<br/>refreshRanking() 호출
  RS->>WNR: 오답 빈도 집계 쿼리
  Note over WNR: SELECT word_id, COUNT(*) as wrong_count<br/>FROM wrong_notes<br/>GROUP BY word_id<br/>ORDER BY wrong_count DESC<br/>LIMIT 10
  WNR-->>RS: [{wordId, wrongCount}] TOP 10
  RS->>WRR: deleteAll()
  WRR-->>RS: 기존 랭킹 삭제 완료
  RS->>WRR: saveAll(newRankings)
  WRR-->>RS: 갱신 완료
  Note over Scheduler: 다음 자정까지 대기

  Client->>RC: GET /api/ranking
  RC->>RS: getRanking()
  RS->>WRR: findTop10ByOrderByWrongCountDesc()
  WRR-->>RS: WordRanking + Word 정보
  RS-->>RC: List<RankingDto>
  RC-->>Client: 200 [{rank, word, wrongCount, rankedAt}]
```

---

## Phase 3 — PvP · 오프라인

### ① PvP 방 생성 · 참가

방 생성 → 초대 코드 공유 → 참가자 입장 시 status IN_PROGRESS 전환 흐름입니다.

```mermaid
sequenceDiagram
  autonumber
  actor PlayerA as 플레이어 A
  actor PlayerB as 플레이어 B
  participant Client_A as 클라이언트 A
  participant Client_B as 클라이언트 B
  participant PRC as PvpRestController
  participant PRS as PvpRoomService
  participant DB as MySQL (pvp_rooms)

  PlayerA->>Client_A: 방 만들기 (난이도 선택)
  Client_A->>PRC: POST /api/pvp/rooms {difficulty: "MEDIUM"}
  Note over PRC: userId: JWT Principal에서 추출
  PRC->>PRS: createRoom(userId, difficulty)
  PRS->>PRS: roomCode = UUID 8자리 생성
  PRS->>DB: pvp_rooms INSERT (status=WAITING)
  PRS->>DB: pvp_participants INSERT (PlayerA)
  DB-->>PRS: 저장 완료
  PRS-->>PRC: PvpRoom
  PRC-->>Client_A: 201 {roomId, roomCode: "AB3X9K2P", status: "WAITING"}
  Client_A-->>PlayerA: 초대 코드 표시

  PlayerA->>PlayerB: 초대 코드 "AB3X9K2P" 공유
  PlayerB->>Client_B: 초대 코드 입력 후 참가
  Client_B->>PRC: POST /api/pvp/rooms/AB3X9K2P/join
  Note over PRC: roomCode: PathVariable / userId: JWT Principal에서 추출
  PRC->>PRS: joinRoom("AB3X9K2P", playerBId)
  PRS->>DB: pvp_participants INSERT (PlayerB)
  PRS->>DB: pvp_rooms status → IN_PROGRESS
  DB-->>PRS: 완료
  PRS-->>PRC: 업데이트된 PvpRoom
  PRC-->>Client_B: 200 {status: "IN_PROGRESS", participants: [...]}
  Client_B-->>PlayerB: 대결 화면 진입
  Client_A-->>PlayerA: 대결 화면 진입 (WebSocket 알림)
```

---

### ② PvP 실시간 대결 (STOMP)

STOMP /app/pvp/{roomCode}/answer 수신 → 채점 → progress 브로드캐스트 → 양측 완료 시 result 전송.

```mermaid
sequenceDiagram
  autonumber
  actor PlayerA as 플레이어 A
  actor PlayerB as 플레이어 B
  participant Client_A as 클라이언트 A
  participant Client_B as 클라이언트 B
  participant WS as STOMP Broker
  participant PWC as PvpWebSocketController
  participant PGS as PvpGameService
  participant PBS as PvpBroadcastService
  participant DB as MySQL

  Note over Client_A,Client_B: STOMP 연결: /ws<br/>구독: /topic/pvp/{roomCode}/progress<br/>구독: /topic/pvp/{roomCode}/result

  PlayerA->>Client_A: 답변 입력
  Client_A->>WS: SEND /app/pvp/{roomCode}/answer {wordId, userAnswer}
  Note over WS,PWC: payload: {wordId, userAnswer}<br/>userId: STOMP Principal에서 추출
  WS->>PWC: handleAnswer(roomCode, payload)
  PWC->>PGS: processAnswer(roomCode, userId, wordId, answer)
  PGS->>PGS: 채점 (equalsIgnoreCase + trim)
  PGS->>DB: pvp_participants score 업데이트
  PGS->>PBS: broadcastProgress(roomCode, progress)
  PBS->>WS: convertAndSend /topic/pvp/{roomCode}/progress
  WS-->>Client_A: {userId: A, currentIndex: 3, correctCount: 2}
  WS-->>Client_B: {userId: A, currentIndex: 3, correctCount: 2}
  Client_B-->>PlayerB: 상대 진행현황 실시간 업데이트

  PlayerB->>Client_B: 답변 입력
  Client_B->>WS: SEND /app/pvp/{roomCode}/answer {wordId, userAnswer}
  WS->>PWC: handleAnswer(roomCode, payload)
  PWC->>PGS: processAnswer(roomCode, userId, wordId, answer)
  PGS->>PGS: 채점
  PGS->>DB: 업데이트
  PGS->>PGS: checkAllFinished(roomCode)
  alt 양측 모두 완료
    PGS->>PGS: 결과 집계 (score, elapsedSeconds 비교)
    PGS->>DB: pvp_rooms status → DONE
    PGS->>PBS: broadcastResult(roomCode, result)
    PBS->>WS: convertAndSend /topic/pvp/{roomCode}/result
    WS-->>Client_A: {winner, players:[{score, elapsedSeconds}]}
    WS-->>Client_B: {winner, players:[{score, elapsedSeconds}]}
    Client_A-->>PlayerA: 최종 결과 화면
    Client_B-->>PlayerB: 최종 결과 화면
  else 아직 진행 중
    PBS->>WS: progress만 브로드캐스트
  end
```

---

### ③ 오프라인 동기화

네트워크 재연결 후 localStorage 임시 데이터 → /sync 엔드포인트(QuizSyncController) → QuizSyncService 누락 답안 upsert 흐름.

```mermaid
sequenceDiagram
  autonumber
  actor User as 사용자
  participant Client as 클라이언트
  participant LS as localStorage
  participant QC as QuizSyncController
  participant QSS as QuizSyncService
  participant DB as MySQL

  User->>Client: 퀴즈 진행 중
  Client->>LS: 답변마다 임시 저장 {sessionId, answers[], lastAnsweredIndex}

  Note over Client: 네트워크 단절 발생

  User->>Client: 답변 계속 입력
  Client->>LS: 로컬에만 저장 (서버 전송 실패)

  Note over Client: 네트워크 재연결

  Client->>LS: 임시 저장 데이터 로드
  LS-->>Client: {sessionId, answers[], lastAnsweredIndex}
  Client->>QC: POST /api/quiz/{sessionId}/sync
  Note over Client,QC: {lastAnsweredIndex: 6, answers: [...]}
  QC->>QSS: syncAnswers(sessionId, lastAnsweredIndex, answers)
  QSS->>DB: 기존 quiz_answers 조회
  DB-->>QSS: 서버에 저장된 답안 목록
  QSS->>QSS: 누락된 답안 식별 (서버 - 로컬 diff)
  loop 누락 답안 upsert
    QSS->>DB: quiz_answers upsert
  end
  QSS->>DB: last_answered_index 갱신
  DB-->>QSS: 완료
  QSS-->>QC: SyncResult {syncedCount, lastAnsweredIndex, status}
  QC-->>Client: 200 {sessionId, lastAnsweredIndex: 6, syncedCount: 3}
  Client->>LS: 임시 저장 데이터 삭제
  Client-->>User: 동기화 완료, 퀴즈 재개
```
