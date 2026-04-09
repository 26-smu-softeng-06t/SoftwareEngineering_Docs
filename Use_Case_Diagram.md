# Use Case Diagram

## 전체 시스템 유스케이스 (Phase 1 ~ Phase 3)

```mermaid
graph LR
  %% ── Actors ──────────────────────────────────────────────
  User(["👤<br/>일반 사용자"])
  Admin(["👤<br/>관리자"])
  Google(["🌐<br/>Google OAuth2"])
  Scheduler(["⚙️<br/>스케줄러"])

  %% ── System Boundary ──────────────────────────────────────
  subgraph SYS["🖥️  영단어 학습 시스템"]
    direction TB

    subgraph AUTH["인증 / 계정"]
      UC01("SSO 로그인")
      UC02("관리자 권한 등록")
    end

    subgraph LEARN["단어 학습"]
      UC03("난이도 선택")
      UC04("단어 카드 학습")
      UC05("TTS 발음 듣기")
      UC06("관계어 조회<br/>유의어·반의어·파생어")
      UC07("숙어 / Phrase 조회")
    end

    subgraph QUIZ["퀴즈"]
      UC08("퀴즈 시작<br/>한→영 / 영→한 선택")
      UC09("퀴즈 이어풀기")
      UC10("답안 제출 · 채점")
      UC11("결과 · 피드백 확인")
      UC12("오답노트 조회")
      UC13("오답노트 해제")
    end

    subgraph RANKING["랭킹"]
      UC14("랭킹 조회<br/>오답 빈도 TOP 10")
      UC15("랭킹 자동 갱신<br/>매일 자정")
    end

    subgraph PVP["PvP 실시간 대결  ·  Phase 3"]
      UC16("PvP 방 생성")
      UC17("초대 코드 공유")
      UC18("PvP 방 참가")
      UC19("실시간 대결")
      UC20("대결 결과 확인")
    end

    subgraph OFFLINE["오프라인 지원  ·  Phase 3"]
      UC21("답변 임시 저장<br/>localStorage")
      UC22("네트워크 재연결<br/>데이터 동기화")
    end

    subgraph WORD_ADMIN["단어 관리  ·  관리자 전용"]
      UC23("단어 개별 등록")
      UC24("단어 수정")
      UC25("단어 삭제")
      UC26("엑셀 업로드 미리보기")
      UC27("엑셀 일괄 업로드")
    end
  end

  %% ── Actor → Use Case 연관 ───────────────────────────────

  %% 일반 사용자
  User --> UC01
  User --> UC03
  User --> UC04
  User --> UC08
  User --> UC09
  User --> UC10
  User --> UC11
  User --> UC12
  User --> UC13
  User --> UC14
  User --> UC16
  User --> UC18
  User --> UC19
  User --> UC20
  User --> UC21
  User --> UC22

  %% 관리자 (일반 사용자 기능 + 관리 기능)
  Admin -.->|"<<generalization>>"| User
  Admin --> UC02
  Admin --> UC23
  Admin --> UC24
  Admin --> UC25
  Admin --> UC26
  Admin --> UC27

  %% 외부 시스템
  Google -.->|"OAuth2 인증 제공"| UC01
  Scheduler --> UC15

  %% ── <<include>> 관계 ────────────────────────────────────
  UC04 -.->|"<<include>>"| UC03
  UC08 -.->|"<<include>>"| UC03
  UC10 -.->|"<<include>>"| UC11
  UC11 -.->|"<<include>>"| UC12
  UC27 -.->|"<<include>>"| UC26
  UC16 -.->|"<<include>>"| UC17
  UC19 -.->|"<<include>>"| UC18

  %% ── <<extend>> 관계 ─────────────────────────────────────
  UC09 -.->|"<<extend>>"| UC08
  UC13 -.->|"<<extend>>"| UC12
  UC05 -.->|"<<extend>>"| UC04
  UC06 -.->|"<<extend>>"| UC04
  UC07 -.->|"<<extend>>"| UC04
  UC22 -.->|"<<extend>>"| UC21
  UC20 -.->|"<<include>>"| UC19
```

