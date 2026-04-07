## Architecture

클래스/ERD/시퀀스/API 설계 문서의 단일 진입점입니다.

### 1. Class Diagram & ERD
- 상세 문서: [Class-Diagram-and-ERD](Class-Diagram-and-ERD)
- 요약:
  - Phase 1: `users`, `words`, `quiz_sessions`, `quiz_answers`, `wrong_notes`
  - Phase 2: `word_ranking`, `word_relations`, `word_phrases`, `last_answered_index`, `phonetic`
  - Phase 3: `pvp_rooms`, `pvp_participants`, `business_example`

### 2. Sequence Diagrams
- 상세 문서: [Sequence-Diagrams](Sequence-Diagrams)
- 플로우:
  - Phase 1: 로그인/JWT, 퀴즈 채점, 관리자 등록
  - Phase 2: 엑셀 업로드, 이어풀기, 랭킹 배치
  - Phase 3: PvP 생성·실시간 대결, 오프라인 동기화

### 3. API Spec (Wiki 기준)
- 엔드포인트별 메서드/경로/권한/설명을 Wiki에 유지
- 주요 도메인: 퀴즈, 단어 관리, 랭킹, PvP, 동기화

#### API 요약표

| Domain | Method | Path | Auth | Description |
| --- | --- | --- | --- | --- |
| Auth | `GET` | `/oauth2/authorization/google` | Public | Google OAuth2 로그인 시작 |
| Auth | `POST` | `/api/auth/admin/register` | USER | 관리자 비밀키 검증 후 ADMIN 승격 |
| Quiz | `POST` | `/api/quiz/start` | USER | 난이도/방향 기반 퀴즈 세션 시작 |
| Quiz | `POST` | `/api/quiz/{sessionId}/submit` | USER | 전체 답안 제출 및 채점 |
| Quiz Resume | `GET` | `/api/quiz/resume` | USER | 진행 중 세션 조회 |
| Quiz Resume | `PATCH` | `/api/quiz/{sessionId}/abandon` | USER | 기존 진행 세션 포기 처리 |
| Admin Word | `POST` | `/api/admin/words` | ADMIN | 단어 등록 |
| Admin Word | `POST` | `/api/admin/words/preview` | ADMIN | 엑셀 업로드 미리보기 |
| Admin Word | `POST` | `/api/admin/words/upload` | ADMIN | 엑셀 일괄 반영 |
| Ranking | `GET` | `/api/ranking` | USER | 오답 빈도 Top 10 조회 |
| PvP | `POST` | `/api/pvp/rooms` | USER | PvP 방 생성 |
| PvP | `POST` | `/api/pvp/rooms/{roomCode}/join` | USER | PvP 방 참가 |

#### WebSocket 요약

| Type | Endpoint | Description |
| --- | --- | --- |
| Connect | `/ws` | STOMP 연결 |
| Publish | `/app/pvp/{roomCode}/answer` | 답안 전송 |
| Subscribe | `/topic/pvp/{roomCode}/progress` | 진행 상태 수신 |
| Subscribe | `/topic/pvp/{roomCode}/result` | 최종 결과 수신 |
