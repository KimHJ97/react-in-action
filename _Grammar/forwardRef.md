# forwardRef

 - [forwardRef 공식 문서](https://ko.react.dev/reference/react/forwardRef)
 - [forwardRef 사용법](https://www.daleseo.com/react-forward-ref/)

## forwardRef 기본 개념

forwardRef는 React에서 부모 컴포넌트가 자식 컴포넌트의 DOM 요소나 해당 요소에 접근할 수 있게 하기 위해 사용하는 함수이다.  
React에서 특수한 목적으로 사용되기 떄문에 일반적인 용도로 사용할 수 없는 prop이 몇 가지 있다. 대표적인 예로 루프를 돌면서 동일한 컴포넌트 여러 번 렌더링할 때 사용하는 key prop이 있다. ref prop도 마찬가지로 HTML 엘리먼트 접근이라는 특수한 용도로 사용되기 떄문에 일반적인 prop으로 사용할 수 없다.  
 - __HTML 엘리먼트가 아닌 React 컴포넌트에서 ref prop을 사용하려면 forwardRef() 함수를 이용해야 한다.__

## forwardRef 사용 방법

자식 컴포넌트를 만들 때 forwardRef() 함수로 감싸준다. forwardRef 함수로 컴포넌트를 감싸면서 부모로부터 전달된 ref를 자식 컴포넌트의 특정 DOM 요소에 연결할 수 있게 된다.  
 - __forwardRef__: 부모가 자식 컴포넌트의 DOM 요소에 접근할 수 있도록 함.
 - __useRef__: DOM 요소에 접근할 때 사용하는 React 훅.
 - __ref 전달__: forwardRef를 통해 자식 컴포넌트의 내부 DOM에 ref를 전달하여 부모에서 접근 가능하게 만듦.
```javascript
import React, { useRef, forwardRef } from 'react';

// 자식 컴포넌트
const CustomInput = forwardRef(function CustomInput(props, ref) => {
  return <input ref={ref} {...props} />;
});

// 부모 컴포넌트
const ParentComponent = () => {
  const inputRef = useRef(null);

  const focusInput = () => {
    if (inputRef.current) {
      // CustomInput의 <input>에 직접 포커스를 줄 수 있음
      inputRef.current.focus(); 
    }
  };

  return (
    <div>
      <CustomInput ref={inputRef} />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
};

export default ParentComponent;
```
