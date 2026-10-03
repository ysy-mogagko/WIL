# 2026-10-03 (토) J-planner 작업 기록

> 시간: 10:00 ~ 12:00 (KST)
> 프로젝트: **J-planner** — MBTI J를 위한 반응형 웹 플래너 (Next.js + Spring Boot + MySQL)
> 진행 방식: AI 에이전트 창을 역할별로 띄워 두고, 나는 PM(대표) 역할로 결정만 내린다
> - **PO**: 기획·결정 기록·백로그·화면 검수
> - **BE**: Java 25 / Spring Boot 4.1 / MySQL 8.4
> - **FE**: Next.js / React / Redux Toolkit
> - **ORCH (Orchestrator)**: 창 하나가 조사·개발·리뷰·검수 에이전트를 직접 띄워 스토리 하나를 끝까지 맡는 방식. US-25(메모)로 시험 중
> - 창끼리는 공유 대시보드(메시지·작업 레인·스토리·로그)로 대화한다

### 레포지토리

| 레포 | 내용 | 오늘 커밋 |
|---|---|---|
| [awesomedesk/j-planner-product](https://github.com/awesomedesk/j-planner-product) | 기획 문서 (요구사항·결정 기록·백로그·DB/API 설계·M5 기획) | 6개 |
| [awesomedesk/j-planner-fe](https://github.com/awesomedesk/j-planner-fe) | 프론트엔드 (Next.js 16 · React 19 · Redux Toolkit) | 4개 |
| [awesomedesk/J-planner-BE](https://github.com/awesomedesk/J-planner-BE) | 백엔드 (Java 25 · Spring Boot 4.1 · MySQL 8.4) | 0개 (설계 문서는 product 레포에) |

---

## 오늘 한 줄 요약

첫 배포 전 마일스톤 **M5(로그인·보안·어드민)** 범위를 정하고 보안 조사를 반영해 설계를 확정했다. **Todo 박스·순서 바꾸기(US-13·14)** 와 **메모(US-25, Orchestrator 시험)** 가 검수를 통과했다. 조사 중 찾은 Next.js 취약점 때문에 **FE를 Next 16 + React 19로 올렸다.**

---

## 시간순 흐름

| 시각 | 누가 | 한 일 |
|---|---|---|
| 10:43 | PO | M5 범위 결정(D-060), 보안 조사 근거를 담은 기획 문서 [`11-m5-login-security.md`](https://github.com/awesomedesk/j-planner-product/blob/main/11-m5-login-security.md), 스토리 US-31~44 추가 |
| 10:46 | FE | US-14 Todo 끌어서 순서 바꾸기 ([`eb3c1e9`](https://github.com/awesomedesk/j-planner-fe/commit/eb3c1e9)) |
| 10:52 | FE | US-14 버그 수정: 이미 사라진 포인터에 `setPointerCapture`를 부르면 끌기가 시작되지 않던 문제 ([`e27cda3`](https://github.com/awesomedesk/j-planner-fe/commit/e27cda3)) |
| 10:54 | BE | M5 BE 설계 제안 [`12-m5-be-proposal.md`](https://github.com/awesomedesk/j-planner-product/blob/main/12-m5-be-proposal.md) (로그인 유지·사용자별 DB·API·요청 제한·메일·어드민, 결정 9건) |
| 11:02 | FE | 메모 시험 브랜치(`trial/us-25-memo`, 12커밋)를 main에 합침 ([`fdc48a9`](https://github.com/awesomedesk/j-planner-fe/commit/fdc48a9)) |
| 11:04 | PO | 대표 답장 + BE 제안 반영해 M5 세부 결정 (D-061, [결정 기록](https://github.com/awesomedesk/j-planner-product/blob/main/03-decisions.md)) |
| 11:08 | BE | 서버 대수(한 대 vs 두 대) 비교를 설계 문서에 추가 |
| 11:09 | PO | US-13·US-14 화면 검수 통과 기록 |
| 11:17 | FE | **Next.js 15.4.6 → 16.3.8, React 18 → 19.2.8** 업그레이드 (US-31, `0713cc7`) |
| 12:01 | ORCH | US-25 메모 검수 통과, Orchestrator 시험 비교표 작성 ([`10-orchestrator-trial.md`](https://github.com/awesomedesk/j-planner-product/blob/main/10-orchestrator-trial.md)) |

---

## PO (기획·검수)

### 1. M5 범위 확정 (D-060)
첫 배포는 로그인까지 만든 뒤에 하기로 했다(D-059). 그 묶음을 M5로 정했다.
- **회원**: 이메일 가입·로그인, 이메일 인증, 비밀번호 찾기, 구글 로그인, 로그아웃, 탈퇴, 휴면, 내 계정
- **서비스 화면**: 메인, 소개, 로그인, 이용약관, 개인정보처리방침, 문의처, 둘러보기
- **보안 19개 항목 전부 필수** + 출시 전 모든 화면·API 보안 전수검사
- **어드민 서버**는 서비스와 따로 만든다
- 비밀번호는 **최소 15자 + 유출 비밀번호 차단**, 조합 규칙(특수문자 필수 등)은 없음 (NIST SP 800-63B-4)

### 2. 보안·법 조사 (조사 에이전트 2개 병렬 실행 → 핵심 출처 직접 재확인)
- **Next.js 15.4.6이 React2Shell(CVE-2025-55182 / CVE-2025-66478, CVSS 10)에 해당**했다. App Router를 쓰면 Server Actions가 없어도 영향을 받는다. 15.4 라인은 이후 보안 패치(2026-05, 13건)를 받지 못했고, Next 15 지원은 2026-10-21에 끝난다 → **16 LTS 최신으로 올리기로 결정**
- npm 공급망 공격(2025-09 Shai-Hulud 웜, 2026-08 ChainDrop) → lockfile 점검을 요구사항에 넣었다
- 로그인 유지는 1st-party 웹이면 **서버 세션 + HttpOnly 쿠키**가 권장된다 (IETF OAuth 브라우저 앱 초안, OWASP)
- 같은 이메일 계정을 자동으로 합치면 **계정 선점(pre-hijacking) 공격**에 노출된다 (MSRC·USENIX 2022 연구)
- 한국법
  - 휴면 계정 의무(유효기간제)는 2023-09 폐지됐다 → 자체 정책으로 운영
  - 만 14세 미만은 가입을 막는다
  - 유출 시 72시간 안에 이용자 통지, 해킹이면 1명이라도 신고
  - 운영자 접속 기록 1년 보관·월 1회 점검 (2025 개정분은 2026-10-31부터 적용)

### 3. M5 세부 결정 (D-061)
- **계정 합치기 없음**
  - 같은 이메일로는 두 번째 계정을 만들 수 없다
  - 구글 연결은 로그인한 뒤 '내 계정'에서만 한다
- **임시 가입 테이블** (대표 아이디어)
  - 가입하면 임시로만 저장하고, 인증 링크를 눌러야 회원이 생긴다
  - 남의 이메일을 미리 점유해도 진짜 주인을 막지 못한다 → 선점 공격 차단
- '이미 가입된 이메일' 안내는 가입 여부를 드러낸다 → 요청 횟수 제한을 강하게
- BE 제안 9건 모두 승인
  - 서버 세션(Spring Session + Redis)
  - 최대 30일·14일 미사용 만료·기기 10대
  - FE와 API는 같은 주소(`/api`)
  - 일기·메모는 AES-256-GCM 암호화
  - 메일은 Amazon SES 서울
  - 어드민 DB 계정은 본문 테이블 권한 자체가 없음
- 휴면 4년 뒤 삭제
- 어드민 서버는 새 저장소에서 Orchestrator가 만든다
- 게스트 브라우저 저장(+가입 시 가져오기)은 다음 마일스톤

### 4. 화면 검수 (Chrome + 실제 서버)
- **US-13 그날의 Todo 박스 ✅**
  - 체크하면 박스에서 숨기고 '완료 n개 보기'로 보여준다
  - 체크를 풀면 원래 자리로 돌아온다
  - 모바일 일간 Todo 탭과 전체 화면 수정 + 'Todo 삭제'도 확인
- **US-14 Todo 순서 바꾸기 ✅**
  - 끌어서 놓으면 `PUT /todos/{id}/position` 200, 새로고침 뒤에도 유지
  - 끈 뒤에는 수정 창이 열리지 않는다
- FE가 기획 빈틈을 메운 선택 10개는 추천안대로 승인

---

## BE

- **M5 설계 제안** (`12-m5-be-proposal.md`)
  - 로그인 유지 방식 비교: 서버 세션+Redis / JDBC 세션 / JWT → 서버 세션 추천
  - DB: `users`, `user_identities`(provider+sub 유일), `password_credentials`, `auth_tokens`(SHA-256 해시만), `audit_logs`(앱 계정은 INSERT·SELECT만)
  - 모든 테이블에 `user_id`를 붙이고, 유일 규칙은 사용자별로
  - **교차 계정 테스트**: 사용자 A·B로 모든 엔드포인트를 돌리고, openapi의 모든 경로가 테스트 목록에 있는지도 검사한다. ArchUnit으로 user_id 없는 조회를 막는다
  - 요청 제한: 로그인 실패 5회 30초 → 10회 5분 → 20회 30분 + IP·가입·메일·일반 API 기준표
  - 의존성 확인: Boot 4.1.1 = Framework 7.0.9 / Security 7.1.1. 8/20 Spring Security 공지 4건이 모두 고쳐진 버전. MySQL 최신 8.4.12
- **서버 대수 비교** — 한 대(8GB, 보완 4가지 필수) vs 두 대(서비스 / 관리·관제 분리). 결정은 서버를 고를 때
  - 관제는 자체 설치 Prometheus·Grafana OSS 추천. Grafana Cloud 무료 플랜은 데이터가 해외로 나가고 보관이 14일이라 접속 기록 1년 보관 요건에 맞지 않는다
- 다음: US-32(사용자별 데이터 + 기존 1인 데이터를 admin 계정으로 이전)부터 TDD

## FE

- **US-14 Todo 끌어서 순서 바꾸기**
  - PC는 4px 넘게 움직이면 끌기 시작, 모바일은 0.4초 길게 누르면 시작
  - 키보드 Alt+↑/↓ 지원
  - 실패하면 원래 순서로 되돌린다
  - 끌기 로직은 `useDragReorder`, 순서 계산은 `reorder`로 분리
- **메모 시험 브랜치 main 합치기**: 충돌 2곳(일간 탭·사이드바 연결 지점)에서 Todo와 메모를 둘 다 꽂음, 테스트 566개 통과
- **US-31 Next 16 업그레이드**
  - next 16.3.8, react 19.2.8, Redux Toolkit 2.13, react-redux 9.3
  - 안 쓰는 `next-redux-wrapper`·`dotenv` 제거 (공급망 노출 줄이기)
  - `next lint` → ESLint 9 flat config
  - React 19 규칙 대응: 렌더 중 ref 쓰기 → `useLatest`, effect 안 setState → 렌더 중 이전 값 비교
  - **보안 하한선 테스트** 추가: next ≥ 16.3.6, react 19.2.x, lockfile에 Shai-Hulud~ChainDrop 오염 버전이 없는지 검사
  - 테스트 574개 통과, tsc·lint 깨끗, Turbopack 빌드 성공

## ORCH (Orchestrator 시험 — US-25 메모)

- 메모 검수 통과: 테스트 495개 + Chrome PC/모바일 21항목
- 시험 결과 (10/1 21:10 시작 → 10/3 오전 통과, 약 1.5일)
  - 대표가 손댄 횟수 **17회**: 결정·답 8, 승인 3, 서버·환경 4, 기타
  - 개발 전에 찾은 기획 빈틈 4개(+보충 2) → D-055. 개발 중 3개 → D-057, 리뷰 중 3개 → D-058
  - 리뷰에서 고칠 것 3건, 되돌림 2회, 화면 검수 FAIL 0
  - 운영 문제 4건: git index.lock, worktree 절대 경로, rebase 사용자 정보, 3000번 포트에 다른 서버
- 다음: 어드민 서버(새 저장소)를 Orchestrator 방식으로 진행

---

## 배운 것 / 느낀 것 (WIL)

- **"쓰지 않는 기능이라 안전하다"는 판단은 위험하다.** Server Actions를 안 써도 App Router를 쓰는 것만으로 React2Shell 영향을 받았다. 버전·지원 기간은 기능이 아니라 **릴리스 라인 기준**으로 관리해야 한다.
- **보안 요구사항은 '정책'보다 '테스트'로 남길 때 지켜진다.** 예: FE의 보안 하한선 테스트(버전·오염 패키지 검사), BE의 교차 계정 테스트(모든 엔드포인트 × 다른 사용자).
- **기능을 단순하게 바꾸면 보안 문제가 같이 사라지기도 한다.** 계정 합치기를 없애고 임시 가입을 두니, 계정 선점 공격 대응이 따로 필요 없어졌다.
- **에이전트에게 맡길수록 '결정 기록'이 중요하다.** D-번호 결정 기록 + 백로그 인수 조건 + 대시보드 메시지가 있으니, 창이 바뀌거나 대화가 끊겨도 바로 이어서 일할 수 있었다.
- **Orchestrator 방식**은 빈틈을 개발 전에 더 많이 찾는 대신, git/worktree 같은 운영 문제가 새로 생긴다. 계속할지는 비교표를 보고 정할 예정.

## 다음 할 일

- [ ] FE US-31(Next 16) 화면 검수 (1440/820/390)
- [ ] BE US-32 시작 (사용자별 데이터·admin 계정 이전), 결정 내용을 DB·API 문서·openapi로 옮기기
- [ ] Orchestrator: 어드민 서버 저장소 구성 제안
- [ ] 서버 대수(한 대/두 대)·도메인 결정은 서버 고를 때
- [ ] Orchestrator 방식 계속할지 결정 (계속 / 일부만 / 원래대로)
