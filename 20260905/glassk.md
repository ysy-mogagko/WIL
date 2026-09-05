# 20260905

## Next.js & React 점진적 마이그레이션 계획 검토

- Pages Router 유지 가능한 범위에서 우선 진행
- App Router 전환에 따른 폴더 구조 변경 및 불필요한 파일 삭제, 빌드 속도 및 결과물 크기 개선
- 백엔드 API 성능 개선 전후 수치 비교
- TypeScript 버전 업 고려
- Webpack -> Turbopack
- 기존 미비한 테스트 환경 -> 마이그레이션 과정에서 기존 기능이 깨지는 것을 막기 위해 RTL 코드 작성, 코어 기능 리스트업

## Nuxt.js 플랫폼 성능 개선 및 테스트 환경 구축

- 성능 개선
  - 번들링 속도 및 결과 측정 비교
  - 개선 과정에서 발생한 트레이드오프 분석
  - 불필요한 파일 제거(기존 프로젝트 clone 단계에서 미사용 파일 및 이미지 다수 남아있었음)
- 테스트
  - 결제 모듈 mocking
  - 모달 UI 중첩 레이어 추상화
  - Unit/E2E Testing
  - 컨벤션 제안

## 사내 기술 스터디 세션 준비

- Next.js
- React

## 인앱 웹뷰 성능 개선 관련 아티클

- [웹뷰 다음의 레일을 깔다: 당근이 Lynx를 선택한 이유](https://medium.com/daangn/%EC%9B%B9%EB%B7%B0-%EB%8B%A4%EC%9D%8C%EC%9D%98-%EB%A0%88%EC%9D%BC%EC%9D%84-%EA%B9%94%EB%8B%A4-%EB%8B%B9%EA%B7%BC%EC%9D%B4-lynx%EB%A5%BC-%EC%84%A0%ED%83%9D%ED%95%9C-%EC%9D%B4%EC%9C%A0-34dd50abbfb7)
- 추가로 다른 자료도 찾아보고 초기 로딩 속도 개선 관련 기술 조사. 스터디 세션 때 공유 준비
