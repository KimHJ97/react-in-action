# useEffect

 - [useEffect 공식 문서](https://ko.react.dev/reference/react/useEffect)

## useEffect 기본 개념

useEffect는 리액트 컴포넌트가 렌더링될 때마다 특정 작업(Side effect)를 실행할 수 있도록 하는 리액트 Hook이다. 주로 컴포넌트가 렌더링된 후나 특정 상태 또는 props가 변경될 떄 실행되는 동작을 정의할 수 있다.
 - 클래스형 컴포넌트의 생명 주기 메서드인 componenetDidMount, componentDidUpdate, componentWillUnmount를 대체한다.
```javascript
useEffect(setup, dependencies?)
```

### 주요 사용 사례

 - __데이터 가져오기__: API에서 데이터를 가져와 상태를 업데이트할 때
 - __구독 설정 및 해제__: 웹소켓, 이벤트 리스너 구독 설정 및 해제
 - __타이머 및 인터벌 설정__: setInterval이나 setTimeout을 통해 주기적인 작업 처리
 - __DOM 조작__: 외부 라이브러리를 통한 직접적인 DOM 조작

## useEffect 사용 방법

 - 컴포넌트가 mount 됐을 때 (처음 렌더링 후 실행)
    - deps 위치에 빈 배열을 넣으면 처음 렌더링 후에 1번만 실행된다.
```javascript
useEffect(() => {
  console.log("컴포넌트가 마운트되었습니다.");
}, []);
```

 - 컴포넌트가 update 됐을 때
    - 특정 값이 업데이트 될 때 실행하고 싶은 경우 deps 위치의 배열 안에 검사하고 싶은 값을 넣어준다.
    - 처음 렌더링 시에 실행되고, 의존성 배열안에 값이 변경될 때마다 실행된다.
```javascript
useEffect(() => {
  console.log("count 값이 변경되었습니다.");
}, [count]);
```

 - 컴포넌트가 unmount 됐을 때
    - useEffect 내부에서 반환하는 함수는 cleanup 함수로, 주로 컴포넌트가 언마운트되거나 이펙트가 다시 실행되기 직전에 호출된다.
```javascript
useEffect(() => {
  const timer = setInterval(() => {
    console.log("타이머 작동 중");
  }, 1000);

  return () => {
    clearInterval(timer); // 컴포넌트가 언마운트되거나 의존성이 변경될 때 타이머 정리
  };
}, []);
```

 - useEffect안에 상태 변수 사용하기
    - useEffect에서 사용하는 모든 반응형값은 의존성으로 선언되어야 한다.
```javascript
// ✔ 반응형 값 의존성에 선언
function ChatRoom({ roomId }) { // 이것은 반응형 값입니다
  const [serverUrl, setServerUrl] = useState('https://localhost:1234'); // 이것도 반응형 값입니다

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // 이 Effect는 이 반응형 값들을 읽습니다
    connection.connect();
    return () => connection.disconnect();
  }, [serverUrl, roomId]); // ✅ 그래서 이 값들을 Effect의 의존성으로 지정해야 합니다
  // ...
}

// ❌ 반응형 값을 선언안할 경우 린트 에러
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');
  
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // 🔴 React Hook useEffect has missing dependencies: 'roomId' and 'serverUrl'
  // ...
}

// ✔ 의존성 제거를 위해 반응형값을 상수로 변경
const serverUrl = 'https://localhost:1234'; // 더 이상 반응형 값이 아님

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ 모든 의존성이 선언됨
  // ...
}
```

## useEffect 활용 예시

 - 데이터 가져오기 - API 호출
    - 렌더링 시에 API 호출 후 data 상태에 json 데이터를 저장한다.
    - API 호출 데이터를 화면에 출력한다.
```javascript
import React, { useState, useEffect } from 'react';

function DataFetchingComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    // 1. API 호출
    fetch('https://jsonplaceholder.typicode.com/posts/1')
      .then((response) => response.json())
      .then((json) => setData(json));

    // 2. API 호출
    const json = await fetch('https://jsonplaceholder.typicode.com/posts/1')
        .then((response) => response.json());
    setData(json);
  }, []); // 빈 배열이므로 처음 렌더링 시에만 실행

  return (
    <div>
      {data ? (
        <div>
          <h2>{data.title}</h2>
          <p>{data.body}</p>
        </div>
      ) : (
        <p>Loading...</p>
      )}
    </div>
  );
}

export default DataFetchingComponent;
```

 - 반응형 값 개선하기 (1초마다 카운트 증가)
```javascript
// ❌ count가 반응형 값으로 반드시 의존성 배열에 추가해야한다.
// count가 변경되는 것은 Effect가 정리된 후 다시 설정되는 것을 야기한다.
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setCount(count + 1); // 초마다 카운터를 증가시키고 싶습니다...
    }, 1000)
    return () => clearInterval(intervalId);
  }, [count]); // 🚩 ... 하지만 'count'를 의존성으로 명시하면 항상 인터벌이 초기화됩니다.
  // ...
}

// ✔ 의존성 베열에 count를 제거한다.
// setCount에서 count 값을 직접 사용하지 않고, 수정 메서드를 이용해서 count 값을 이용한다.
// useEffect에서 상태 변수를 사용하지 않고 있어 의존성 배열에 넣지 않아도 된다.
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setCount(c => c + 1); // ✅ State 업데이터를 전달
    }, 1000);
    return () => clearInterval(intervalId);
  }, []); // ✅ 이제 count는 의존성이 아닙니다

  return <h1>{count}</h1>;
}
```
