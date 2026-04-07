## Sequence Diagrams

Phase별 주요 유스케이스 시퀀스를 정리합니다.

## Phase 1
### 1) SSO 로그인 · JWT 발급
- OAuth2 인증 → 사용자 조회/신규 저장 → JWT 발급

### 2) 퀴즈 시작 · 제출 · 채점
- 세션 생성(`IN_PROGRESS`) → 제출 → 채점(`trim + equalsIgnoreCase`) → 결과 반환
- `wrong_notes` 자동 기록

### 3) 관리자 단어 등록
- 관리자 비밀 키 검증 → role=ADMIN → 관리자 단어 등록

## Phase 2
### 1) 엑셀 업로드
- preview → 유효성 검사 → upload → 일괄 저장

### 2) 퀴즈 이어풀기
- 진행 세션 조회 → 이어풀기/새로시작 → 필요 시 `ABANDONED`

### 3) 랭킹 자동 갱신
- 매일 자정 배치 → `wrong_notes` TOP10 집계 → `word_ranking` 갱신

## Phase 3
### 1) PvP 방 생성 · 참가
- 방 생성(`WAITING`) → 코드 공유 → 참가(`IN_PROGRESS`)

### 2) PvP 실시간 대결
- STOMP 수신 → 채점/점수 갱신 → progress/result 브로드캐스트

### 3) 오프라인 동기화
- localStorage 임시 저장 → 재연결 `/sync` → 누락 답안 upsert
