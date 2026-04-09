# User Story

## 1) 문서 목적

이 문서는 TOKKI 서비스의 요구사항을 사용자 관점(User Story)으로 재구성한 기준 문서이다.  
`요구사항명세서.md`, `Use_Case_Diagram`, `TOKKI/README.md`, `TOKKI/CONTRIBUTING.md`를 바탕으로 작성했으며,
Sprint 계획 및 GitHub Issue(PBI)로 분해 가능한 형태를 목표로 한다.

---

## 2) 작성 규칙

- 형식: `As a [사용자], I want [목표], so that [가치]`
- 각 스토리는 요구사항 ID(FR/NFR)와 Phase를 명시한다.
- 수용 기준(AC)은 테스트 가능한 문장으로 작성한다.
- 우선순위는 `Must / Should / Could`로 구분한다.

---

## 3) Epic 정의

- **Epic A. 인증/권한**: 로그인, 관리자 권한
- **Epic B. 학습 경험**: 난이도 선택, 단어 카드, TTS/연관어/숙어
- **Epic C. 퀴즈/피드백**: 주관식 풀이, 이어풀기, 결과/오답노트
- **Epic D. 경쟁/동기부여**: 랭킹, PvP 대결
- **Epic E. 운영/관리**: 관리자 단어 CRUD, 엑셀 업로드
- **Epic F. 신뢰성/품질**: 성능, 보안, 반응형, 정확성

---

## 4) User Stories

### US-01 SSO 로그인

- **Story**: As a learner, I want to sign in with my Google account, so that I can start quickly without creating a new password.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-01, NFR-02
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - Google OAuth2 인증 성공 시 사용자 세션/JWT가 발급된다.
  - 로컬 비밀번호 저장/관리 기능이 존재하지 않는다.
  - 토큰 만료 시 자동 로그아웃 처리된다.

### US-02 관리자 권한 등록/검증

- **Story**: As an admin candidate, I want my admin access to be validated with a secret key, so that only authorized members can use admin features.
- **Actor**: 관리자
- **Requirement Mapping**: FR-01, NFR-02
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 관리자 권한 부여 시 서버에서 비밀 키 검증을 수행한다.
  - 비밀 키는 환경변수로 관리되며 코드/DB에 평문 저장하지 않는다.
  - 관리자 전용 기능 접근 시 role 검증 실패하면 접근이 거부된다.

### US-03 난이도/레벨 선택

- **Story**: As a learner, I want to choose my level before learning or quizzes, so that content matches my current TOEIC range.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-02
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 하/중/상 난이도와 각 10단계를 선택할 수 있다.
  - 학습 시작/퀴즈 시작 전 난이도 선택이 선행된다.
  - 선택한 난이도/단계에 맞는 데이터만 노출된다.

### US-04 단어 카드 학습

- **Story**: As a learner, I want to study words with meaning and example sentences on cards, so that I can memorize by context.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-03
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 영단어 탭에서 단어/뜻/예문을 카드 형태로 조회할 수 있다.
  - 선택한 난이도/단계 기준으로 카드가 제공된다.
  - 카드 UI는 모바일 브라우저에서 사용 가능하다.

### US-05 주관식 퀴즈 풀이

- **Story**: As a learner, I want to solve free-text quizzes in both Korean-to-English and English-to-Korean modes, so that I can practice active recall.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-04, NFR-01, NFR-04
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 퀴즈 유형(한→영 / 영→한)을 선택할 수 있다.
  - 모든 문항은 주관식 입력 방식이다.
  - 채점 시 대소문자 및 앞뒤 공백은 무시된다.
  - 다음 문항 전환은 평균 0.5초 이내를 만족한다.

### US-06 결과 확인 및 오답노트 자동 기록

- **Story**: As a learner, I want to see per-question feedback and automatically collect wrong answers, so that I can focus on weak vocabulary.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-05
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 퀴즈 제출 후 문항별 정오 결과를 확인할 수 있다.
  - 오답은 오답노트에 자동 저장된다.
  - 이전 퀴즈 결과를 재방문하여 조회할 수 있다.
  - 재학습 후 정답 처리된 항목은 오답노트 해제 여부를 선택할 수 있다.

### US-07 관리자 단어 CRUD

- **Story**: As an admin, I want to create, edit, and delete vocabulary entries, so that learning data stays accurate and current.
- **Actor**: 관리자
- **Requirement Mapping**: FR-07
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 관리자 화면에서 단어를 개별 등록/수정/삭제할 수 있다.
  - 일반 사용자 role에서는 해당 기능에 접근할 수 없다.
  - 변경된 단어 데이터가 학습/퀴즈에 반영된다.

