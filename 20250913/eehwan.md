## 사이드 프로젝트 - 인증 기능 개선

- JWT 토큰에 refresh_token 포함: 현재 기기 식별을 위해 JWT payload에 refresh_token 추가
- 로그아웃 로직 개선: JWT에서 refresh_token 추출해 정확한 현재 기기만 로그아웃 구현
- User-Agent 파싱: 사용자 입력 없이 브라우저 정보로 자동 생성
