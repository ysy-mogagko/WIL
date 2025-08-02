# Branch Prefix

Pull Request 자동 생성을 위해 브랜치 접두어(prefix)를 적용했습니다.

다양한 접두어로 PR을 쉽게 만들어보세요.

prefix를 적용한 branch에서 push를 할 경우 PR이 자동생성됩니다.

## 기본 설정 - 'study/'

'study/' prefix를 사용하면 pr이 컨벤션에 맞게 제목과 내용이 자동적용됩니다.

## 공부 내용에 따른 label 자동적용 prefix 들

pr 에서 공부 내용에 따라 labels 를 선택할 수 있습니다.

브랜치 명에 prefix를 적용하면 브랜치에 따른 label이 자동적용됩니다.

현재 적용된 label은 아래와 같습니다.

![labels](/Guide/image/branch-prefix/labels.png)

|label명|prefix|
|---|---|
|Computer Science|cs/|
|DB/SQL|db/|
|알고리즘/자료구조|algo/|
|업무|work/|
|프로젝트|proj/
|자격증|lic/|


### prefix 적용 예시 'proj/'

prefix를 적용한 브랜치를 push를 할경우 아래처럼 label이 적용된 pr이 자동생성됩니다.

![proj-branch-pr](/Guide/image/branch-prefix/proj-branch-pr.png)