### US-08 퀴즈 이어풀기

- **Story**: As a learner, I want to resume an unfinished quiz, so that I do not lose progress when I leave in the middle.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-04, FR-05
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - 중도 이탈 시 문항 단위 진행 상태가 저장된다.
  - 재접속 시 이어풀기 가능한 세션을 선택할 수 있다.
  - 이어풀기 후 최종 제출하면 일반 퀴즈와 동일하게 채점/피드백을 제공한다.

### US-09 TTS/연관어/숙어 확장 학습

- **Story**: As a learner, I want to listen to pronunciation and view related words/phrases, so that I can build deeper vocabulary connections.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-03
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - 단어 카드에서 발음(TTS)을 재생할 수 있다.
  - 유의어/반의어/동의어/파생어 등 관계어 정보를 조회할 수 있다.
  - Phrase/숙어 정보를 함께 확인할 수 있다.

### US-10 랭킹 조회

- **Story**: As a learner, I want to see the most frequently missed words, so that I can prioritize high-risk vocabulary.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-08
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - 오답 빈도 TOP 10 단어를 조회할 수 있다.
  - 랭킹 데이터는 매일 자정 기준으로 자동 갱신된다.
  - 조회 시점에 최신 갱신 결과가 반영된다.

### US-11 엑셀 일괄 업로드

- **Story**: As an admin, I want to upload vocabulary in bulk via Excel with a preview step, so that I can manage large datasets efficiently.
- **Actor**: 관리자
- **Requirement Mapping**: FR-07
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - `.xlsx` 파일 업로드 전 미리보기 화면을 제공한다.
  - 허용 컬럼(단어/뜻/예문/난이도) 형식을 검증한다.
  - 파일 크기 5MB 초과 시 업로드를 차단한다.
  - 유효한 데이터는 DB에 일괄 반영된다.

### US-12 PvP 실시간 대결

- **Story**: As a learner, I want to compete with another learner in real time using invite codes, so that I can stay motivated through competition.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-06
- **Phase**: Phase 3
- **Priority**: Could
- **Acceptance Criteria**
  - 사용자가 PvP 방을 생성하고 초대 코드를 발급할 수 있다.
  - 상대 사용자가 코드로 방에 참가할 수 있다.
  - 대결 중 상대 진행 현황이 실시간으로 표시된다.
  - 종료 후 점수와 소요 시간을 비교한 결과를 제공한다.

### US-13 오프라인 임시 저장/동기화

- **Story**: As a learner, I want my progress to be temporarily stored when network is unstable, so that I can continue learning without losing answers.
- **Actor**: 일반 사용자
- **Requirement Mapping**: NFR-03
- **Phase**: Phase 3
- **Priority**: Could
- **Acceptance Criteria**
  - 네트워크 단절 시 진행 상태를 로컬에 임시 저장한다.
  - 네트워크 복구 시 서버와 동기화 시도를 수행한다.
  - 동기화 성공/실패 상태를 사용자가 인지할 수 있다.

---

## 5) 비기능 요구사항 기반 공통 수용 기준

- **성능(NFR-01)**: 초기 로딩 2초 이내, 문항 전환 0.5초 이내(예외 조건 명시)
- **보안(NFR-02)**: SSO 기반 인증, role 기반 인가, 관리자 비밀 키 보호, 토큰 만료 처리
- **가용성(NFR-03)**: 반응형 웹 지원, 모바일 한 손 조작 고려, 네트워크 단절 대응
- **정확성(NFR-04)**: 단어 데이터 검수 기준 유지, 채점 정규화 규칙(대소문자/공백 무시)

---

## 6) DoD(Definition of Done) 체크리스트

각 User Story 완료 시 아래 항목을 충족한다.

- [ ] 스토리의 Acceptance Criteria를 모두 만족한다.
- [ ] 기능/회귀 테스트를 수행하고 결과를 기록한다.
- [ ] 관련 문서(`요구사항명세서.md`, 다이어그램 문서 등)를 필요 시 업데이트한다.
- [ ] PR 본문에 변경 목적/주요 변경/테스트 방법/관련 이슈를 포함한다.
- [ ] 리뷰 피드백을 반영하고 팀 합의 기준을 충족한다.
# User Story

## 1) 문서 목적

