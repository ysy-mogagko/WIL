# 20260919

## 코딩테스트 (LeetCode) 문제 풀이 정리

> 커밋: https://github.com/jungeu1509/jungeu1509.github.io/commit/f2ab904e6258449444df460a9045e218fe142661

### [Two Sum](https://jungeu1509.github.io/coding-test/leetcode/two-sum/)

- 브루트포스(이중 반복문, O(n²))와 HashMap을 이용한 One-pass 풀이(O(n)) 두 가지로 정리
- HashMap 풀이 핵심: `target - nums[i]`(보수, complement)가 이미 map에 있는지 확인하면서 한 번의 순회로 해결
- 시간-공간 트레이드오프 비교: 브루트포스는 O(1) 공간에 O(n²) 시간, HashMap은 O(n) 공간에 O(n) 시간

### [Add Two Numbers](https://jungeu1509.github.io/coding-test/leetcode/add-two-numbers/)

- 역순으로 저장된 연결 리스트 두 개를 더하는 문제, 더미 노드(dummy node)를 활용해 결과 리스트 구성
- 올림(carry) 처리 시 자주 하는 실수 정리
  - 루프 조건에 `!carryFlag`를 써서 무한 루프에 빠지는 경우 (`carryFlag`가 false일 때 계속 반복됨)
  - 루프 조건은 `l1`, `l2`를 보면서 실제 업데이트는 `now1`, `now2`를 하는 변수 불일치로 인한 무한 루프
  - carry 값을 매 루프마다 초기화해서 이전 올림 값이 소실되는 문제
- boolean 플래그 대신 정수형 `carry`를 사용해 `sum / 10`(올림), `sum % 10`(현재 자리)으로 계산하는 더 간결한 풀이로 개선
