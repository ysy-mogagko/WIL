# 20251101

## 사이드 프로젝트 제작

### FE

> 코드 수정 : https://github.com/awesomedesk/j-planner-fe/pull/22

- 캘린더 네비게이션 개선
  - 중복 네비게이션 로직 제거를 위한 커스텀 훅 제작
    - `useCalendarNavigation`: 캘린더 전용 네비게이션 훅
    - `useNavigation`: 범용 재사용 가능한 네비게이션 유틸리티
  - Calendar 및 CalendarMonthly 컴포넌트에서 중복 라우터 코드 제거
  - "show +N" 링크를 클릭 가능하게 개선하여 일일 뷰로 직접 이동 가능
- 상수 구조 개선
  - `CALENDAR_CONSTANTS`를 `WEEKLY_DAILY_VIEW_CONSTANTS`로 명확하게 변경
  - 월별 뷰 전용 `MONTHLY_VIEW_CONSTANTS` 분리 생성
  - `getSchedulePosition()` 함수 개선으로 다일 이벤트 지원
  - 미사용 색상 관련 함수 제거
- 코딩 표준 문서화 (CODING_STANDARDS.md)
  - 네이밍 규칙: 파일, 컴포넌트, 함수, 변수에 대한 역할 기반 명명
  - Boolean 네이밍: is/has/should/can 접두사 패턴
  - 이벤트 핸들러: handle/on 명명 규칙
  - Git 커밋: 기능 기반 전략 및 한국어 메시지 표준
  - Pull Request: 제목 형식, 구조, 리뷰 체크리스트
- 테스트 데이터 확장
  - 야간 일정, 다일 이벤트, 하루 내 여러 이벤트 케이스 추가
