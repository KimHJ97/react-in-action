# Custom Hook 만들기

커스텀 Hook은 React에서 재사용 가능한 로직을 캡슐화하여 여러 컴포넌트에서 공유할 수 있는 기능이다. React의 기본 Hook(useState, useEffect, useContext, 등)을 조합하여 더 복잡한 상태 관리나 로직을 처리할 수 있으며, 이를 하나의 함수로 묶어 커스텀 Hook으로 만든다.  

커스텀 Hook은 함수로 이 함수는 다른 Hook처럼 상태나 효과(사이드 이펙트)를 관리할 수 있으며, 일반 함수와 다르게 React Hook들을 사용할 수 있다. __커스텀 Hook을 만들 때에는 보통 'use'라는 키워드로 시작하는 파일을 만들고 작성한다. 해당 파일 안에서 useState, useEffect, useReducer, useCallback 등 Hooks를 사용하여 원하는 기능을 구현해주고, 컴포넌트에서 사용하고 싶은 값들을 반환해주면 된다.__
 - __코드 재사용__: 여러 컴포넌트에서 중복되는 로직을 커스텀 Hook으로 추출하여 코드 중복을 줄일 수 있다.
 - __더 깔끔한 코드__: 복잡한 비즈니스 로직을 컴포넌트에서 분리하여, 컴포넌트는 UI에 집중할 수 있게 만들고, 로직은 커스텀 Hook에 넣어 관리한다.
 - __상태와 로직을 함께 관리__: 상태와 사이드 이펙트를 하나의 함수에서 처리할 수 있기 때문에, 관련된 로직을 하나로 묶어서 관리할 수 있다.

## Custom Hook 활용 예시

### API 데이터 fetch Hook

 - useFetch
    - 매개변수로 URL을 받아서 컴포넌트 렌더링 시에 해당 URL에 API를 호출한 후 데이터를 설정한다.
    - useState와 useEffect를 사용하여 데이터를 가져오는 비동기 로직과 로딩 상태 및 오류를 관리한다.
```javascript
import { useState, useEffect } from 'react';

// 데이터를 fetch하는 커스텀 Hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then((response) => response.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((error) => {
        setError(error);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}

export default useFetch;
```

 - useFetch 사용
    - useFetch 커스텀 Hook을 사용하여 데이터를 가져오고, 로딩 중에는 "Loading..."을, 오류가 발생하면 오류 메시지를 표시하며, 데이터를 정상적으로 가져오면 화면에 표시합니다.
```javascript
import React from 'react';
import useFetch from './useFetch'; // 커스텀 Hook 불러오기

function DataFetchingComponent() {
  const { data, loading, error } = useFetch('https://jsonplaceholder.typicode.com/posts/1');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error occurred: {error.message}</p>;

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.body}</p>
    </div>
  );
}

export default DataFetchingComponent;
```

### 폼 입력을 관리하는 커스텀 Hook

 - useInput
    - 초기값을 받아 폼 입력값을 관리한다. 입력값을 쉽게 변경하고, 입력값을 초기화하는 reset 함수도 제공한다.
```javascript
import { useState } from 'react';

// 폼 입력을 관리하는 커스텀 Hook
function useInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  function reset() {
    setValue('');
  }

  return {
    value,
    onChange: handleChange,
    reset,
  };
}

export default useInput;
```

 - useInput 사용
```javascript
import React from 'react';
import useInput from './useInput';

function InputComponent() {
  const name = useInput(''); // useInput Hook 사용

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(name.value);
    name.reset(); // 입력 초기화
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name.value}
        onChange={name.onChange}
        placeholder="이름 입력"
      />
      <button type="submit">Submit</button>
    </form>
  );
}

export default InputComponent;
```
