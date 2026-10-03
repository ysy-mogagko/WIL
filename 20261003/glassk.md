# 20261003

## AI

- AX 구축 및 개발 활용 경험 쌓으면서 AI 활용 능력을 증명할 만한 것을 추가로 마련해두면 좋을 것 같다
- [AI 엔지니어링](https://product.kyobobook.co.kr/detail/S000217939673?LINK=NVB&NaPm=ct%3Dmurtk020%7Cci%3D9894eb3854fda0551849376588861c7fe6901872%7Ctr%3Dboksl1%7Csn%3D5342564%7Chk%3D762706b601f6d2f7c150719a0a8a739a1f34a7cb)
- Oracle Agentic AI Foundations Associate
- [AI 자격증 정리 자료](https://fieldby.notion.site/AI-8-3ecd730b395381c2aa2cc1c7a18c6a8e#3ecd730b395381408b9be1d265a46e9f)

## Sentry & GA4 활용

- 회사 프로젝트에서 직접 세팅하게 되었는데 잘 활용해보고 싶다
- 측정하고자 하는 유저 퍼널 및 이벤트 정의, PO/마케팅과 논의 필요
- 에러/개선필요 상태 레벨 정의

## LCP & INP 개선

### LCP 개선 방안: 리소스를 더 빨리 받아서 더 빨리 보여주기

- LCP 이미지 우선 로딩
  - preload, fetchpriority="high" 적용
  - LCP 이미지에 loading="lazy" 사용하지 않기
  - Next.js에서는 Image의 priority 활용
- 이미지 최적화
  - WebP, AVIF 등 차세대 포맷 사용
  - 실제 노출 크기에 맞게 리사이징
  - srcset, sizes로 디바이스별 적절한 이미지 제공
- 서버 응답 시간 단축
  - TTFB 개선
  - SSR 처리 시간, API, DB 병목 확인
  - CDN 및 캐싱 활용
  - 불필요한 서버 처리 최소화
- 렌더링 차단 리소스 최소화
  - 불필요한 CSS 제거
  - JavaScript에 defer 적용
  - Critical CSS 최소화
  - 폰트 preload 적용
  - 서드파티 스크립트 로딩 지연
- LCP 요소를 초기 HTML에 포함
  - useEffect 이후 렌더링되는 구조 피하기
  - 가능하면 SSR, Server Component 등 활용
  - LCP 리소스 요청 시작 시점을 최대한 앞당기기

### INP 개선 방안: 메인 스레드를 덜 막아서 더 빨리 반응하기

- Long Task 줄이기
  - 50ms 이상 메인 스레드를 점유하는 작업 확인
  - 큰 작업을 여러 작은 작업으로 분할
  - 필요하면 requestAnimationFrame 등을 활용해 작업 양보
- 이벤트 핸들러 경량화
  - 클릭, 입력 이벤트 내부에서 무거운 계산 피하기
  - 즉시 필요한 UI 변경을 먼저 처리
  - 부가적인 연산은 이후로 지연
- React 렌더링 비용 줄이기
  - 불필요한 state 및 재렌더링 최소화
  - Context 변경 범위 축소
  - React.memo, useMemo, useCallback은 병목 확인 후 적용
  - React Profiler로 실제 렌더링 비용 확인
- 업데이트 우선순위 분리
  - startTransition으로 긴급한 UI 업데이트와 비긴급 업데이트 분리
  - 사용자 입력 반응은 먼저 처리
- 긴 리스트 최적화
  - 화면에 보이지 않는 DOM까지 모두 렌더링하지 않기
  - Virtualization 적용
  - react-window, TanStack Virtual 등 활용
- 무거운 연산 분리
  - CPU 연산이 큰 경우 Web Worker 활용
  - 메인 스레드에서 UI와 사용자 입력 처리에 집중
