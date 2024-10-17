# useRef

 - [useState대신 useRef를 사용해야 하는 경우](https://hanghae99.spartacodingclub.kr/blog/react-usestate-%EB%8C%80%EC%8B%A0-useref%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC-%ED%95%98%EB%8A%94-%EA%B2%BD%EC%9A%B0-20844)
 - [리액트에서 useRef의 이해와 활용](https://f-lab.kr/insight/understanding-and-using-useref-in-react?gad_source=1&gclid=CjwKCAjw68K4BhAuEiwAylp3kokfICrzKfLFnQV1T_UiUl9V3E9nMhfdfegt3tujWgsMARmuEPosBRoCxB4QAvD_BwE)

리액트 환경에서 개발시 컴포넌트 내에서 DOM 요소나 특정 값을 참조해야 하는 상황이 발생한다. 이때, useRef 훅을 사용하면 렌더링과 무관하게 값을 유지할 수 있다.  

## useRef 기본 개념

리액트에서 useRef는 DOM 요소에 직접 접근할 때 사용하는 훅이다. useRef는 렌더링 간에도 그 값이 유지되며, 이를 통해 다양한 용도로 활용될 수 있다.  
 - __useRef는 변경될 때 리렌더링을 유발하지 않는다.__ 컴포넌트 내에서 변경되어야 하는 값이 있지만, 이 값의 변경이 화면에 바로 반영될 필요가 없는 경우 useRef를 사용할 수 있다.
 - __useRef는 컴포넌트 내에서 조회 및 수정이 가능한 변수를 관리하는 데에도 사용될 수 있다.__ 이전 상태값을 참조해야 할 때 유용하게 사용될 수 있다.
 - useRef는 주로 DOM 요소에 접근하기 위해 사용되지만, 컴포넌트 내부에서 지속적으로 참조해야 하는 값을 관리하는 데에도 활용될 수 있다.

### useRef 활용 사례

useRef를 사용하면 특정 DOM 요소에 직접 접근할 수 있다. 예를 들어, __특정 입력 필드에 자동으로 포커스를 맞추거나, 스크롤 위치를 조절__ 하는 등의 작업을 수행할 수 있다. 또한, __useRef는 컴포넌트가 렌더링될 때마다 초기화되지 않는 특성__ 때문에, 애니메이션 상태를 관리하거나, __setTimeout이나 setInterval과 같은 타이머 함수의 id를 저장하는 용도__ 로도 사용될 수 있다.  
 - 포커스 관리, 애니메이션 제어, 외부 라이브러리와의 통합

### useRef 사용 시 주의사항

useRef로 생성된 객체는 불변하지 않으므로, 참조하는 값이 변경될 경우 이를 수동으로 관리해야 한다. useRef로 관리되는 값이 변경되어도 컴포넌트가 리렌더링되지 않기 떄문에, 이러한 변경사항을 컴포넌트에 반영하기 위해서는 추가적인 로직이 필요할 수 있다.  

useRef는 불변의 참조를 관리하는 데 사용되므로, 참조 대상이 변경될 가능성이 있는 경우에는 useState나 useReducer와 같은 다른 상태 관리 훅을 사용하는 것이 더 적합할 수 있다.  

__useRef를 사용하여 DOM 요소에 접근할 때는 해당 요소가 실제로 마운트된 후에 접근해야 한다.__ 그렇지 않으면, 참조하려는 DOM 요소가 아직 존재하지 않아 오류가 발생할 수 있다.  

useRef는 리액트의 데이터 흐름을 따르지 않는 탈출구 역할을 할 수 있으므로, 이를 남용하지 않도록 주의해야 한다. useRef를 사용할 때는 리액트의 선언적인 패러다임을 최대한 유지하려는 노력이 필요하다.  

## useRef 사용 방법

useRef 훅을 사용하여 참조 객체를 생성하고, 이 객체를 DOM 요소의 ref 속성에 할당함으로써 해당 DOM 요소에 접근할 수 있게 된다.  

 - 버튼 클릭시 Element 포커스
```typescript
// JavaScript
import React, { useRef, useEffect } from 'react';

function TextInputWithFocusButton() {
    // 1. useRef 참조 객체 생성
    const inputRef = useRef(null);

    const onButtonClick = () => {
        inputRef.current.focus();
    };
    
    return (
        <>
            {/* 2. 참조 객체를 DOM 요소의 ref 속성에 할당 */}
            <input type="text" ref={inputRef}>
            <button onClick="onButtonClick">
        </>
    );
}

// TypeScript
function TextInputWithFocusButton() {
    const inputRef = useRef<HTMLInputElement>(null);

    const onButtonClick = () => {
        inputRef.current?.focus();
    };
    
    return (
        <>
            <input type="text" ref={inputRef} />
            <button onClick={onButtonClick}>Focus the input</button>
        </>
    );
}
```

 - 버튼 클릭시 자식 Element 중에서 포커스
```typescript
// JavaScript
import React, { useRef, useEffect } from 'react';

function TextInputWithFocusButton() {
    const divRef = useRef(null);
    const inputElement = stepRef.current.querySelector('input');

    const onButtonClick = () => {
        if (divRef.current) {
            const inputElement = divRef.current.querySelector('input');
            if (inputElement) {
                inputElement.focus();
            }
        }
    };
    
    return (
        <>
            <div useRef={divRef}>
                <input type="text" />
                <button onClick={onButtonClick}>Focus the input</button>
            </div>
        </>
    );
}

// TypeScript
function TextInputWithFocusButton() {
    const divRef = useRef<HTMLDivElement>(null);
    const inputElement = stepRef.current.querySelector('input');

    const onButtonClick = () => {
        if (divRef.current) {
            const inputElement = divRef.current.querySelector('input');
            if (inputElement) {
                inputElement.focus();
            }
        }
    };
    
    return (
        <>
            <div useRef={divRef}>
                <input type="text" />
                <button onClick={onButtonClick}>Focus the input</button>
            </div>
        </>
    );
}
```

 - 데이터 저장소로 사용
    - useRef를 사용하여 데이터를 관리하면 데이터 양이 증가하더라도 컴포넌트의 렌더링 속도에 영향을 주지 않는다.
```javascript
import React, { useRef } from 'react';

const BigDataComponent = () => {
  const dataRef = useRef('');

  const fetchData = () => {
    // 대용량 데이터를 가져온다고 가정
    // useRef를 사용하여 컴포넌트의 렌더링에 영향을 주지 않고 데이터를 설정
    const newData = 'Very large data...';
    dataRef.current = newData;
  };

  return (
    <div>
      <button onClick={fetchData}>Fetch Data</button>
      <p>Data Length: {dataRef.current.length}</p>
    </div>
  );
};

export default BigDataComponent;
```