이 문서는 TOKKI 서비스의 요구사항을 사용자 관점(User Story)으로 재구성한 기준 문서이다.  
`요구사항명세서.md`, `Use_Case_Diagram`, `TOKKI/README.md`, `TOKKI/CONTRIBUTING.md`를 바탕으로 작성했으며,
Sprint 계획 및 GitHub Issue(PBI)로 분해 가능한 형태를 목표로 한다.

---

## 2) 작성 규칙

- 형식: `As a [사용자], I want [목표], so that [가치]`
- 각 스토리는 요구사항 ID(FR/NFR)와 Phase를 명시한다.
- 수용 기준(AC)은 테스트 가능한 문장으로 작성한다.
- 우선순위는 `Must / Should / Could`로 구분한다.

---

## 3) Epic 정의

- **Epic A. 인증/권한**: 로그인, 관리자 권한
- **Epic B. 학습 경험**: 난이도 선택, 단어 카드, TTS/연관어/숙어
- **Epic C. 퀴즈/피드백**: 주관식 풀이, 이어풀기, 결과/오답노트
- **Epic D. 경쟁/동기부여**: 랭킹, PvP 대결
- **Epic E. 운영/관리**: 관리자 단어 CRUD, 엑셀 업로드
- **Epic F. 신뢰성/품질**: 성능, 보안, 반응형, 정확성

---

## 4) User Stories

### US-01 SSO 로그인

- **Story**: As a learner, I want to sign in with my Google account, so that I can start quickly without creating a new password.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-01, NFR-02
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - Google OAuth2 인증 성공 시 사용자 세션/JWT가 발급된다.
  - 로컬 비밀번호 저장/관리 기능이 존재하지 않는다.
  - 토큰 만료 시 자동 로그아웃 처리된다.

### US-02 관리자 권한 등록/검증

- **Story**: As an admin candidate, I want my admin access to be validated with a secret key, so that only authorized members can use admin features.
- **Actor**: 관리자
- **Requirement Mapping**: FR-01, NFR-02
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 관리자 권한 부여 시 서버에서 비밀 키 검증을 수행한다.
  - 비밀 키는 환경변수로 관리되며 코드/DB에 평문 저장하지 않는다.
  - 관리자 전용 기능 접근 시 role 검증 실패하면 접근이 거부된다.

### US-03 난이도/레벨 선택

- **Story**: As a learner, I want to choose my level before learning or quizzes, so that content matches my current TOEIC range.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-02
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 하/중/상 난이도와 각 10단계를 선택할 수 있다.
  - 학습 시작/퀴즈 시작 전 난이도 선택이 선행된다.
  - 선택한 난이도/단계에 맞는 데이터만 노출된다.

### US-04 단어 카드 학습

- **Story**: As a learner, I want to study words with meaning and example sentences on cards, so that I can memorize by context.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-03
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 영단어 탭에서 단어/뜻/예문을 카드 형태로 조회할 수 있다.
  - 선택한 난이도/단계 기준으로 카드가 제공된다.
  - 카드 UI는 모바일 브라우저에서 사용 가능하다.

### US-05 주관식 퀴즈 풀이

- **Story**: As a learner, I want to solve free-text quizzes in both Korean-to-English and English-to-Korean modes, so that I can practice active recall.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-04, NFR-01, NFR-04
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 퀴즈 유형(한→영 / 영→한)을 선택할 수 있다.
  - 모든 문항은 주관식 입력 방식이다.
  - 채점 시 대소문자 및 앞뒤 공백은 무시된다.
  - 다음 문항 전환은 평균 0.5초 이내를 만족한다.

### US-06 결과 확인 및 오답노트 자동 기록

- **Story**: As a learner, I want to see per-question feedback and automatically collect wrong answers, so that I can focus on weak vocabulary.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-05
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 퀴즈 제출 후 문항별 정오 결과를 확인할 수 있다.
  - 오답은 오답노트에 자동 저장된다.
  - 이전 퀴즈 결과를 재방문하여 조회할 수 있다.
  - 재학습 후 정답 처리된 항목은 오답노트 해제 여부를 선택할 수 있다.

### US-07 관리자 단어 CRUD

- **Story**: As an admin, I want to create, edit, and delete vocabulary entries, so that learning data stays accurate and current.
- **Actor**: 관리자
- **Requirement Mapping**: FR-07
- **Phase**: Phase 1 (MVP)
- **Priority**: Must
- **Acceptance Criteria**
  - 관리자 화면에서 단어를 개별 등록/수정/삭제할 수 있다.
  - 일반 사용자 role에서는 해당 기능에 접근할 수 없다.
  - 변경된 단어 데이터가 학습/퀴즈에 반영된다.

