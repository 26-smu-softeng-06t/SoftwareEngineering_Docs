# ERD (Entity Relationship Diagram)

## Phase 1 — users · words · quiz_sessions · quiz_answers · wrong_notes

```mermaid
erDiagram
  USERS {
    BIGINT id PK
    VARCHAR email
    VARCHAR nickname
    VARCHAR role "USER or ADMIN"
    DATETIME created_at
  }
  WORDS {
    BIGINT id PK
    VARCHAR word
    TEXT meaning
    TEXT example
    VARCHAR difficulty "LOW, MEDIUM, HIGH"
    DATETIME created_at
    DATETIME updated_at
  }
  QUIZ_SESSIONS {
    BIGINT id PK
    BIGINT user_id FK
    VARCHAR difficulty
    VARCHAR direction "KO_EN or EN_KO"
    VARCHAR status "IN_PROGRESS or DONE"
    DATETIME created_at
  }
  QUIZ_ANSWERS {
    BIGINT id PK
    BIGINT session_id FK
    BIGINT word_id FK
    TEXT user_answer
    BOOLEAN is_correct
  }
  WRONG_NOTES {
    BIGINT id PK
    BIGINT user_id FK
    BIGINT word_id FK
    BOOLEAN is_resolved
    DATETIME created_at
  }
  USERS ||--o{ QUIZ_SESSIONS : "starts"
  USERS ||--o{ WRONG_NOTES : "has"
  QUIZ_SESSIONS ||--o{ QUIZ_ANSWERS : "contains"
  QUIZ_ANSWERS }o--|| WORDS : "references"
  WRONG_NOTES }o--|| WORDS : "references"
```

---

## Phase 2 — +word_ranking · word_relations · word_phrases / words.phonetic · quiz_sessions.last_answered_index

```mermaid
erDiagram
  WORDS {
    BIGINT id PK
    VARCHAR word
    TEXT meaning
    TEXT example
    VARCHAR phonetic "신규추가"
    ENUM difficulty
    DATETIME created_at
    DATETIME updated_at
  }
  QUIZ_SESSIONS {
    BIGINT id PK
    BIGINT user_id FK
    ENUM difficulty
    ENUM direction
    ENUM status "IN_PROGRESS, ABANDONED, DONE"
    INT last_answered_index "신규추가"
    DATETIME created_at
  }
  WORD_RANKING {
    BIGINT id PK
    BIGINT word_id FK
    INT wrong_count
    DATETIME ranked_at
  }
  WORD_RELATIONS {
    BIGINT id PK
    BIGINT word_id FK
    TEXT synonyms
    TEXT antonyms
    TEXT derivatives
    DATETIME cached_at
  }
  WORD_PHRASES {
    BIGINT id PK
    BIGINT word_id FK
    TEXT phrase
    TEXT phrase_meaning
    DATETIME cached_at
  }
  WORD_RANKING ||--|| WORDS : "1:1"
  WORD_RELATIONS ||--|| WORDS : "1:1"
  WORD_PHRASES }o--|| WORDS : "N:1"
```

---

## Phase 3 — +pvp_rooms · pvp_participants / words.business_example

```mermaid
erDiagram
  WORDS {
    BIGINT id PK
    VARCHAR word
    TEXT meaning
    TEXT example
    TEXT business_example "신규추가"
    VARCHAR phonetic
    ENUM difficulty
    DATETIME created_at
    DATETIME updated_at
  }
  PVP_ROOMS {
    BIGINT id PK
    VARCHAR room_code "UNIQUE"
    ENUM status "WAITING, IN_PROGRESS, DONE"
    ENUM difficulty
    DATETIME created_at
  }
  PVP_PARTICIPANTS {
    BIGINT id PK
    BIGINT room_id FK
    BIGINT user_id FK
    INT score
    INT elapsed_seconds
    DATETIME finished_at
  }
  USERS {
    BIGINT id PK
    VARCHAR email
    VARCHAR nickname
    ENUM role
    DATETIME created_at
  }
  PVP_ROOMS ||--o{ PVP_PARTICIPANTS : "1:N"
  PVP_PARTICIPANTS }o--|| USERS : "N:1"
```
