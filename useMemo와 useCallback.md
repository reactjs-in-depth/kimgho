## useMemo
useMemo는 비용이 큰 연산에 대한 결과를 저장해두고 저장된 값을 반환하는 훅이다.

> 공식 문서에서는 useMemo를 재렌더링 사이에 계산 결과를 캐싱할 수 있게 해주는 React Hook이라 설명하고 있다.

`useMemo(calculateValue, dependencies)`
- 첫 번째 인수로 어떤 값을 반환하는 생성 함수
  - 순수해야 하며 인자를 받지 않고, 모든 타입의 값을 반환할 수 있어야 한다.
- 두 번째 인수로 해당 함수가 의존하는 값의 배열
  - 값에는 props,state와 컴포넌트 바디에 직접 선언된 모든 변수와 함수가 포함
  - 이 의존성 배열의 값이 변경됐다면 첫 번째 인수의 함수를 실행한 후 그 값을 다시 기억
  

  
### 사용법

- useMemo도 훅이기 때문에 컴포넌트의 최상위 레벨 또는 CustomHook에서만 호출할 수 있다.

- 재렌더링 사이에 계산을 캐싱하려면 컴포넌트의 최상위 레벨에서 `useMemo`를 호출하여 계산을 감싸면 된다.
```javascript
import {useMemo} from 'react';

const TodoList=({todos,tab,theme})=>{
  const visibleTodos = useMemo(()=>filterTodos(todos,tab),[todos,tab]);
  ...
```

### 왜 사용하는가?

- 대부분의 계산을 매우 빠르게 하기 때문에 문제가 되지 않는다.
- 그러나 큰 배열을 필터링 혹은 변환하거나 비용이 많이 드는 계산을 수행하는 경우에 데이터가 변경되지 않았지만 다시 계산하는 것은 불필요한 일이다.
- 따라서 불필요한 계산과 리렌더링을 줄이고 성능을 최적화하기 위해 사용한다. 

### 모든 컴포넌트에 useMemo를 사용하는게 좋을까?

- 공식 문서(한국어)에는 아래와 같은 경우에만 유용하다고 설명한다.
>1.`useMemo`에 입력하는 계산이 눈에 띄게 느리고 종속성이 거의 변경되지 않는 경우
2.`memo`로 감싸진 컴포넌트에 prop로 전달할 경우. 값이 변경되지 않았다면 렌더링을 건너뛰고 싶을 것입니다. 메모이제이션을 사용하면 의존성이 동일하지 않은 경우에만 컴포넌트를 다시 렌더링할 수 있습니다.
3.전달한 값을 나중에 일부 Hook의 종속성으로 이용할 경우. 예를 들어, 다른 `useMemo`의 계산 값이 여기에 종속되어 있을 수 있습니다. 또는 `useEffect`의 값에 종속되어 있을 수 있습니다.

---

## useCallback

- `useMemo`가 값을 기억했다면, `useCallback`은 인수로 넘겨받은 콜백 자체를 기억한다.
  - 이는 특정 함수를 새로 만들지 않고 재사용한다는 의미다.

>공식 문서에는 `useCallback`은 리렌더링 간에 함수 정의를 캐싱해 주는 React Hook이라 설명하고 있다.

`useCallback(function,dependencies)`

- 첫 번째 인수로 캐싱할 함수를 받는다.
  - 이 함수는 어떤 인자나 반환값도 가질 수 있다.
- 두 번째 인수로 해당 함수가 의존하는 값의 배열
  - 마찬가지로 의존성이 변했을 때 함수를 재실행한다.
  
### 사용법

- ` useCallback`도 훅이기 때문에 컴포넌트의 최상위 레벨 또는 CustomHook에서만 호출할 수 있다.
- 컴포넌트의 리렌더링 간에 함수를 캐싱하려면 함수 정의를 `useCallback`으로 감싼다.
```javascript
import { useCallback } from "react";

const ProductPage=({productId,referrer,theme})=>{
  const handleSubmit = useCallback((orderDetail)=>{
    post(url,{
      referrer,
      orderDetail,
    });
  },[productId,referrer]);
  ...
```

### 왜 사용하는가?

- `useMemo`와 마찬가지로 불필요한 리렌더링을 방지한다.
- 매번 함수가 새로 생성되면 불필요한 useEffect가 실행이 되는 것을 막기 위함이다.

### 항상 useCallback을 사용하는게 좋은가?

- 이 역시 `useMemo`처럼 모든 상황에서 유용한 것이 아니다.
> 공식 문서에서
1.`memo`로 감싸진 컴포넌트에 prop으로 넘깁니다.
`memoization`은 의존성이 변했을 때만 컴포넌트가 리렌더링하도록 합니다.
2.넘긴 함수가 나중에 어떤 Hook의 의존성으로 사용됩니다. 예를 들어, `useCallback`으로 감싸진 다른 함수가 이 함수에 의존하거나, `useEffect`에서 이 함수에 의존합니다.

---

## useMemo와 useCallback의 차이점

- 두 개 모두 어떤 것을 캐싱한다는 것이 공통적이다.

차이는

**`useMemo`** 의 경우 호출한 **함수의 결과값**을 캐싱한다.

**`useCallback`** 의 경우 **함수 자체**를 캐싱한다.

---

## 주의점

`useMemo`와 `useCallback`모두 성능 최적화를 위한 용도로 사용을 권장하고 있다.

>만약 코드가 `useMemo`와 `useCallback` 없이 작동하지 않는다면 근본적인 문제를 찾아 해결한 뒤 최적화를 해야 한다.

무분별하게 사용할 경우 오히려 성능 저하를 초래한다.

**메모이제이션 비용**
- 캐싱을 위한 메모리 사용량이 늘어나고, 계속 메모리에 남아 있기 때문에 메모리 관리에서 비효율적이다.

**의존성 배열 관리**
- 의존성 배열 값들이 변경될 때마다 메모이제이션 값을 무효화하고 새로 계산하는데, 이 과정에서 복잡성이 증가하고 관리를 잘못하면 성능이 저하된다.


[useMemo 한국 문서](https://ko.react.dev/reference/react/useMemo)
[useCallback 한국 문서](https://ko.react.dev/reference/react/useCallback)
