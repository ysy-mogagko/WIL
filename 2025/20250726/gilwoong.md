오늘부터 하나씩 강의를 듣고 정리하는 시간을 가져보려 한다. 그동안 나는 `코딩은 동작만 하면 되는거 아니냐?`라는 마인드의 개발자였으나 이것이 유지보수에 하자가 크게 생긴다는 것을 느낀 이후로 클린 코드에 관심을 가지기 시작했다.

## 1. 실무에서 클린코드의 의의

회사에서 흔히 말하는 레거시 코드라는 것이 무슨 코드일까? 여러 특징들이 있겠지만 요약하자면

1. 흐름파악이 어렵고
2. 도메인 맥락 표현이 안되어
3. 동료들에게 물어봐야 알 수 있는 코드

가 나쁜 코드가 된다. 이런 코드들은 읽기 어려울 뿐더러 고칠 때에도 손이 많이 가고 기능을 추가할 때 더욱더 번잡스러워진다.

## 2. 리팩토링 예시

예를 들면 이런 코드가 있다고 가정하자.

```jsx
function QuestionPage() {
  const [popupOpened, setPopupOpened] = useState(false);
  async function handleQuestionSubmit() {
    const 연결전문가 = await 연결전문가_받아오기();
    if (연결전문가 !== null) {
      setPopupOpened(true);
    } else {
      const 약관동의 = await 약관동의_받아오기();
      if(!약관동의) {
        await 약관동의_팝업열기();
      }
      await 질문전송(questionValue);
      alert('질문이 등록되었어요.');
    }
  }

  async function handleMyExpertQuestionSubmit() {
    await 연결전문가_질문전송(questionValue, 연결전문가.id);
    alert (`${연결전문가.name}에게 질문이 등록되었어요!`);
  }
  return (
    <main>
      <form>
        <Button onClick={handleQuestionSubmit}>질문하기</Button>
      </form>
      {popupOpened && (
        <연결전문가팝업 onSubmit={handleMyExpertQuestionSubmit} />
      )}
    </main>
  );
```

자연스러운 코드가 되어있는것처럼 보이지만 사실 난잡한 코드라는 말을 듣고 속으로 뜨끔했다. 난 맨날 이런 코드를 써서 이게 나쁘다는 생각 자체를 하지 못했다.
이 코드가 난잡하고 나쁜 코드가 된 이유는 다음과 같다.

1. 하나의 목적인 코드가 흩뿌러져있다.
   연결중인 전문가 팝업에 관련한 내용이 상,중,하에 모두 배치되어 있어 전체적인 흐름을 읽기 위해서는 스크롤을 왔다갔다 해야 한다.

2. 하나의 함수가 여러 가지 일을 하고 있다.
   handleQuestionSubmit함수의 경우 연결전문가도 받아오고 약관 동의도 확인하고 질문 전송까지 3가지 일을 혼자 도맡아 하고 있다.

3. 함수 세부 구현 단계가 제각각이다.
   handleMyExpertQuestionSubmit의 경우 질문전송 하나만 담당하지만 handleQuestionSubmit함수의 경우 이름은 첫 번째 함수와 비슷하지만 하는 일이 훨씬 다양하고 많아서 함수간의 계층 내용이 맞지 않다.

이처럼 동작은 하지만 전체 그림으로 보면 엉망인 케이스들이 종종 보인다. 특히 나의 경우 안일한 마인드로 코딩을 했기에 조금 많이 찔렸다. 이제 강연자가 말씀해주시는 리펙토링 과정을 흝어보자.

### 함수 세부 구현 단계 통일

통일감을 주기 위하여 1.api를 전송하고 2. alert를 띄운다. 라는 단순한 구조로 재정의했다.

```js
async function handleNewExpertQuestionSubmit() {
  await 질문전송(questionValue);
  alert("질문이 등록되었어요");
}

async function handleMyExpertQuestionSubnit() {
  await 연결전문가_질문전송(questionValue, 연결전문가.id);
  alert(`${연결전문가.name}에게 질문이 등록되었어요`);
}
```

### 하나의 목적인 코드는 뭉쳐두기

기존에는 팝업을 여는 버튼과 팝업코드가 동떨어져 있는데 이를 PopupTriggerButton으로 뭉쳐두었다.

```jsx
{연결전문가.connected ? (
  <PopupTriggerButton
    popup={(
      <연결전문가팝업
        onButtonSubmit={handleMyExpertQuestionSubmit} />
      )}
  >
   	질문하기 </PopupTriggerButton>
  ) : (
```

팝업에 대한 내용은 props로 담아서 한번에 해결할 수 있도록 뭉친 모습을 알 수 있다.

### 함수가 한 가지 일만 하도록 쪼개기

약관동의는 약관동의만 할 수 있도록! 함수를 쪼개서 필요한 시점에 부르도록 설계했다.

```jsx
async function openPopupToNotAgreeUsers() {
  const 약관동의 = await 약관동의_받아오기();
  if(!약관동의) {
    await 약관동의_팝업열기();
  }
}

return (
<Button onClick(async () => {
    await openPopupToNotAgreeUsers();
    await handleMyExpertQuestionSubmit();
    })
  >
  질문하기
  </Button>
)
```

최종적으로 이 셋의 문제를 고치는 방식은 다음과 같다.![](https://velog.velcdn.com/images/lkwer/post/a516df63-43b2-4317-a189-4192edb50ad4/image.png)

### 핵심 데이터는 밖에서 전달, 나머지는 뭉친다

그 다음 스텝이다. 핵심 내용은 읽을 때 중요하니 표시하고 핵심적이지 않은 내용은 뭉쳐두어 전체 흐름을 파악하기 쉽게 설계한다.

```jsx
function QuestionPage() {
  const [openPopup] = usePopup();
  async function handleClick() {
    const confirmed = await openPopup({
      title: '보험 질문하기',
      comment: <div>전문가가 설명해드려요</div>,
    });
    if(confirmed) {
      await submitQuestion();
    }
  }

  async function submitQuestion(연결전문가) {
    await 질문전송(연결전문가.id);
    alert('질문을 전송했습니다');
  }
  return <button onClick={handleClick}>질문하기</button>;
```

이런 방식으로 핵심 데이터만 전달받고 세부 구현은 뭉친 뒤 숨겨 두는 개발 스타일을 `선언적 프로그래밍`이라고 부른다.
![](https://velog.velcdn.com/images/lkwer/post/d2d4a0a2-e6cc-4ba9-8855-cd339e3b9952/image.png)
선언적 프로그래밍은 한눈에 흐름을 파악할 수 있고 무엇을 바꾸어 재사용을 할 수 있는지 볼 수 있다는 점에서 장점이 있다.

## 3. 추상화 활용하기

```jsx
//0단계
<button onClick={showConfirm}>
  전송</button>
{isShowConfirm && (
  <confirm onCLick={() => {showMessage('성공')}}/>
  )}

//1단계
<ConfirmButton
  onConfirm={() => {showMessage('성공')}}
  >
  전송
</ConfirmButton>


//2단계
<ConfirmButton message='성공'>전송</ConfirmButton>

//3단계
<ConfirmButton />
```

이처럼 추상화 단계는 여러 단계가 있고 정답은 없다. 하지만 중요한 점은 추상화의 수준이 섞여 있다면 코드 파악이 어렵다는 점이다. 위의 단계를 확인해보고 추상화 단계를 맞추는 연습을 해봐야 할 것 같다.
