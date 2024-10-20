# useReducer

 - [useReducer 공식 문서](https://ko.react.dev/reference/react/useReducer)
 - [useReducer를 언제 쓰는게 좋을까?](https://velog.io/@semnil5202/useReducer%EB%A5%BC-%EC%96%B8%EC%A0%9C-%EC%93%B0%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C)
 - [useReducer를 사용하여 상태 업데이트 로직 분리하기](https://react.vlpt.us/basic/20-useReducer.html)
 - [useReducer 사용 방법](https://ccomccomhan.tistory.com/281)

## useReducer 기본 개념

useReducer는 React에서 상태 관리를 위한 Hook으로, 복잡한 상태 로직을 처리할 때 유용합니다. 상태를 업데이트할 때 단순한 useState Hook 대신, 리듀서(reducer) 함수와 **액션(action)**을 통해 상태 변화를 관리할 수 있다.  
 - useReducer는 Redux에서 사용하는 리듀서 패턴과 유사하다. 상태(state)와 액션(action)을 기반으로 상태를 변경하는 방식으로, 한 곳에 여러 상태 업데이트 로직을 모아서 처리할 수 있다.
    - state: 현재 상태 값입니다.
    - dispatch: 상태를 업데이트하기 위해 호출되는 함수로, 액션을 전달합니다.
    - reducer: 상태를 업데이트하는 함수로, 이전 상태와 액션을 받아서 새로운 상태를 반환합니다.
    - initialState: 상태의 초기값입니다.
```javascript
// 1. 리듀서 함수 정의
// state와 action을 매개변수로 받는 순수 함수를 작성한다.
// 상태를 직접 수정하지 않고 새로운 상태 객체를 반환하여 불변성을 유지한다.
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}

// 2. 리듀서 함수와 초기값을 인자로 넘겨 리듀서를 생성한다.
const [state, dispatch] = useReducer(reducer, initialState);

// 3. 리듀서 함수 트리거
// dispatch는 상태 업데이트를 트리거하는 함수이다.
// dispatch 함수에 액션 객체를 전달하면 리듀서 함수가 호출되어 상태를 업데이트한다.
// 액션 객체는 { type: 'action_type', payload: additionalData } 형태로, type은 필수적으로 상태를 변경하는 유형을 나타냅니다.
dispatch({ type: 'increment' });
```

## useReducer 활용 예시

 - 카운터 예시
```javascript
import React, { useReducer } from 'react';

// 리듀서 함수 정의
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}

function Counter() {
  const initialState = { count: 0 };

  // useReducer 사용
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}

export default Counter;
```

 - 복잡한 상태 로직 처리
```javascript
import React, { useReducer } from 'react';

// 복잡한 상태를 관리하는 리듀서 함수
function reducer(state, action) {
  switch (action.type) {
    case 'addItem':
      return { ...state, items: [...state.items, action.payload] };
    case 'removeItem':
      return { ...state, items: state.items.filter((item) => item !== action.payload) };
    case 'toggleTheme':
      return { ...state, darkMode: !state.darkMode };
    default:
      return state;
  }
}

function App() {
  const initialState = { items: [], darkMode: false };
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Current theme: {state.darkMode ? 'Dark' : 'Light'}</p>
      <button onClick={() => dispatch({ type: 'toggleTheme' })}>
        Toggle Theme
      </button>

      <p>Items: {state.items.join(', ')}</p>
      <button onClick={() => dispatch({ type: 'addItem', payload: 'NewItem' })}>
        Add Item
      </button>
      <button onClick={() => dispatch({ type: 'removeItem', payload: 'NewItem' })}>
        Remove Item
      </button>
    </div>
  );
}

export default App;
```

 - 복잡한 상태 로직 처리2
```javascript
import React, { useRef, useReducer, useMemo, useCallback } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

// 초기값
const initialState = {
  inputs: {
    username: '',
    email: ''
  },
  users: [
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]
};

// reducer 순수 함수
function reducer(state, action) {
  switch (action.type) {
    case 'CHANGE_INPUT':
      return {
        ...state,
        inputs: {
          ...state.inputs,
          [action.name]: action.value
        }
      };
    case 'CREATE_USER':
      return {
        inputs: initialState.inputs,
        users: state.users.concat(action.user)
      };
    case 'TOGGLE_USER':
      return {
        ...state,
        users: state.users.map(user =>
          user.id === action.id ? { ...user, active: !user.active } : user
        )
      };
    case 'REMOVE_USER':
      return {
        ...state,
        users: state.users.filter(user => user.id !== action.id)
      };
    default:
      return state;
  }
}

function App() {
  // useReducer 사용 - 상태(state)와 리듀서 트리거 함수(dispatch) 반환
  const [state, dispatch] = useReducer(reducer, initialState);
  const nextId = useRef(4);

  const { users } = state;
  const { username, email } = state.inputs;

  // 변경시 리듀서 함수 실행 (CHANGE_INPUT)
  // dispatch함수의 넘겨주는 객체는 action 매개변수에 들어간다.
  const onChange = useCallback(e => {
    const { name, value } = e.target;
    dispatch({
      type: 'CHANGE_INPUT',
      name,
      value
    });
  }, []);

  // 생성시 리듀서 함수 실행 (CREATE_USER)
  // dispatch함수의 넘겨주는 객체는 action 매개변수에 들어간다.
  const onCreate = useCallback(() => {
    dispatch({
      type: 'CREATE_USER',
      user: {
        id: nextId.current,
        username,
        email
      }
    });
    nextId.current += 1;
  }, [username, email]);

  const onToggle = useCallback(id => {
    dispatch({
      type: 'TOGGLE_USER',
      id
    });
  }, []);

  const onRemove = useCallback(id => {
    dispatch({
      type: 'REMOVE_USER',
      id
    });
  }, []);

  const count = useMemo(() => countActiveUsers(users), [users]);
  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onToggle={onToggle} onRemove={onRemove} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;
```
