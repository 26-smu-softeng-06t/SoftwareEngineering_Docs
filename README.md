# SoftwareEngineering_Docs

상명대학교 26년 1학기 소프트웨어엔지니어링 과목 과제 수행을 위한 TOKKI 문서 묶음입니다.

## Canonical Documents

- `요구사항명세서.md`: FR/NFR 및 Phase 범위 기준 문서
- `User_Story.md`: 사용자 스토리와 수용 기준
- `관리프로세스.md`: Scrum, Issue, PR, Review 운영 기준
- `git_branching_strat.md`: `dev` 통합 + `main` 안정 브랜치 전략
- `decisions.md`: 승인된 의사결정 로그
- `개발환경_및_구조.md`: 현재 TOKKI 저장소 구조, 환경변수, AI 지시문 운영 기준
- `ERD.md`, `Class_Diagram.md`, `Sequence_Diagram.md`, `Use_Case_Diagram.md`: 설계 산출물

## Current Repository Layout

`TOKKI` 저장소는 2026-04-28 기준으로 다음 구조를 사용합니다.

- `frontend/`: Vite React 클라이언트
- `backend/`: Spring Boot API 서버
- `scripts/`: 로컬/운영 보조 스크립트
- `.env.example`: 공유 가능한 환경변수 템플릿
- `.env`: 개인 로컬 비밀값 파일이며 Git에 커밋하지 않음

상세 내용은 `개발환경_및_구조.md`를 기준으로 합니다.
