# 20250927

## 사이드 프로젝트 제작

> 코드 수정 : https://github.com/awesomedesk/j-planner-fe/commit/59f6bde42f9764d80d05894ac45e34dce0f79be2

- SSR/CSR 하이드레이션 불일치 문제 해결
  - 클라이언트 전용 컴포넌트를 별도 파일로 분리 (*Client.tsx)
  - dynamic import와 loading fallback 추가
  - ClientOnly wrapper 컴포넌트 도입

- Redux 통합 및 테마 시스템 개선
  - utils/hooks/useTheme.ts 및 utils/theme/themeUtils.ts 제거
  - 모든 컴포넌트가 Redux의 getThemeState 직접 사용
  - ReduxApp 컴포넌트로 Redux 초기화 로직 분리

- 레이아웃 및 스타일링 개선
  - app/layout.tsx를 클라이언트 컴포넌트로 전환
  - globals.css 정리 (사용하지 않는 CSS 변수 제거)
  - 홈페이지 테마 적용 및 디자인 개선

- 코드 품질 개선
  - TypeScript path alias 활용 증가
  - 불필요한 console.log 제거
  - 함수명 수정 (calender → exampleFunction)
