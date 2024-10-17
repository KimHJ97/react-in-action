# useState

 - [useState 공식 문서](https://ko.react.dev/reference/react/useState)
 - [useStgate 사용법](https://react.vlpt.us/basic/07-useState.html)

useState는 컴포넌트의 상태를 관리할 때 사용된다. 상태가 변할 때마다 컴포넌트를 다시 렌더링하고 싶을 떄 주로 사용된다. 상태를 변경하면 해당 컴포넌트가 다시 렌더링된다.  

## useState 기본 개념

useState는 React에서 컴포넌트의 상태 관리를 위해 사용하는 훅으로 함수형 컴포넌트에서 상태를 정의하고, 그 상태를 변경하는 방법을 제공해준다.  

 - __사용 방법__
    - useState 함수에 초기값을 인자로 넣어주면 state(상태)와 setState 두 가지 요소를 배열 형태로 반환해준다.
    - state: 현재 상태 값
    - setState: 상태를 업데이트하는 함수
    - initialState: 초기 상태 값
```javascript
const [state, setState] = useState(initialState);
```

### 주요 특징

 - __상태 유지__: useState는 컴포넌트가 다시 렌더링되더라도 상태를 유지한다.
 - __비동기적 업데이트__: setState로 상태를 변경할 때는 비동기적으로 처리된다. 여러 setState 호출이 있을 경우, React는 이를 하나로 모아서 한 번에 처리할 수 있다.
 - __기본 초기값 설정__: useState에 전달하는 초기값은 컴포넌트가 처음 렌더링될 때만 사용되며, 이후 상태는 해당 값을 기준으로 변경된다.

## useState 예시

 - 여러가지 종류의 상태 관리
```javascript
// 1. 값 상태 관리
// 1-1. 값 상태 생성
const [name, setName] = useState("홍길동")

// 1-2. 값 상태 변경
setName("김철수")

// 2. 객체 상태 관리
// 2-1. 객체 상태 생성
const [person, setPerson] = useState({
    name: '홍길동',
    age: 20
})

// 2-2. 객체 상태 일부 변경
setPerson((prevPerson) => ({
    ...prevPerson,
    age: 30
}))

// 3. 배열 상태 관리
// 3-1. 배열 상태 생성
const [fruits, setFruits] = useState([]);

// 3-2. 배열 추가: 이전 배열을 복사하고 새로운 항목 추가
setFruits([...fruits, newFruit]);
```

 - 카운터 예제
```javascript
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You pressed me {count} times
    </button>
  );
}
```

 - Input 상태 관리
```javascript
import { useState } from 'react';

export default function Form() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => setAge(age + 1)}>
        Increment age
      </button>
      <p>Hello, {name}. You are {age}.</p>
    </>
  );
}
```

 - 체크박스 상태 관리
```javascript
import { useState } from 'react';

export default function MyCheckbox() {
  const [liked, setLiked] = useState(true);

  function handleChange(e) {
    setLiked(e.target.checked);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={liked}
          onChange={handleChange}
        />
        I liked this
      </label>
      <p>You {liked ? 'liked' : 'did not like'} this.</p>
    </>
  );
}
```

### useState 주의 사항

 - 이전 state를 기반으로 state 업데이트하기
```javascript
// 아래의 결과는 43이 된다.
// set 함수를 호출해도 이미 실행 중인 코드에서 age 상태 변수가 업데이트되지 않았다.
function handleClick() {
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
}

// 이것을 해결하기 위해 업데이트 함수를 전달할 수 있다.
function handleClick() {
  setAge(a => a + 1); // setAge(42 => 43)
  setAge(a => a + 1); // setAge(43 => 44)
  setAge(a => a + 1); // setAge(44 => 45)
}
```
