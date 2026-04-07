## Process

**One-line summary**: 스프린트 운영, 통제 프로세스, 이슈/라벨 체계, 개발 절차를 단일 문서로 관리합니다.

---

## 1. 관리 프로세스

### 계획 프로세스 (Agile/Scrum)

| 항목 | 내용 |
|------|------|
| 스프린트 주기 | 1주 |
| 스탠드업 | 수업 후 5~10분 — 한 일 / 할 일 / 블로커 |
| 추가 미팅 | 필요 시 금요일 비대면 (스프린트 계획·회고) |
| 스프린트 계획 | 백로그 선정 → 작업 배분 → 완료 기준 합의 |
| 스프린트 회고 | Keep / Problem / Try |

### 통제 프로세스

Github Project 및 Github Issue, Github Pull Request를 통한 관리

이슈 등록 시 Github Proj에 자동 백로그 등록 -> Assignee(s)가 Pick-up하면 Ready 상태로 변경

-> Assignee(s)가 작업 진행 시 In Progress로 변경하여 작업 시작, Issue내 코멘트 통해서 작업 사항 알림 -> 작업 종료 후 Pull Request 작성

#### Dev Branch

-> Pull Request에 대해서 Copilot Review 진행 -> Lint 오류 없으면 Self-Merge

#### Main Branch

-> -> Pull Request에 대해서 Triage Review 진행 -> 과반(2인+Copilot)이상 동의 시 Main으로 Merge

### 이슈 관리 프로세스

이슈 로그는 Github에 직접 등록한다.

현재 제공되는 Issue Template는 다음과 같다.

1. Bug Report
2. Documentation
3. Meeting / Decision Record
4. PBI / User story
5. Refactor / Tech debt
6. Spike / Research
7. Feature

각 Issue 등록 시 Tag를 지정 할 수 있다.

Tag는 다음을 지원한다.

- Backend
- Blocked
- Decision
- Design
- Duplicate
- Fullstack
- help wanted
- needs-triage
- pbi
- priority/high
- priority/low
- priority/medium
- question
- refactor
- spike
- sprint
- task
- tech-dept
- wontfix
- test

---

## 2. 개발 프로세스

### 요구사항 도출 및 명세
브레인스토밍 → 기능/비기능 요구사항 분류 (FR-001, NFR-001) → 명세서 작성 → 팀 전체 검토 확정

### 설계
| 산출물                      | 도구            |
| ------------------------ | --------------- |
| WBS                      | Google Sheets   |
| Use Case / Class Diagram | Mermaid         |
| ERD                      | Mermaid         |
| Sequence Diagram         | Mermaid         |
| Flow Chart               | Mermaid         |
| UI 설계서                   | Google AI Studio |
| UI Mockup               | Figma           |

### WBS 요약

| 구분 | 기준 |
| --- | --- |
| 관리 단위 | 스프린트(1주) + 기능 묶음(에픽/스토리) |
| 기본 컬럼 | Task, Owner, Start, Due, Status, Dependency, Note |
| 상태 값 | Todo / In Progress / Review / Done / Blocked |
| 동기화 규칙 | 스프린트 계획 회의에서 신규 항목 확정, 데일리 후 상태 업데이트 |

- WBS 상세 일정/담당/의존성은 Google Spreadsheet를 기준으로 운영하고, Wiki에는 관리 규칙과 요약만 유지합니다.

### Git 전략
별도 문서 참고: [Git-Branching-Strategy](Git-Branching-Strategy)
