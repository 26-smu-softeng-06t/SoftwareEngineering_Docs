## Class Diagram + ERD

Phase별 클래스 다이어그램/ERD의 구조와 변경 포인트를 정리합니다.

## Phase 1 (MVP)
### Class Diagram 핵심
- `User`, `Word`, `QuizSession`, `QuizAnswer`, `WrongNote`
- `QuizService`, `QuizStartResponse`
- Enum: `Role`, `Difficulty`, `Direction`, `SessionStatus`

### ERD 핵심
- `users`, `words`, `quiz_sessions`, `quiz_answers`, `wrong_notes`
- 관계:
  - `users` 1:N `quiz_sessions`
  - `users` 1:N `wrong_notes`
  - `quiz_sessions` 1:N `quiz_answers`
  - `quiz_answers` N:1 `words`
  - `wrong_notes` N:1 `words`

## Phase 2 (고도화)
### Class Diagram 추가
- `RankingService`, `RankingScheduler`
- `ExcelUploadService`
- `QuizResumeService`
- `WordRelationService`
- `WrongNoteService`

### ERD 추가/변경
- 신규: `word_ranking`, `word_relations`, `word_phrases`
- 확장:
  - `words.phonetic`
  - `quiz_sessions.last_answered_index`
  - `quiz_sessions.status`에 `ABANDONED`

## Phase 3 (PvP · 오프라인)
### Class Diagram 추가
- `PvpRoom`, `PvpParticipant`
- `PvpRoomService`, `PvpGameService`, `PvpBroadcastService`
- `PvpRestController`, `PvpWebSocketController`
- `QuizSyncController`, `QuizSyncService`

### ERD 추가/변경
- 신규: `pvp_rooms`, `pvp_participants`
- 확장: `words.business_example`
- 관계:
  - `pvp_rooms` 1:N `pvp_participants`
  - `pvp_participants` N:1 `users`