### US-08 퀴즈 이어풀기

- **Story**: As a learner, I want to resume an unfinished quiz, so that I do not lose progress when I leave in the middle.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-04, FR-05
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - 중도 이탈 시 문항 단위 진행 상태가 저장된다.
  - 재접속 시 이어풀기 가능한 세션을 선택할 수 있다.
  - 이어풀기 후 최종 제출하면 일반 퀴즈와 동일하게 채점/피드백을 제공한다.

### US-09 TTS/연관어/숙어 확장 학습

- **Story**: As a learner, I want to listen to pronunciation and view related words/phrases, so that I can build deeper vocabulary connections.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-03
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - 단어 카드에서 발음(TTS)을 재생할 수 있다.
  - 유의어/반의어/동의어/파생어 등 관계어 정보를 조회할 수 있다.
  - Phrase/숙어 정보를 함께 확인할 수 있다.

### US-10 랭킹 조회

- **Story**: As a learner, I want to see the most frequently missed words, so that I can prioritize high-risk vocabulary.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-08
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - 오답 빈도 TOP 10 단어를 조회할 수 있다.
  - 랭킹 데이터는 매일 자정 기준으로 자동 갱신된다.
  - 조회 시점에 최신 갱신 결과가 반영된다.

### US-11 엑셀 일괄 업로드

- **Story**: As an admin, I want to upload vocabulary in bulk via Excel with a preview step, so that I can manage large datasets efficiently.
- **Actor**: 관리자
- **Requirement Mapping**: FR-07
- **Phase**: Phase 2
- **Priority**: Should
- **Acceptance Criteria**
  - `.xlsx` 파일 업로드 전 미리보기 화면을 제공한다.
  - 허용 컬럼(단어/뜻/예문/난이도) 형식을 검증한다.
  - 파일 크기 5MB 초과 시 업로드를 차단한다.
  - 유효한 데이터는 DB에 일괄 반영된다.

### US-12 PvP 실시간 대결

- **Story**: As a learner, I want to compete with another learner in real time using invite codes, so that I can stay motivated through competition.
- **Actor**: 일반 사용자
- **Requirement Mapping**: FR-06
- **Phase**: Phase 3
- **Priority**: Could
- **Acceptance Criteria**
  - 사용자가 PvP 방을 생성하고 초대 코드를 발급할 수 있다.
  - 상대 사용자가 코드로 방에 참가할 수 있다.
  - 대결 중 상대 진행 현황이 실시간으로 표시된다.
  - 종료 후 점수와 소요 시간을 비교한 결과를 제공한다.

### US-13 오프라인 임시 저장/동기화

- **Story**: As a learner, I want my progress to be temporarily stored when network is unstable, so that I can continue learning without losing answers.
- **Actor**: 일반 사용자
- **Requirement Mapping**: NFR-03
- **Phase**: Phase 3
- **Priority**: Could
- **Acceptance Criteria**
  - 네트워크 단절 시 진행 상태를 로컬에 임시 저장한다.
  - 네트워크 복구 시 서버와 동기화 시도를 수행한다.
  - 동기화 성공/실패 상태를 사용자가 인지할 수 있다.

---

## 5) 비기능 요구사항 기반 공통 수용 기준

- **성능(NFR-01)**: 초기 로딩 2초 이내, 문항 전환 0.5초 이내(예외 조건 명시)
- **보안(NFR-02)**: SSO 기반 인증, role 기반 인가, 관리자 비밀 키 보호, 토큰 만료 처리
- **가용성(NFR-03)**: 반응형 웹 지원, 모바일 한 손 조작 고려, 네트워크 단절 대응
- **정확성(NFR-04)**: 단어 데이터 검수 기준 유지, 채점 정규화 규칙(대소문자/공백 무시)

---

## 6) DoD(Definition of Done) 체크리스트

각 User Story 완료 시 아래 항목을 충족한다.

- [ ] 스토리의 Acceptance Criteria를 모두 만족한다.
- [ ] 기능/회귀 테스트를 수행하고 결과를 기록한다.
- [ ] 관련 문서(`요구사항명세서.md`, 다이어그램 문서 등)를 필요 시 업데이트한다.
- [ ] PR 본문에 변경 목적/주요 변경/테스트 방법/관련 이슈를 포함한다.
- [ ] 리뷰 피드백을 반영하고 팀 합의 기준을 충족한다.

