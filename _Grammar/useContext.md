# useContext

 - [useContext 공식 문서](https://ko.react.dev/reference/react/useContext)
 - [다른 사람들이 안 알려주는 리액트에서 Context API 잘 쓰는 방법](https://velog.io/@velopert/react-context-tutorial)
 - [React Context 효율적으로 사용하는 법](https://kentcdodds.com/blog/how-to-use-react-context-effectively)
 - [초보자를 위한 리액트 Context 가이드](https://www.freecodecamp.org/korean/news/cobojareul-wihan-riaegteu-context-wanbyeog-gaideu-2021/)

## useContext 기본 개념

useContext는 React에서 Context API를 사용하여 전역 상태를 쉽게 관리하고, 컴포넌트 계층 구조에서 __prop drilling__(불필요한 단계별 props 전달)을 방지하기 위해 사용하는 Hook이다. useContext를 사용하면, 부모-자식 관계에 상관없이 필요한 컴포넌트에서 직접 데이터를 불러와 사용할 수 있다.  
 - Provider: Context의 값을 제공하는 컴포넌트
 - Consumer: Context의 값을 받아 사용하는 컴포넌트

### useContext 주요 사용 사례

 - 테마 데이터(다크 모드 혹은 라이트 모드)
 - 사용자 데이터(현재 인증된 사용자)
 - 로케일 데이터(언어 혹은 지역)

## useContext 사용 방법

먼저 Context 객체를 생성해야 합니다. React.createContext()를 통해 Context를 생성합니다. Provider는 트리 구조에서 Context 값을 하위 컴포넌트들에게 제공합니다. 하위 컴포넌트에서는 useContext를 사용하여 Context 값을 직접적으로 접근할 수 있습니다.  

 - Context 생성(createContext(initialValue))
    - createContext를 통해 Context 객체를 생성합니다.
```javascript
import React, { createContext, useContext, useState } from 'react';

// Context 생성
const MyContext = createContext(null);
```

 - Provider 설정(Context.Provider)
    - MyContext.Provider를 사용하여 Context 값을 하위 컴포넌트들에게 제공한다.
```javascript
function MyProvider({ children }) {
  const [value, setValue] = useState('Hello, Context!');

  return (
    <MyContext.Provider value={{ value, setValue }}>
      {children}
    </MyContext.Provider>
  );
}
```

 - Context 값 사용(useContext(Context))
    - 하위 컴포넌트에서 useContext를 사용하여 값을 가져올 수 있다.
```javascript
function MyComponent() {
  const { value, setValue } = useContext(MyContext);

  return (
    <div>
      <p>Context 값: {value}</p>
      <button onClick={() => setValue('Updated Context Value!')}>
        값 업데이트
      </button>
    </div>
  );
}
```

## useContext 활용 예제

### Context에서 상태 관리가 필요한 경우

```javascript
import { createContext, useContext, useMemo, useState } from 'react';

// 1. Context 생성
const CounterContext = createContext();

// 2. CounterProvider라는 커스텀 Provider 생성
function CounterProvider({ children }) {
  // Context에 저장 및 사용할 상태 정의
  const [counter, setCounter] = useState(1);

  // Context의 값을 변경할 수 있는 함수 정의
  const actions = useMemo(
    () => ({
      increase() {
        setCounter((prev) => prev + 1);
      },
      decrease() {
        setCounter((prev) => prev - 1);
      }
    }),
    []
  );

  const value = useMemo(() => [counter, actions], [counter, actions]);

  return (
    <CounterContext.Provider value={value}>{children}</CounterContext.Provider>
  );
}

// 3. Context를 사용할 수 있는 커스텀 훅 생성
function useCounter() {
  const value = useContext(CounterContext);
  if (value === undefined) {
    throw new Error('useCounterState should be used within CounterProvider');
  }
  return value;
}

// 4. Provider 설정
function App() {
  return (
    <CounterProvider>
      <div>
        <Value />
        <Buttons />
      </div>
    </CounterProvider>
  );
}

// 5-1. Value 컴포넌트에서 Context의 값 조회
function Value() {
  const [counter] = useCounter();
  return <h1>{counter}</h1>;
}

// 5-2. Buttons 컴포넌트에서 Context의 값 변경
function Buttons() {
  const [, actions] = useCounter();

  return (
    <div>
      <button onClick={actions.increase}>+</button>
      <button onClick={actions.decrease}>-</button>
    </div>
  );
}

export default App;
```

### 값과 업데이트 함수를 2개의 Context로 분리하기

만약, Context에서 관리하는 상태가 빈번하게 업데이트되지 않는 다면 위의 방법도 충분히 괜찮다. 하지만, 만약에 상태가 빈번하게 업데이트 된다면, 성능적으로 좋지 않다. 실제로 변화가 반영되는 곳은 Value 컴포넌트인데, Buttons 컴포넌트도 리렌더링되기 떄문이다.  

 - 기존의 CounterContext 를 CounterValueContext와 CounterActionsContext로 분리한다.
    - 버튼을 눌러서 상태에 변화가 일어날 때 Value 컴포넌트에서만 리렌더링이 발생한다.
```javascript
import { createContext, useContext, useMemo, useState } from 'react';

// 1. Context 생성(값 컨텍스트와 업데이트 함수 컨텍스트)
const CounterValueContext = createContext();
const CounterActionsContext = createContext();

// 2. CounterProvider라는 커스텀 Provider 생성
// 값 컨텍스트와 업데이트 함수 컨텍스트를 등록해준다.
function CounterProvider({ children }) {
  const [counter, setCounter] = useState(1);
  const actions = useMemo(
    () => ({
      increase() {
        setCounter((prev) => prev + 1);
      },
      decrease() {
        setCounter((prev) => prev - 1);
      }
    }),
    []
  );

  return (
    <CounterActionsContext.Provider value={actions}>
      <CounterValueContext.Provider value={counter}>
        {children}
      </CounterValueContext.Provider>
    </CounterActionsContext.Provider>
  );
}

// 3. Context를 사용할 수 있는 커스텀 훅 생성
// 3-1. 값을 가져오는 커스텀 훅
function useCounterValue() {
  const value = useContext(CounterValueContext);
  if (value === undefined) {
    throw new Error('useCounterValue should be used within CounterProvider');
  }
  return value;
}

// 3-1. 값을 변경하는 커스텀 훅
function useCounterActions() {
  const value = useContext(CounterActionsContext);
  if (value === undefined) {
    throw new Error('useCounterActions should be used within CounterProvider');
  }
  return value;
}

// 4. Provider 설정
function App() {
  return (
    <CounterProvider>
      <div>
        <Value />
        <Buttons />
      </div>
    </CounterProvider>
  );
}

// 5-1. Value 컴포넌트에서 Context의 값 조회
function Value() {
  console.log('Value');
  const counter = useCounterValue();
  return <h1>{counter}</h1>;
}

// 5-2. Buttons 컴포넌트에서 Context의 값 변경
function Buttons() {
  console.log('Buttons');
  const actions = useCounterActions();

  return (
    <div>
      <button onClick={actions.increase}>+</button>
      <button onClick={actions.decrease}>-</button>
    </div>
  );
}

export default App;
```

### useReducer를 이용한 방법

```javascript
import * as React from 'react'

// 1. Context 생성(값 컨텍스트와 업데이트 함수 컨텍스트)
const CounterValueContext = createContext();
const CounterActionsContext = createContext();

// 2. 순수 Reducer 함수 작성
function countReducer(state, action) {
	switch (action.type) {
		case 'increment': {
			return { count: state.count + 1 }
		}
		case 'decrement': {
			return { count: state.count - 1 }
		}
		default: {
			throw new Error(`Unhandled action type: ${action.type}`)
		}
	}
}

// 3. CounterProvider라는 커스텀 Provider 생성
function CountProvider({ children }) {
	const [counter, dispatch] = React.useReducer(countReducer, { count: 0 })
  const actions = useMemo(
    () => ({
      increase() {
        dispatch({ type: 'increment' })
      },
      decrease() {
        dispatch({ type: 'decrement' })
      }
    }),
    []
  );

  return (
    <CounterActionsContext.Provider value={actions}>
      <CounterValueContext.Provider value={counter}>
        {children}
      </CounterValueContext.Provider>
    </CounterActionsContext.Provider>
  );
}

// 4. Context를 사용할 수 있는 커스텀 훅 생성
// 4-1. 값을 가져오는 커스텀 훅
function useCounterValue() {
  const value = useContext(CounterValueContext);
  if (value === undefined) {
    throw new Error('useCounterValue should be used within CounterProvider');
  }
  return value;
}

// 4-1. 값을 변경하는 커스텀 훅
function useCounterActions() {
  const value = useContext(CounterActionsContext);
  if (value === undefined) {
    throw new Error('useCounterActions should be used within CounterProvider');
  }
  return value;
}

// 5. Provider 설정
function App() {
  return (
    <CounterProvider>
      <div>
        <Value />
        <Buttons />
      </div>
    </CounterProvider>
  );
}

// 6-1. Value 컴포넌트에서 Context의 값 조회
function Value() {
  console.log('Value');
  const counter = useCounterValue();
  return <h1>{counter.count}</h1>;
}

// 6-2. Buttons 컴포넌트에서 Context의 값 변경
function Buttons() {
  console.log('Buttons');
  const actions = useCounterActions();

  return (
    <div>
      <button onClick={actions.increase}>+</button>
      <button onClick={actions.decrease}>-</button>
    </div>
  );
}
```
