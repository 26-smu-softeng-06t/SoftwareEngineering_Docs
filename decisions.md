# TOKKI Decisions Log

본 문서는 팀의 확정 의사결정(DEC)만 별도로 모은 기록이다.  
회의 상세 대화/운영 로그는 저장소의 회의록 문서를 참고한다.

---

## DEC-001 인증: SSO-Only 채택
- 상태: Approved
- 결정일: 2026-04-04
- 발의자: L
- 검토: K
- 결론:
  - Google OAuth2 기반 SSO 로그인만 지원
  - 자체 비밀번호 저장/검증 로직은 도입하지 않음
  - 관리자 권한은 DB `role` + 비밀 키 검증으로 분리
- 근거 문서:
  - `요구사항명세서.md` (FR-01, NFR-02)

### 법/고시 근거
- 「개인정보 보호법」 제3조, 제29조, 제30조, 제34조, 제39조의3
- 「개인정보 보호법 시행령」 제30조, 제30조의2
- 「개인정보의 안전성 확보조치 기준」(행정안전부 고시) 제4조, 제6조, 제7조, 제8조, 제9조, 제10조

---

## DEC-002 요구사항 관리: SRS 단일본 운영
- 상태: Approved
- 결정일: 2026-04-01 ~ 2026-04-02
- 발의자: L(초안), K(통합)
- 결론:
  - 요구사항 기준 문서는 `요구사항명세서.md` 단일본으로 운영
  - FR/NFR 변경은 Issue + PR 승인 후 반영

---

## DEC-003 협업 운영: Scrum + GitHub 중심
- 상태: Approved
- 결정일: 2026-03-25 (보강: 2026-04-01)
- 발의자: K
- 결론:
  - 1주 Sprint, 스탠드업, 계획/회고 운영
  - GitHub Project/Issue/PR 중심 추적
  - 의사결정은 `Meeting / Decision Record` 형태로 기록

---

## DEC-004 브랜치 전략: dev 통합 + main 안정
- 상태: Approved
- 결정일: 2026-03-25 (재확인: 2026-04-04)
- 발의자: K
- 결론:
  - `feat/* -> dev` PR 통합
  - `dev -> main` 승격은 PR 기준으로 진행하며, maintainer 2명 이상의 승인 리뷰가 있을 때만 허용
  - `main` 브랜치는 직접 push를 금지하고, GitHub branch protection으로 승인 리뷰 요건을 강제

---

## DEC-005 범위 전략: Phase 기반 개발
- 상태: Approved
- 결정일: 2026-04-04
- 발의자: K
- 결론:
  - Phase 1(MVP): SSO, 난이도/카드, 주관식 퀴즈, 오답노트, 관리자 CRUD
  - Phase 2~3: 엑셀 업로드, 랭킹, 이어풀기, TTS, Relation 추천, Phrase/숙어, PvP, 비즈니스 예문

---

## DEC-006 프로젝트명 확정
- 상태: Approved
- 결정일: 2026-04-04
- 발의자: 팀 전체 (정리: K)
- 결론:
  - 공식 명칭: `TOKKI (TOEIC Optimized Knowledge & Keyword Index)`
  - 보고서/PPT/저장소 표기 일원화
