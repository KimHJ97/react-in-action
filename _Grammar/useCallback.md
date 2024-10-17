# useCallback

 - [useCallback 공식 문서](https://ko.react.dev/reference/react/useCallback)
 - [useCallback을 사용하여 함수 재사용하기](https://react.vlpt.us/basic/18-useCallback.html)
 - [useCallback 사용 방법](https://velog.io/@hjthgus777/React-%EB%8B%A4%EC%8B%9C-%ED%95%9C%EB%B2%88-useCallback%EC%9D%84-%ED%8C%8C%ED%97%A4%EC%B3%90%EB%B3%B4%EC%9E%90)

## useCallback 기본 개념

useCallback은 React의 훅 중 하나로, 주로 메모이제이션을 통해 함수의 재생성을 방지하기 위해 사용된다. 컴포넌트가 __렌더링될 때마다 함수가 다시 생성되는 것을 방지__ 하여 불필요한 렌더링과 성능 저하를 피할 수 있다.  
 - 특정 함수를 새로 만들지 않고 재사용하고 싶을 때 사용한다.

### useCallback 사용 사례

 - 자식 컴포넌트에 콜백함수(ex: 이벤트 핸들러)를 prop으로 전달할 떄 자식 컴포넌트가 불필요하게 재렌더링되는 것을 방지할 수 있다.
 - 콜백 함수를 의존성으로 사용하는 useEffect와 같은 훅에서 의도하지 않은 리렌더링을 피하고 싶을 때

## useCallback 사용 방법

useCallback 함수는 첫 번째 인자로 콜백 함수, 두 번째 인자로 의존성 배열을 받는다. 이 훅은 의존성 배열에 있는 값들이 변경될 때에만 새로운 콜백 함수를 생성하고, 그렇지 않으면 이전에 생성된 콜백을 반환한다.

```javascript
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

## useCallback 예제

 - 기본 예제
    - increment 함수는 컴포넌트가 리렌더링될 때마다 재생성되지 않고, 메모이제이션된 함수가 유지된다. 이를 통해 불필요한 함수 재생성을 방지하여 성능을 최적화할 수 있다.
    - 기존에 count가 변경될 때마다 재렌더링으로 인해 함수도 다시 재생성되었다. 하지만, useCallback으로 메모리에서 가져와 재사용한다.
    - __아래 버튼을 클릭하면 숫자가 0만 출력한다. 메모이제이션된 함수 안에 있는 number에는 메모이제이션했을 당시 number의 값이 들어간다. 떄문에, 메모이제이션 함수안에서 상태 변수를 직접 사용할 때 의존성 배열에 해당 상태 변수도 추가해주는 것이 좋다.__
```javascript
import React, { useState, useCallback } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  // 함수가 매번 렌더링 시 재생성되는 것을 방지
  const increment = useCallback(() => {
    console.log('count: ', count); // 0만을 출력
    setCount((prevCount) => prevCount + 1);
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

 - 자식 컴포넌트에 이벤트 핸들러 전달하기
    - 만약, handleClick 함수의 useCallback을 사용하지 않으면 Parent가 리렌더링될 때마다 handleClick 함수가 새로 생성되고, 자식인 Child 컴포넌트도 리렌더링된다.
```javascript
import React, { useState, useCallback } from 'react';

function Child({ onClick }) {
  console.log('Child component rendered');

  return (
    <div>
      <button onClick={onClick}>Increment Parent Count</button>
    </div>
  );
}

function Parent() {
  const [count, setCount] = useState(0);

  // useCallback으로 함수 메모이제이션
  const handleClick = useCallback(() => {
    setCount((prevCount) => prevCount + 1);
  }, []);

  return (
    <div>
      <p>Parent Count: {count}</p>
      <Child onClick={handleClick} />
    </div>
  );
}

export default Parent;
```

 - 배열에 항목을 추가하는 경우
    - addTodo 함수는 input 값이 변경될 때에만 새로 생성된다.
    - setTodos() 함수에서 업데이트시에 input 상태 변수를 직접 사용하기 떄문에, 의존성 배열에 input 상태 변수를 추가해주어야 한다.
```javascript
import React, { useState, useCallback } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  // 메모이제이션된 addTodo 함수
  const addTodo = useCallback(() => {
    setTodos((prevTodos) => [...prevTodos, input]);
    setInput(''); // 입력 필드 초기화
  }, [input]);

  return (
    <div>
      <input
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <button onClick={addTodo}>Add Todo</button>
      <ul>
        {todos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
    </div>
  );
}

export default TodoList;
```

 - useEffect와 함께 사용하기
    - fetchData 함수는 useCallback으로 메모이제이션되어 컴포넌트가 처음 마운트될 때 한 번만 생성된다.
    - useEffect 내에서 fetchData를 호출할 때 fetchData를 의존성으로 추가한다. 이 경우 useCallback을 사용하지 않으면 매 렌더링마다 새로운 함수가 생성되어 useEffect가 의도하지 않게 반복 실행될 수 있다.
```javascript
import React, { useState, useEffect, useCallback } from 'react';

function FetchDataComponent() {
  const [data, setData] = useState(null);
  const [count, setCount] = useState(0);

  // 데이터를 fetch하는 함수
  const fetchData = useCallback(async () => {
    const response = await fetch('https://api.example.com/data');
    const result = await response.json();
    setData(result);
  }, []); // 빈 배열이므로 이 함수는 한 번만 생성됨

  // 컴포넌트 마운트 시 fetchData 실행
  useEffect(() => {
    fetchData();
  }, [fetchData]); // 의존성 배열에 fetchData 추가

  return (
    <div>
      <button onClick={() => setCount((prev) => prev + 1)}>
        Re-render Component ({count})
      </button>
      {data && <div>{JSON.stringify(data)}</div>}
    </div>
  );
}

export default FetchDataComponent;
```