---

## Phase별 유스케이스 범위

| Phase | 포함 유스케이스 |
|-------|----------------|
| **Phase 1** (MVP) | SSO 로그인, 관리자 권한 등록, 난이도 선택, 단어 카드 학습, 퀴즈 시작, 답안 제출·채점, 결과·피드백 확인, 오답노트 조회, 단어 개별 등록·수정·삭제 |
| **Phase 2** | 퀴즈 이어풀기, TTS 발음 듣기, 관계어 조회, 숙어/Phrase 조회, 오답노트 해제, 랭킹 조회, 랭킹 자동 갱신, 엑셀 업로드 미리보기, 엑셀 일괄 업로드 |
| **Phase 3** | PvP 방 생성, 초대 코드 공유, PvP 방 참가, 실시간 대결, 대결 결과 확인, 답변 임시 저장, 네트워크 재연결 데이터 동기화 |

---

## Actor 설명

| Actor | 설명 |
|-------|------|
| 일반 사용자 | Google SSO로 로그인한 일반 학습자. `role = USER` |
| 관리자 | 비밀 키 검증 후 권한 승격된 사용자. `role = ADMIN`. 일반 사용자 기능 전부 포함 |
| Google OAuth2 | 외부 인증 서버. SSO 로그인 흐름에서 Authorization Code 및 사용자 정보 제공 |
| 스케줄러 | Spring `@Scheduled` 기반 내부 시스템. 매일 자정 랭킹 자동 갱신 실행 |

---

## 주요 관계 설명

| 관계 | 유스케이스 쌍 | 설명 |
|------|--------------|------|
| `<<include>>` | 단어 카드 학습 → 난이도 선택 | 학습 전 반드시 난이도를 선택해야 함 |
| `<<include>>` | 퀴즈 시작 → 난이도 선택 | 퀴즈 전 반드시 난이도를 선택해야 함 |
| `<<include>>` | 답안 제출·채점 → 결과·피드백 확인 | 제출 시 항상 결과가 표시됨 |
| `<<include>>` | 결과·피드백 확인 → 오답노트 조회 | 오답은 자동으로 오답노트에 기록됨 |
| `<<include>>` | 엑셀 일괄 업로드 → 엑셀 업로드 미리보기 | 업로드 전 반드시 미리보기 단계를 거침 |
| `<<include>>` | PvP 방 생성 → 초대 코드 공유 | 방 생성 시 항상 초대 코드가 발급됨 |
| `<<include>>` | 실시간 대결 → PvP 방 참가 | 대결은 방 참가 후에만 가능 |
| `<<include>>` | 대결 결과 확인 → 실시간 대결 | 대결 완료 후 결과가 표시됨 |
| `<<extend>>` | 퀴즈 이어풀기 → 퀴즈 시작 | IN_PROGRESS 세션이 있을 때만 선택적으로 발동 |
| `<<extend>>` | 오답노트 해제 → 오답노트 조회 | 사용자가 선택적으로 오답 해제 가능 |
| `<<extend>>` | TTS 발음 듣기 → 단어 카드 학습 | 선택적으로 발음 청취 가능 |
| `<<extend>>` | 관계어 조회 → 단어 카드 학습 | 선택적으로 유의어 등 조회 가능 |
| `<<extend>>` | 숙어/Phrase 조회 → 단어 카드 학습 | 선택적으로 숙어 조회 가능 |
| `<<extend>>` | 데이터 동기화 → 답변 임시 저장 | 네트워크 재연결 시 localStorage → 서버 동기화 |
| `<<generalization>>` | 관리자 → 일반 사용자 | 관리자는 일반 사용자의 모든 유스케이스를 상속 |
