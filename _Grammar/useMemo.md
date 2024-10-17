# useMemo

useMemo는 React의 훅 중 하나로, 값을 메모이제이션하여 컴포넌트의 성능을 최적화하는 데 사용된다. 복잡한 계산이나 값의 재생성을 피하고 싶을 때 유용하게 사용되며, 의존성 배열에 지정된 값들이 변경될 때만 재계산이 이루어지도록 한다.  

## useMemo 사용 방법

useMemo는 첫 번째 인자로 값을 반환하는 함수를 받고, 두 번째 인자로 의존성 배열을 받는다. 의존성 배열에 있는 값들이 변경될 때에만 첫 번째 인자로 전달된 함수가 실행되고, 그렇지 않으면 계산된 값을 반환한다.  

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

## useMemo 사용 사례

 - 컴포넌트가 렌더링될 때마다 동일한 계산을 반복하는 경우가 있을 때, 이러한 불필요한 계산을 줄여 성능을 향상시키고 싶을 때 사용한다.
 - 예를 들어, 비싼 연산을 통해 계산된 값, 배열이나 객체의 정렬/필터링 결과 등을 캐싱하여 불필요한 연산을 피할 수 있다.
```javascript
// expensiveCalculation 변수는 useMemo를 사용해 메모이제이션되어 의존성 배열이 변경되지 않는 한 재계산되지 않는다.
// 포넌트가 리렌더링되어도 count나 input이 변경될 때 expensiveCalculation은 재계산되지 않고 이전 값을 사용한다.
import React, { useState, useMemo } from 'react';

function ExpensiveCalculationComponent() {
  const [count, setCount] = useState(0);
  const [input, setInput] = useState('');

  // 복잡한 계산을 useMemo로 메모이제이션
  const expensiveCalculation = useMemo(() => {
    console.log('Performing expensive calculation...');
    let result = 0;
    for (let i = 0; i < 1000000000; i++) {
      result += i;
    }
    return result;
  }, []); // 빈 배열이므로 최초 렌더링 시 한 번만 계산

  return (
    <div>
      <p>Expensive Calculation Result: {expensiveCalculation}</p>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
    </div>
  );
}

export default ExpensiveCalculationComponent;
```

 - 배열의 필터링 최적화
    - 리스트 필터링이나 검색에서 useMemo를 사용할 수도 있다. 만약 목록이 길다면, 리렌더링될 때마다 전체 목록을 다시 필터링하는 대신 useMemo로 메모이제이션하여 불필요한 재계산을 방지할 수 있다.
```javascript
import React, { useState, useMemo } from 'react';

function FilteredList() {
  const [query, setQuery] = useState('');
  const items = ['apple', 'banana', 'cherry', 'date', 'elderberry', 'fig', 'grape'];

  // 필터링 결과를 useMemo로 메모이제이션
  const filteredItems = useMemo(() => {
    console.log('Filtering items...');
    return items.filter((item) => item.toLowerCase().includes(query.toLowerCase()));
  }, [query, items]);

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search items..."
      />
      <ul>
        {filteredItems.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}

export default FilteredList;
```
