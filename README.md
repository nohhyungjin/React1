# 202130113 노형진
## 2025-05-15 11주차
#### Step 4: State가 어디에 있어야 할 지 정하기

1. 각 state에 대해 다음을 수행:

   * 해당 state를 사용(렌더링)하는 컴포넌트를 찾는다.
   * 이들을 모두 포괄하는 가장 가까운 공통 부모 컴포넌트를 찾는다.
   * 보통 그 공통 부모에 state를 두는 것이 적절하다.
   * 공통 부모가 없다면 새로 컴포넌트를 만들어 상위에 추가할 수 있다.

2. 예제에서 사용된 두 가지 state:

   * `filterText`: 검색어
   * `inStockOnly`: 체크박스 상태 (재고 있는 상품만 보기)

3. state를 사용하는 컴포넌트:

   * `ProductTable`: 리스트 필터링
   * `SearchBar`: 입력값 표시

4. 공통 부모:

   * `FilterableProductTable` → 두 컴포넌트 모두 이 컴포넌트의 자식

5. 결정된 state 위치:

   * `FilterableProductTable` 내부에 `useState`로 두 state를 선언함

---

코드 적용

```jsx
function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar
        filterText={filterText}
        inStockOnly={inStockOnly}
      />
      <ProductTable
        products={products}
        filterText={filterText}
        inStockOnly={inStockOnly}
      />
    </div>
  );
}
```

이렇게 하면 `filterText`와 `inStockOnly`가 `SearchBar`와 `ProductTable` 양쪽에 전달되어 렌더링에 사용됨

---

`SearchBar` 컴포넌트가 `filterText`, `inStockOnly` 값을 props로 받아 입력 요소의 값은 보여주지만 변경할 수 없음
에러 메시지:

  ```
  You provided a `value` prop to a form field without an `onChange` handler.
  ```
* 이유: 입력 요소가 "읽기 전용"이기 때문에 onChange 핸들러가 필요

---

state를 적절한 위치에 두는 데이터 흐름 구성"까지 완료
이제 `onChange` 이벤트 핸들러를 추가하여 입력값을 상태로 반영하고 업데이트할 수 있게 만들어야 함

#### Step 5: 역 데이터 흐름 추가하기



1. 기존 데이터 흐름

* React 컴포넌트는 일반적으로 부모 → 자식 방향으로 데이터(props)를 전달
* 예: `FilterableProductTable` → `SearchBar`, `ProductTable`

2. 문제 상황

* 사용자 입력(input, checkbox)에 반응해 UI를 업데이트해야 하는데,
* `input`의 `value`가 `filterText` 상태에 고정되어 있으므로,
* 사용자가 값을 바꿔도 `state`가 바뀌지 않으면 화면도 바뀌지 않음 (입력 무시됨)

3. 해결 방법: 역 데이터 흐름

* `SearchBar`는 자신이 직접 state를 갖지 않음 → 대신 부모의 상태를 변경할 수 있어야 함
* 이를 위해 `setFilterText`, `setInStockOnly` 함수를 `SearchBar`에 props로 전달함
* `SearchBar`에서 `onChange` 이벤트 발생 시 이 함수들을 호출하여 부모 상태를 변경

4. **결과

* 사용자 입력에 따라 `FilterableProductTable`의 state가 변경되고,
* 상태 변화가 다시 props를 통해 자식 컴포넌트로 전달되므로
* UI가 사용자 입력에 맞춰 자동으로 업데이트됨

---

#### FilterableProductTable

```jsx
<SearchBar
  filterText={filterText}
  inStockOnly={inStockOnly}
  onFilterTextChange={setFilterText}
  onInStockOnlyChange={setInStockOnly}
/>
```

#### SearchBar

```jsx
<input
  type="text"
  value={filterText}
  onChange={(e) => onFilterTextChange(e.target.value)}
/>

<input
  type="checkbox"
  checked={inStockOnly}
  onChange={(e) => onInStockOnlyChange(e.target.checked)}
/>
```

---

결론

* React는 명시적인 데이터 흐름을 권장함
* "역 데이터 흐름"을 구현하려면 상태를 끌어올리고(`lifting state up`), 자식 컴포넌트에서 부모 상태를 변경할 수 있는 함수 props를 전달해야 함
* 이 방식은 초기 설정은 번거로울 수 있지만, UI의 일관성과 예측 가능성이 높아짐

## 2025-05-08 10주차
### React로 사고하기
컴포넌트로 나누고 연결하는 방식으로 바꿔서 보겠다는 얘기
#### 모의 시안과 함께 시작하기 
이걸 만드는 게 목표  
<img src="./images/s_thinking-in-react_ui.png" alt="목록이 있는 보드" width="200" />
#### Step 1: UI를 컴포넌트 계층으로 쪼개기
* 컴포넌트 나누기
  * 디자인 시안 분석: 모든 UI 요소에 박스를 그려 이름을 붙이며 컴포넌트를 식별
  * 디자이너와 협업: 이미 정의된 컴포넌트 명칭이 있을 수 있으므로 디자이너에게 확인하기
* 관점에 따른 컴포넌트 분해
  * 프로그래밍 관점: 단일 책임 원칙(SRP)을 따르며, 컴포넌트는 한 가지 기능만 수행, 복잡해질 경우 하위 컴포넌트로 나누기
  * CSS 관점: 클래스 선택자 기준으로 나누기
  * 디자인 관점: 디자인 계층 구조를 고려해 나누기
  * 데이터(JSON) 구조: 잘 구조화된 데이터는 UI 구조와 자연스럽게 일치하며, 이를 기반으로 컴포넌트를 정의

예시 컴포넌트 설명 및 구조  
<img src="./images/s_thinking-in-react_ui_outline.png" alt="목록이 있는 보드" width="200" />  
컴포넌트 목록:  
* FilterableProductTable (회색)
  * 전체 UI를 포괄하는 루트 컴포넌트
* SearchBar (파란색)
  * 사용자 입력을 위한 검색창
* ProductTable (라벤더색)
  * 제품 목록을 보여주며 필터링 기능 포함
* ProductCategoryRow (초록색)
  * 제품 카테고리 헤더
* ProductRow (노란색)
  * 개별 제품 정보를 표시하는 행

"Name"과 "Price" 헤더는 현재 ProductTable 내부에 포함되어 있지만, 기능이 복잡해지면 ProductTableHeader로 분리 가능

계층 구조로 정리한 거

* FilterableProductTable
  * SearchBar
  * ProductTable
    * ProductCategoryRow
    * ProductRow
   
#### Step 2: React로 정적인 버전 구현하기

1. 정적 UI 버전부터 시작하자

* 우선순위: 상호작용 없는 정적 버전 → 이후 상호작용 추가
* 장점: 타이핑은 많지만, 복잡한 로직 없이 구조에만 집중 가능
* 상호작용 로직(state 등)는 이 단계에서 생략

---

2. 컴포넌트 구조와 개발 흐름

* 컴포넌트 계층구조에 따라 상향식(Bottom-up) 혹은 하향식(Top-down) 개발 가능

  * 간단한 예시: 하향식
  * 복잡한 프로젝트: 하향식 + 컴포넌트별 테스트

---

3. 컴포넌트 역할 요약

* `FilterableProductTable`: 최상위 컴포넌트, `products` 데이터를 `props`로 전달
* `SearchBar`: 검색창 + 체크박스 UI만 표시
* `ProductTable`: 상품 테이블 렌더링, 카테고리별로 그룹화
* `ProductCategoryRow`: 카테고리 헤더 행
* `ProductRow`: 상품의 이름/가격을 보여주는 행

  * 재고 없는 상품은 **빨간색**으로 표시

---

4. 데이터 전달 방식

* 단방향 데이터 흐름: 데이터는 상위 컴포넌트에서 하위 컴포넌트로 `props`를 통해 전달
* 이 구조는 예측 가능하고 유지보수가 용이함

---

5. 예시 데이터

```js
const PRODUCTS = [
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  ...
];
```

---

결과

이 과정을 통해 **재사용 가능한 정적 컴포넌트 라이브러리**를 갖추게 되며, 이후 상태(state)와 상호작용 기능을 쉽게 추가할 수 있음

#### Step 3: 최소한의 데이터만 이용해서 완벽하게 UI State 표현하기 

* React 컴포넌트가 기억해야 할 변경 가능한 데이터
* UI 상의 변화에 따라 상태를 저장하고 업데이트하는 데 사용됨

State 구성 원칙

* 중복 배제 원칙 (DRY: Don’t Repeat Yourself):

  * 필요한 최소한의 데이터만 state로 관리
  * 파생 데이터(계산 가능 데이터)는 state로 저장하지 않음

---

State 여부를 판단하는 3가지 질문

1. 시간이 지나도 변하지 않는가?
   → 변하지 않으면 state 아님
2. props로 부모 컴포넌트로부터 전달되는가?
   → 그렇다면 state 아님
3. 다른 state나 props로부터 계산 가능한가?
   → 그렇다면 state 아님

> 이 세 질문에 모두 "아니오"인 경우에만 state로 간주

---

예시 애플리케이션에서의 적용

| 데이터 항목        | state 여부 | 이유                           |
| ------------- | -------- | ---------------------------- |
| 제품의 원본 목록     | ❌        | `props`로 전달됨                 |
| 검색어 (텍스트 입력값) | ✅        | 시간에 따라 변하고 계산 불가능            |
| 체크박스 값        | ✅        | 시간에 따라 변하고 계산 불가능            |
| 필터링된 제품 목록    | ❌        | 원본 목록, 검색어, 체크박스를 기반으로 계산 가능 |

---

결론

* 이 애플리케이션에서 관리해야 할 state는 두 가지:

  1. 검색어 (`searchText`)
  2. 체크박스 상태 (`inStockOnly`)

이렇게 최소한의 state만 추출하면 애플리케이션이 간단하고 유지보수하기 쉬워짐

---

## 2025-04-18 8주차
### 틱택토 만들기(이어서)
#### 시간여행 추가하기(이어서)
##### 한 번 더 state 끌어올리기(이어서)
```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    // TODO
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
        //...
  )
}
```
onPlay로 줬으니까 Board에도 onPlay로 받도록 추가해줘야 함

```js
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    //...
  }
  // ...
}
```

```js
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    onPlay(nextSquares);
  }
  //...
}
```

원래 `Board`가 `useState` 두 개를 이용했지만 이제 `Game`이 대신 하고, `props`로 제어하게 됨

마지막으로 `Game` 안에 있는 `handlePlay`까지 만들어주면 잘 작동할 것임  
handlePlay가 해줘야 하는 것은,
- 새로운 보드 상태(nextSquares)를 history 배열에 추가
- 다음 차례 플레이어를 결정하기 위해 xIsNext 값을 반전

```js
export default function Game() {
  //...
  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }
  //...
}
```

여기서 사용한 방법이

`[...]` 전개 연산자(spread operator):

`history`의 기존 항목들을 새 배열에 복사

맨 뒤에 `nextSquares`를 추가

`setHistory(...):`

이 새로운 배열로 `history` 상태를 갱신 → 컴포넌트 리렌더링 유도

`setXIsNext(!xIsNext):`

현재 차례를 반전시켜 다음 턴을 준비

<img src="./images/BoardWithMessage.png" alt="메시지가 있는 보드" width="100" />
UI로 잘 보여준다

---
##### 과거 움직임 보여주기

이제 이전에 두어진 수를 목록으로 보여줄 수 있도록 만들기

`history`에 저장해놨으니까, `map`을 이용해서 React 엘리먼트 배열로 만들어서 사용하기

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}
```

- `map`을 사용해서 `history`의 각 항목을 `<button>`으로 변환  
- 버튼 클릭 시 jumpTo(move)가 호출되어 해당 시점으로 점프
- `<ol>{moves}</ol>`로 출력

콘솔에 이렇게 뜨는데,
```console
Each child in a list should have a unique "key" prop.

Check the render method of `Game`. See https://react.dev/link/warning-keys for more information.
```
React는 목록을 렌더링할 때 각 항목을 추적하기 위해 고유한 key 값이 필요함  
→ 없으면 성능 저하 및 잘못된 렌더링 가능성

<img src="./images/BoardWithHistory.png" alt="목록이 있는 보드" width="100" />
어쨌든 잘 보인다

---

##### Key 선택하기

**React의 `key`란?**
- **React가 리스트를 효율적으로 렌더링**하기 위해 사용하는 **특별한 속성**
- 각 항목을 고유하게 식별해서 **변경, 추가, 제거를 정확하게 처리**함

---

**동작 방식**
1. 리스트가 업데이트되면 React는 각 항목의 `key`를 비교함
2. 이전 리스트와 **key가 일치**하면:
   - 기존 컴포넌트 유지 (state도 유지됨)
3. **key가 새로 생겼다면**:
   - 새로운 컴포넌트 생성
4. **key가 없어졌다면**:
   - 해당 컴포넌트 제거

---

**왜 중요한가?**
- `key`가 있어야 React가 **컴포넌트를 다시 만들지 않고 재사용**할 수 있음
- key가 바뀌면 **컴포넌트가 제거되고 새로 생성되며, state도 초기화**됨
- 성능과 사용자 경험에 큰 영향

---

**주의사항**
- `key`는 컴포넌트 **형제 사이에서만 고유**하면 충분함 (전역 X)
- `props`처럼 보이지만 **컴포넌트 내부에서는 접근할 수 없음**
- 배열 인덱스를 `key`로 사용하는 것은 **권장되지 않음**
  - 순서 변경이나 항목 삽입 시 **잘못된 재렌더링** 발생 가능

---

##### 시간여행 구현하기

```jsx
const moves = history.map((squares, move) => {
  //...
  return (
    <li key={move}>
      <button onClick={() => jumpTo(move)}>{description}</button>
    </li>
  );
});
```
그래서 키를 추가해주고,

jumpTo를 구현하기 위해 현재 어떤 단계인지 알려줄 `currentMove`라는 새 `state`를 만들어줌

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[history.length - 1];
  //...
}
```

그리고 currentMove를 계속 업데이트 하도록 jumpTo를 만들어줌

```jsx
export default function Game() {
  // ...
  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
    setXIsNext(nextMove % 2 === 0);
  }
  //...
}
```

이제 과거에 뒀던 수를 클릭한 다음, 그때 새로운 수를 두면 그 이후에 뒀던 수는 필요없으니까 지우도록 바꿈

```jsx
function handlePlay(nextSquares) {
  const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
  setHistory(nextHistory);
  setCurrentMove(nextHistory.length - 1);
  setXIsNext(!xIsNext);
}
```

그리고 currentSquares를 굳이 마지막 동작을 불러오는 게 아니라 currentMove라고 현재 상태를 보여줄 수 있으니까 바꿈

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[currentMove];

  // ...
}
```
---

##### 최종 정리

`currentMove`가 짝수일 때는 `xIsNext === true`가 되고, `currentMove`가 홀수일 때는 `xIsNext === false`가 됨  
굳이 state 두 개 다 저장할 필요 없으니까 하나는 지움

```jsx
export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }
  // ...
}
```

#### 마무리

이제 다 만들었음

1. 현재 이동에 대해서만 버튼 대신 “당신은 #번째 순서에 있습니다…”를 표시해 보세요.
2. Board를 하드 코딩 하는 대신 두 개의 루프를 사용하여 사각형을 만들도록 다시 작성해 보세요.
3. 동작을 오름차순 또는 내림차순으로 정렬할 수 있는 토글 버튼을 추가해 보세요.
4. 누군가 승리하면 승리의 원인이 된 세 개의 사각형을 강조 표시해 보세요. (아무도 승리하지 않으면 무승부라는 메시지를 표시하세요. )
5. 이동 히스토리 목록에서 각 이동의 위치를 형식(열, 행)으로 표시해 보세요.

연습을 더 하고 싶으면 이런 기능도 구현해보라고 써있음

## 2025-04-17 7주차
### 틱택토 만들기(이어서)
#### 게임 완료하기(이어서)
##### state 끌어올리기(이어서)
- handleClick 함수는 왼쪽 위 사각형만 가능하도록 하드 코딩 되어 있음
- 같은 함수로 모든 사각형을 컨트롤할 수 있도록 파라미터로 i를 추가
```js
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```
그리고 실행해보면...
```jsx
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```
```Console
Too many re-renders. React limits the number of renders to prevent an infinite loop.
```

오류가 남

handleClick(0)처럼 괄호를 붙여 함수를 즉시 실행하면 컴포넌트 렌더링 도중 state를 변경하여 **무한 렌더링 루프 발생**

그래서 JSX에서 onSquareClick={() => handleClick(i)}처럼 화살표 함수(Arrow Function)를 사용해 클릭 시점에만 실행되도록 함
```jsx
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        // ...
  );
}
```
잘 작동하니까 나머지 사각형도 수정

```jsx
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
};
```
<img src="./images/BoardWithX2.png" alt="X가 있는 보드" width="100" />
클릭했을 때 X가 잘 생성된다

그리고 모든 state 관리가 사각형이 아닌 Board 컴포넌트에서 처리된다

---

컴포넌트 간 역할 분담  
- **Board**가 모든 상태(state)를 관리함  
- **Square**는 props를 통해 값(`value`)과 클릭 이벤트 핸들러(`onSquareClick`)를 전달받음  
- 클릭 시, Square → Board로 이벤트가 전달되어 상태 변경 요청이 이루어짐



클릭 이벤트 흐름  
1. 사용자가 왼쪽 위 사각형 클릭  
2. `Square`의 `<button>`이 `onClick`으로 받은 함수 실행  
3. 해당 함수는 `Board`에서 props로 전달한 `onSquareClick()` (즉, `() => handleClick(0)`)  
4. `handleClick(0)` → `squares[0]`을 `"X"`로 업데이트  
5. 상태가 바뀌어 `Board` 및 자식 컴포넌트들 전체가 리렌더링  
6. 해당 사각형에 `"X"` 표시됨



React 명명 규칙 팁  
- 이벤트 prop: `onSomething` (`onClick`, `onChange`, `onSquareClick` 등)  
- 이벤트 핸들러 함수: `handleSomething` (`handleClick`, `handleSubmit` 등)  
- DOM의 `onClick`은 특별한 prop이며, 사용자 정의 prop는 이름을 자유롭게 정할 수 있음

---

요점  
- **Board가 상태를 중앙에서 관리**, **Square는 단순히 표시하고 클릭 전달**  
- 클릭 시 인덱스를 기반으로 상태 변경  
- 상태 변화 → 자동 리렌더링  
- **React의 명명 규칙과 prop 흐름 이해**가 중요  

--- 

##### 불변성이 왜 중요할까요

데이터를 변경하는 방법은 두 가지가 있음

- 데이터를 직접 변경하기
- 복사본의 데이터를 변경하기

이렇게 불변성을 이용하면 몇 가지 이점이 있음

- 이 앱의 시간 여행 기능에 해당하는, 특정 작업 취소 및 롤백 기능 구현이 쉬움
- 성능 최적화에 유리
- 예측 가능한 코드 작성 가능

##### 순서 정하기

이제 X가 아니라 교대로 O가 나올 수 있도록 만들기

다른 state를 만들어서 구현

```js
function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  // ...
}
```
```js
export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  return (
    //...
  );
}
```
클릭할 때마다 xIsNext가 true false를 왔다갔다 하면서, 교대로 X와 O가 나오게 함

그런데 이렇게 했더니 같은 사각형을 클릭해도 바뀜

클릭한 사각형에 이미 X나 O가 있는지 확인하면 해결

```js
function handleClick(i) {
  if (squares[i]) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```
사각형에 뭔가 있으면 바로 return, 이렇게 중복 클릭을 방지

<img src="./images/BoardWithXO.png" alt="XO가 있는 보드" width="100" />
X랑 O가 잘 보인다

##### 승자 결정하기

이제 X든 O든 3개가 이어지면 이겼다고 보여주기

```js
export default function Board() {
  //...
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

lines에 0,1,2 같은 가로, 0,3,6 같은 세로, 0,4,8 같은 대각선을 만들어주고 if문으로 3개가 이어졌는지 확인

이 함수는 Board 앞이든 뒤든 어디에 배치되도 상관없음

```js
function handleClick(i) {
  if (squares[i] || calculateWinner(squares)) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

클릭할 때마다 확인할 수 있게 넣어주고, 만약 승자가 결정되면 똑같이 바로 return

```jsx
export default function Board() {
  // ...
  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = "Winner: " + winner;
  } else {
    status = "Next player: " + (xIsNext ? "X" : "O");
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        // ...
  )
}
```

승자가 결정됐을 때 누가 이겼다고 표시하기 위해 `status` 추가

if문으로 Winner가 누군지 보여주고, 안 끝났을 때는 Next player가 누군지 보여주기

**구조 분해 할당**  
객체나 배열에서 데이터 전체가 아니라 일부만 필요할 때 사용할 수 있는 문법

```js
const arr = [1, 2, 3];

const [a, b, c] = arr;

console.log(a); // 1
console.log(b); // 2
console.log(c); // 3
```

배열에서 바로 값을 가져와서 변수에 할당함

```js
const user = {
  name: "Alice",
  age: 25,
};

const { name, age } = user;

console.log(name); // "Alice"
console.log(age);  // 25
```

객체에서도 바로 값을 가져와서 변수에 할당함

`const [a, b, c] = lines[i];`

위 예제에서 이렇게 사용됐음

#### 시간여행 추가하기

이제 게임에서 이전 수로 돌아갈 수 있도록 만들기

##### 이동 히스토리 저장하기

앞서 불변성을 위해 slice로 복사본을 만들어서 저장했음  
따라서 history라는 새로운 배열을 만들어서 거기에 과거의 수들을 저장

```js
[
  // Before first move
  [null, null, null, null, null, null, null, null, null],
  // After first move
  [null, null, null, null, 'X', null, null, null, null],
  // After second move
  [null, null, null, null, 'X', null, null, null, 'O'],
  // ...
]
```

##### 한 번 더 state 끌어올리기

과거에 두었던 수들의 목록을 만들기 위해 새로운 컴포넌트 `Game` 생성  

```jsx
function Board() {
  // ...
}

export default function Game() {
  return (
    <div className="game">
      <div className="game-board">
        <Board />
      </div>
      <div className="game-info">
        <ol>{/*TODO*/}</ol>
      </div>
    </div>
  );
}
```

이제 `Board` 컴포넌트에서 `Game` 컴포넌트로 `state`를 끌어올릴 거라서, `export default`를 `Game`에 붙여줌  

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  // ...
```

두었던 수들을 기록하기 위해 state 추가  

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];
  // ...
```

마지막 사각형의 배열을 읽어서 현재 수에 대한 사각형 렌더링

```jsx
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    // TODO
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
        //...
  )
}
```

게임을 업데이트할 때 호출할 `handlePlay` 생성  
`xIsNext`, `currentSquares`, `handlePlay`를 Board 컴포넌트에 props로 전달

`App.js`에서 `import`할 것도 `Board`가 아니라 `Game`으로 바꾸기

## 2025-04-10 6주차
### 틱택토 만들기
#### props를 통해 데이터 전달하기

- 사각형 클릭 시 값을 변경해야 하는 상황   
- 기존 방법으론 `<button>` 9개 다 복붙해야 해서 비효율적  
- React 컴포넌트 구조 활용해 중복 제거 가능

---

1. `Square` 컴포넌트 따로 만듦  
```jsx
function Square() {
  return <button className="square">1</button>;
}
```

2. `Board` 컴포넌트는 JSX로 `<Square />` 9개 렌더링  
   대문자로 시작해야 사용자 정의 컴포넌트로 인식됨

3. 이렇게 하면 숫자가 다 "1"로 나옴  
   → 각 칸에 다른 값 넣으려면 `props` 사용해야 함

---

4. `Square`에서 `props.value` 받아서 표시하도록 수정  
```jsx
function Square({ value }) {
  return <button className="square">{value}</button>;
}
```

5. `value` 변수로 인식되게 하려면 중괄호 `{}` 필요  
   → 그냥 `value` 쓰면 문자열 `"value"`로 나옴

   현재는 `Board`에서 value를 안 넘겨줘서 빈 보드로 보임

---

7. `Board`에서 `Square`에 각각 `value` prop 넘겨줌  
```jsx
<Square value="1" />
<Square value="2" />
...
```

8. 이제 다시 숫자 있는 보드가 보임  
→ props를 통해 값 전달, 컴포넌트 재사용 가능해짐

<img src="./images/BoardWithNumber.png" alt="숫자가 있는 보드" width="100" />
숫자 있는 보드

---

#### 사용자와 상호작용하는 컴포넌트 만들기

- Square를 클릭했을 때 동작 추가하려 함  
- 먼저 `handleClick` 함수 만들어 `onClick`으로 연결  
```jsx
function handleClick() {
  console.log('clicked!');
}
```

- 클릭 시 콘솔에 "clicked!" 뜰 거고, 여러 번 눌러도 콘솔 줄 안 늘고, 숫자만 증가함  
- 로컬 환경이면 크롬 기준 `Shift + Ctrl + J`로 콘솔 열어야 함

---

- 이제 클릭 상태 기억해서 `"X"` 표시하게 만들 계획  
- React의 `useState` 훅 사용함  
```js
import { useState } from 'react';
```

- Square 내부에서 state 선언  
```jsx
const [value, setValue] = useState(null);
```

- 더 이상 `props` 안 씀 → `Board`에서 `Square`에 넘기던 `value` prop 삭제

---

- 클릭하면 `value`를 `'X'`로 바꿈  
```jsx
function handleClick() {
  setValue('X');
}
```

- 버튼 안에는 `{value}` 그대로 사용. 클릭 시 "X" 표시됨

<img src="./images/BoardWithX.png" alt="X가 있는 보드" width="100" />
X가 있는 보드   

---

#### React 개발자 도구 

- React 개발자 도구로 컴포넌트의 props, state 확인 가능 [React Developer Tools](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)

---

#### 게임 완료하기

##### state 끌어올리기

지금까지는,
- `Square` 컴포넌트가 각자의 state를 갖고 있어서 한번에 확인하기 어려움
- 그러므로, state를 Square에서 Board로 끌어올리기

---

1. **`Board` 컴포넌트에서 state 관리
```js
const [squares, setSquares] = useState(Array(9).fill(null));
```
- 9칸짜리 `squares` 배열로 보드 상태를 관리 (모두 null로 초기화)

---

2. `Square`는 state를 갖지 않고 props로만 동작
```js
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

---

3. `Board`에서 Square에 prop으로 값 전달
```js
<Square value={squares[0]} onSquareClick={() => handleClick(0)} />
```
- `handleClick(i)` 호출을 화살표 함수로 감싸서 클릭 시에만 실행되게 함

---

4. `Board`에서 클릭 처리 함수 정의
```js
function handleClick(i) {
  const nextSquares = squares.slice();
  nextSquares[i] = "X";
  setSquares(nextSquares);
}
```
- `slice()`로 복사본 생성 → `X` 추가 → `setSquares()`로 state 업데이트

---

따라서,
- 모든 사각형의 상태를 하나의 곳(Board)에서 관리
- React 철학에 맞는 단방향 데이터 흐름 유지
- 리팩토링이나 디버깅이 쉬워짐

---

## 2025-04-03 5주차
#### 이벤트에 응답하기
component 내부에 event handler 함수를 선언하면 event에 응답할 수 있음   
onClick={handleClick}의 끝에 소괄호가 없음   
함수를 호출하지 말고 전달만 하면 됨   
React는 사용자가 버튼을 클릭할 때 이벤트 핸들러를 호출함
```js
export default function MyButton() {
  function handleClick() {
    alert("You clicked me!");
  }

  return <button onClick={handleClick}>Click me!</button>;
}0
```
다 적용한 MyButton.js의 모습

#### 화면 업데이트하기
컴포넌트가 특정 정보를 “기억”하여 표시하기를 원하는 경우가 종종 있음   
예를 들어 버튼이 클릭된 횟수를 세고 싶을 수 있음   
이렇게 하려면 컴포넌트에 state를 추가하면 됨   


먼저, React에서 useState를 가져오고,   
```js
import { useState } from 'react';
```
이제 컴포넌트 내부에 state 변수를 선언할 수 있음
```js
function MyButton() {
  const [count, setCount] = useState(0);
  // ...
```
useState로부터 현재 state (count)와 이를 업데이트할 수 있는 함수(setCount)를 얻을 수 있음   
이들을 어떤 이름으로도 지정할 수 있지만 [something, setSomething]으로 작성하는 것이 일반적임   

버튼이 처음 표시될 때는 useState()에 0을 전달했기 때문에 count가 0이 됨   
state를 변경하고 싶다면 setCount()를 실행하고 새 값을 전달
이 버튼을 클릭하면 카운터가 증가함   
```js
function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```
React가 컴포넌트 함수를 다시 호출함  
이번에는 count가 1이 되고, 그 다음에는 2가 될 것   

같은 컴포넌트를 여러 번 렌더링하면 각각의 컴포넌트는 고유한 state를 얻게 됨   
각 버튼을 개별적으로 클릭해 보기   

```js
import { useState } from "react";

export default function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return <button onClick={handleClick}>Clicked {count} times</button>;
}
```
다 적용한 MyButton.js의 모습, 아래는 적용된 이미지   
![버튼이 각각 클릭된 횟수를 저장한다.](./images/MyButton.png)   
버튼이 각각 클릭된 횟수를 저장함

각 버튼이 고유한 count state를 “기억”하고 다른 버튼에 영향을 주지 않는다는 점 기억하기

#### Hook 사용하기
use로 시작하는 함수가 Hook임   
useState는 React에서 기본 제공하는 내장 Hook임   
다른 내장 Hook은 공식 API 참고서에서 찾을 수 있음   
기존 Hook을 조합해서 직접 Hook 만들 수도 있음   
Hook은 일반 함수보다 더 제한적임   
컴포넌트나 다른 Hook의 최상단에서만 호출 가능함   
조건문이나 반복문 안에서 useState 쓰면 안 됨   
그렇게 하고 싶으면 새 컴포넌트로 분리해서 사용해야 함   

#### 컴포넌트 간에 데이터 공유하기

이전에는 `MyButton`마다 독립적인 `count` state가 있었음   
그래서 버튼을 클릭해도 **클릭한 버튼의 count만 증가**했음   

하지만 **두 개의 버튼이 같은 count를 공유해야 할 때**가 있음    
그러려면 **state를 공통 부모 컴포넌트로 올려야 함**    

이 예제에서는 `MyApp`이 부모 역할을 함 

**1. 처음 상태 (`MyApp`이 `count` 관리 X)**  
```jsx
export default function MyApp() {
  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return <button onClick={handleClick}>Clicked {count} times</button>;
}
```
이 상태에서는 **각 버튼이 독립적인 `count`를 가짐**  

**2. `count`를 `MyApp`에서 관리하도록 변경**  
```jsx
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}
```
`count`와 `handleClick`을 **props로 전달**해서 **모든 버튼이 같은 `count`를 공유하도록 변경**  

**3. `MyButton`에서 props 사용하도록 수정**
```jsx
function MyButton({ count, onClick }) {
  return <button onClick={onClick}>Clicked {count} times</button>;
}
```
`count`와 `onClick`을 **부모 컴포넌트(`MyApp`)에서 전달받아 사용함**  

**결과**  
![숫자가 같이 올라간다](./images/State.png)   
숫자가 같이 올라간다

버튼을 클릭하면 **부모(`MyApp`)의 `count`가 증가**  
모든 버튼이 **같은 `count`를 공유**  
이걸 **"state 끌어올리기 (lifting state up)"** 라고 함
```js
import MyButton from "./MyButton";
import { Button1, Button3 } from "./ButtonLib";
import AboutPage from "./AboutPage";
import Profile from "./Profile";
import "./App.css";
import ShoppingList from "./ShoppingList";
import { useState } from "react";

function CountState2({ count, onClick }) {
  return (
    <div>
      <button onClick={onClick}>Clicked {count} times, CountState2</button>
    </div>
  );
}

export default function App() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }
  return (
    <div>
      <h1>Hello React!</h1>
      <CountState2 count={count} onClick={handleClick} />
      <CountState2 count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
      &nbsp;&nbsp;
      <Button1 />
      &nbsp;
      <Button3 />
      <br />
      <AboutPage />
      <Profile />
      <ShoppingList />
    </div>
  );
}
```
![](./images/CountState2.png)   
이건 App.js 내부에 CountState2로 정의한 것

### **Function Component vs Class Component 비교**  

| **비교 항목**      | **Function Component** | **Class Component** |
|-------------------|----------------------|---------------------|
| **작성 방식**      | `function` 키워드 사용 | `class` 키워드 사용 |
| **State 사용**    | `useState` Hook 사용  | `this.state` 사용 |
| **이벤트 핸들링** | `onClick={handleClick}` | `this.handleClick = this.handleClick.bind(this);` 필요 |
| **라이프사이클 메서드** | `useEffect` Hook 사용 | `componentDidMount`, `componentDidUpdate`, `componentWillUnmount` |
| **코드 길이**      | 짧고 간결함 | 상대적으로 길고 복잡함 |
| **성능**          | React 16.8 이후 더 최적화됨 | 상대적으로 무거움 |
| **사용 권장 여부** | 추천 (최신 React 스타일) | 잘 사용하지 않음 (옛날 방식) |

### 틱택토 만들기

여기서 배울 내용
- 환경 설정 : 개발환경 설정
- 개요 : Components, props, state에 대해 학습
- 게임 완료하기 : React에서 가장 많이 사용되는 기술 학습
- 시간여행 추가하기 : React만의 강점에 대해 학습

만들 것 :
- 상호작용할 수 있는 틱택토 게임
- 두 사람이 번갈아 빈칸에 말을 놓아서 3개의 말을 한 줄로 완성하면 승리
- 화면 오른쪽에 번호가 있는 목록을 생성
- 목록에 게임에서 발생한 착수를 모두 기록하며 게임이 진행될 때 동시에 업데이트

먼저 정사각형부터 만들기
```js
export default function Square() {
  return <button className="square">1</button>;
}
```



## 2025-03-27 4주차
### 빠르게 시작하기
#### Component 생성 및 중첩 방법
component는 고유한 로직과 모양을 가진 UI의 일부   
component는 버튼처럼 작을 수도 있고, 전체 페이지처럼 클 수도 있음   
component는 마크업을 반환(return)하는 JavaScript 함수   
Nesting은 css 선택자의 중첩 구조를 생각하기   

export default 키워드는 파일 내의 component 중 기본 component를 지정   

export default와 export의 차이   
Named Exports   
하나의 파일 안에 여러 개의 component가 있을 때 사용   

Default Exports   
하나의 파일 안에서 하나의 component만 내보내는 경우 사용    
component를 사용하는 쪽에서는 어떤 이름을 사용해도 상관 없음    
```html
App.js
import MyButton from "./MyButton";

export default function App() {
  return (
    <div>
    <h1>Hello React!</h1>
    <MyButton/>
    </div>
  );
}
```
이것과,
```html
MyButton.js
export default function MyButton() {
    return (
        <button>Click me!</button>
    );
}
```
이 파일이 있을 때, import 이름 from "MyButton.js의 상대 위치 경로"로 App.js에 넣어줄 수 있음   

```html
ButtonLib.js
function Button1() {
    return (
        <button>Button1</button>
    )
}
function Button2() {
    return (
        <button>Button2</button>
    )
}
function Button3() {
    return (
        <button>Button3</button>
    )
}
export { Button1, Button2, Button3 };
```
여러 개를 export 할 때 예시   

component 이름은 항상 대문자로 시작   

#### 마크업과 스타일을 추가하는 방법
JSX로 마크업 작성하기   
React 프로젝트에서는 편의성을 위해 JSX를 사용   
JSX는 HTML보다 더욱 엄격한 문법을 적용   
JSX에선 <br/>처럼 싱글 태그라도 닫기   
React에서는 component 여러 개를 JSX 태그로 반환 가능   
대신 <div>...</div>처럼 wrapping 해주기   

```html
AboutPage.js
export default function AboutPage() {
    return (
        <div>
            <h1>About</h1>
            <p>Hello there.<br/>How do you do?</p>
        </div>
    );
}

<div>...</div>로 묶어주기
```

스타일 추가하기   

React에서는 className으로 CSS 클래스를 지정   
className은 HTML의 class 속성과 동일한 방식으로 동작   
CSS 규칙은 별도의 CSS 파일에 작성, React는 CSS 파일을 추가하는 방법을 규정하지는 않음   
가장 간단한 방법은 HTML에 <link> 태그 추가하는 법 그러나 정적 페이지를 수정해야 하므로 비추천   

#### 데이터를 표시하는 방법
데이터 표시하기   

JSX를 사용하면 JavaScript에 마크업을 넣을 수 있음   
JSX 코드 내에서 JavaScript로 <b>탈출</b>하여 변수나 표현식을 사용   
이 방법을 <b>Escape Back</b>이라고 함   
중괄호를 사용해서 변수나 표현식을 사용자에게 표시   
```html
Profile.js
const user = {
    name: "Hedy Lamarr",
    imageUrl: "https://i.imgur.com/yXOvdOSs.jpg",
    imageSize: 90,
};

export default function Profile() {
    return (
        <div>
            <h1>{user.name}</h1>
            <img
            src={user.imageUrl}
            alt={'photo of ' + user.name}
            style={{width: user.imageSize}}
            />
        </div>
    );
}
```
이런 식으로 중괄호를 사용해서 src, alt, style에 user라는 객체의 정보를 넣을 수 있음   

css 파일 가져오기   
```html
Profile.js
import './Profile.css';

const user = {
    name: "Hedy Lamarr",
    imageUrl: "https://i.imgur.com/yXOvdOSs.jpg",
    imageSize: 90,
};

export default function Profile() {
    return (
        <div>
            <h1>{user.name}</h1>
            <img className='avatar'
            src={user.imageUrl}
            alt={'photo of ' + user.name}
            style={{width: user.imageSize}}
            />
        </div>
    );
}
```
import로 css 파일 가져오기   

```css
Profile.css
.avatar {
    border-radius: 50%;;
}
```
임포트 된 css 파일   

똑같이 App.js 스타일도 App.css를 수정해서 적용할 수 있음   

#### 조건부 렌더링과 목록 렌더링 방법

React에서 조건문을 작성하는 데에는 특별한 문법이 필요하지 않음   
일반적인 if-else, 삼항 연산자, 이항 연산자 모두 JSX 내부에서 동작   
```html
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}
return (
  <div>
    {content}
  </div>
);
```
예를 들면, 위 코드는 isLoggedIn이 True면 AdminPanel을 보여주고, False면 LoginForm을 보여주는 것

```html
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```
삼항 연산자로 더 간단하게 할 수 있음

```html
<div>
  {isLoggedIn && <AdminPanel />}
</div>
```
else 분기가 필요하지 않으면 &&로 더 간단하게 할 수 있음
리스트 렌더링하기

컴포넌트 리스트를 렌더링 하기 위해서는 for문 및 map() 함수같은 JavaScript 기능 사용
```html
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```
이걸 map()을 써서 li 항목으로 바꿈
```html
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

li에 key 속성을 씀   
목록의 각 항목에 대해, 형제 항목 사이에서 해당 항목을 고유하게 식별하는 문자열 또는 숫자를 전달   
React는 나중에 항목을 삽입, 삭제 또는 재정렬할 때 어떤 일이 일어났는지 알기 위해 key를 사용
이것을 key props라고 함

#### 이벤트에 응답하고 화면을 업데이트하는 방법
#### Component간 정보 교류
## 2025-03-20 3주차
### React Project의 구조 
my-project: project 이름
node_modules/: 프로젝트에 설치된 모든 패키지들이 저장되는 폴더, npm install하면 여기에 저장됨
public/ 정적 파일을 저장   
  &nbsp;&nbsp;&nbsp;&nbsp;favicon.ico:브라우저 탭에 표시되는 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;index.html: React 앱이 마운트되는 html 파일 div id="root"가 포함   
  &nbsp;&nbsp;&nbsp;&nbsp;logo192.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;logo512.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;manifest.json: PWA 관련 설정 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;robots.txt: 검색 엔진 크롤러의 접근을 제어하는 파일   
src/: React 앱의 주요 코드가 위치하는 폴더   
  &nbsp;&nbsp;&nbsp;&nbsp;App.css: App.js에 적용되는 스타일   
  &nbsp;&nbsp;&nbsp;&nbsp;App.js: 메인 컴포넌트   
  &nbsp;&nbsp;&nbsp;&nbsp;App.test.js: Jest를 사용한 기본 테스트 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;index.css: 전역 스타일   
  &nbsp;&nbsp;&nbsp;&nbsp; index.js: React 앱의 진입점. ReactDOM.createRoot를 사용하여 App.js를 렌더링함       
  &nbsp;&nbsp;&nbsp;&nbsp;logo.svg: 기본 로고 이미지   
  &nbsp;&nbsp;&nbsp;&nbsp; reportWebVitals.js: 성능 측정 관련 코드   
  &nbsp;&nbsp;&nbsp;&nbsp; setupTests.js: 테스트 환경 설정 파일   
.gitignore: Git에 추가하지 않을 파일 목록을 정의   
package-lock.json: 프로젝트에 설치된 정확한 패키지 버전과 의존성 정보를 기록    
package.json: 패키지(라이브러리)와 프로젝트 정보를 관리하는 파일    
README.md:    
&nbsp;&nbsp;&nbsp;&nbsp;  프로젝트 개요: 프로젝트가 무엇을 하는지 간략히 설명    
&nbsp;&nbsp;&nbsp;&nbsp;  설치 방법: 프로젝트를 실행하려면 어떻게 해야 하는지, 필요한 환경은 무엇인지 등    

#### node_modules/
&nbsp;&nbsp;&nbsp;&nbsp;초기 node_module 및 새로 설치하는 패키지가 저장됨. 프로젝트에서 필요한 라이브러리들을 로컬에 저장.
##### node_modules/ 폴더를 Git에 포함하지 않는 이유
폴더 크기가 너무 크고, 패키지마다 버전이 다를 수 있음   
package.json과 package-lock.json(또는 yarn.lock)만 있으면 npm install로 다시 설치할 수 있음   
그래서 .gitignore 파일에 아래처럼 추가해서 node_modules/ 폴더를 제외하는 게 일반적   
#### src/
React 프로젝트의 주요 코드가 위치하는 디렉토리   
개발 하면서 대부분의 작업이 이루어짐

##### &nbsp;&nbsp;&nbsp;&nbsp; App.js
메인 컴포넌트로 필요한 서브 컴포넌트를 모아 관리   
출력을 위해서 index.js로 전달
##### &nbsp;&nbsp;&nbsp;&nbsp; App.css
App.js에 적용되는 스타일   
일반적으로 App.js 컴포넌트의 스타일을 정의
##### &nbsp;&nbsp;&nbsp;&nbsp; index.css
index.css는 React 애플리케이션의 전역(Global) 스타일을 설정하는 CSS 파일   
##### &nbsp;&nbsp;&nbsp;&nbsp; index.js
index.js는 React 애플리케이션의 시작점(entry point) 역할   
즉, React 앱을 실행할 때 가장 먼저 실행되는 파일   

### 의존성 관리와 package.json
#### 의존성?
의존성(Dependencies) 이란, 프로젝트가 실행되기 위해 필요한 외부 라이브러리 또는 모듈에 의존하여 동작하는 관계   
예시로 React 프로젝트에서는 react, react-dom 같은 라이브러리가 필요하고, Express 서버에서는 express, mongoose 같은 라이브러리가 필요   
따라서 이러한 의존성들을 체계적으로 관리할 필요가 있음, 이것이 의존성 관리(Dependency Management)   
#### package.json?
package.json은 Node.js 프로젝트의 설정 파일로, 프로젝트의 정보와 함께 사용하는 의존성 목록을 저장하는 역할   
package.json 주요 역할
1. 프로젝트의 이름(name), 버전(version), 설명(description) 등의 정보를 저장   
2. 프로젝트에서 사용 중인 의존성(Dependencies) 목록을 저장   
3. npm 또는 yarn 명령어 실행 시 사용할 스크립트(Scripts) 명령어 정의

##### package.json의 내용
dependencies : 실제 코드에서 필요한 패키지,	예시로 express, react    
devDependencies :	개발 환경에서만 필요한 패키지, 예시로 (테스트, 빌드 도구 등)   
peerDependencies : 필요한 라이브러리만, 직접 설치하지 않고 사용자에게 설치를 맡기는 경우
optionalDependencies : 설치가 실패하더라도 프로젝트 실행에 꼭 필요하지 않은 패키지, 해당 패키지가 설치되지 않아도 프로젝트가 정상적으로 작동해야 하는 경우 사용

##### package.json을 유지해야 하는 이유
프로젝트의 의존성 정보 제공
버전 범위 설정 가능
스크립트와 메타데이터 저장
새로운 패키지 설치 및 관리

#### 의존성을 왜 관리하나요?
##### 설치가 쉬움
npm install 또는 yarn install 한 줄로 모든 의존성을 자동 설치 가능   
특정 버전의 라이브러리를 쉽게 업데이트할 수 있음   
##### 일관된 개발환경 유지
개발자마다 다른 버전의 라이브러리를 사용하면 오류 발생 가능성 증가   
package.json에 의존성을 명확히 정의하면, 팀원들이 동일한 환경에서 개발 가능   
##### 배포 환경과 개발 환경 분리
개발 중에만 필요한 패키지(devDependencies)와 배포 시 필요한 패키지(dependencies)를 구분하여 최적화 가능   
##### 불필요한 코드 관리 (최적화)
프로젝트에서 사용하지 않는 패키지가 남아 있으면 용량 증가 & 성능 저하   
package.json만 수정해서 쉽게 관리 가능

| 구분            | package.json                                  | package-lock.json |
|---------------|---------------------------------|----------------------------------|
| 역할          | 프로젝트 기본 정보, 의존성 정의     | 설치된 패키지의 정확한 버전 고정  |
| 내용          | 패키지 이름, 버전, dependencies, scripts 등 포함  | 의존성 트리 및 패키지의 정확한 버전 기록  |
| 업데이트      | 직접 수정 가능 | 직접 수정하지 않으며, npm install 시 자동 업데이트  |
| 버전 관리      | `^1.0.0`처럼 범위 지정 가능       | `1.0.0`처럼 고정된 버전 유지  |
| Git 관리      |  |  |

#### node module의 재설치
node module을 다시 설치해야 하는 경우   
1. 팀 작업을 하면서 Github으로부터 프로젝트 파일을 clone 했을 경우
2. 개인이 자신의 프로젝트를 다른 PC 등에서 clone을 받아 계속 개발해야 하는 경우
3. 프로젝트에 문제가 생겨서 node_module을 다시 설치해야 하는 경우

##### clone을 받은 프로젝트의 경우   
   다음 명령을 실행하면 package.json과 package-lock.json을 참고하여 패키지를 다시 설치 npm install    
   node_modules 디렉토리가 자동 생성   
   설치가 끝나면 프로젝트를 실행 시켜서 정상 동작 확인   

##### 개인이 자신의 프로젝트를 다른 PC 등에서 clone을 받아 계속 개발해야 하는 경우
   rm -rf node_modules package-lock.json node_modules랑 package-lock.json 삭제   
   npm cache clean --force 캐시도 깔끔히 삭제   
   npm install 다시 설치   

##### package-lock.json을 삭제하는 이유
   1. package-lock.json이 손상되었거나, 잘못된 의존성이 있을 때
      예시로 패키지를 여러번 업데이트하면서 충돌이 발생한 경우
      수동으로 package.json을 수정해서 package-lock.json에 영향을 미친 경우
   3. 최신 버전의 패키지를 다시 받고 싶을 때
   4. 팀 프로젝트에서 다른 팀원이 이상한 상태로 package-lock.json을 업데이트 했을 때

### React 홈페이지
文A 버튼 눌러서 한국어로 바꾸면 쉽게 볼 수 있음   
버전 19가 최신임 react.dev   

#### 개요
React는 component 단위로 개발하여 레고를 조립하듯이 앱을 완성   
component는 작은 기능을 실행할 수 있는 하나의 모듈   
공식 사이트의 홈에는 component가 어떻게 사용되는지 설명하고 있음   
component의 조립 과정이 중요   

#### Component를 사용한 유저 인터페이스 생성
React를 사용하면 component라고 하는 개별 조각으로 사용자 인터페이스를 구축할 수 있음   
Video, Thumbnail 및 LikeButton이라는 react component를 생성해서 조립   

#### Component를 작성하는 JavaScript와 Markup
React component는 JavaScript 함수   
조건에 따라 화면을 다르게 표시하려면 if문   
목록을 표시하려면 map()   
→ JS를 이미 알고 있으면 더 쉽게 배울 수 있음   

#### 필요한 곳에 상호작용 기능 추가
React component는 데이터를 수신하고, 화면에 표시해야 하는 내용을 반환   
사용자의 입력을 받아 새로운 데이터를 component에 전달 가능   
이때 React는 상호작용을 통해 얻은 새 데이터로 화면을 업데이트    
 
React 개발은 작은 component를 합쳐서 하나의 새로운 component를 만드는 식   

#### full-stack App 개발을 도와주는 React Framework
React는 라이브러리이기 때문에 component를 조합할 수는 있지만, 라우팅 및 데이터 가져오기 방법 등을 규정하진 않음   
React로 전체 앱을 빌드하려면 Next.js 또는 Remix와 같은 full-stack React Framework를 사용하는 것이 좋음   
사이트의 confs/[slug].js는 Next.js에서 제공하는 routing 방법 중 하나   
React도 하나의 아키텍처   
아키텍처 : 소프트웨어의 구성요소들 사이에서 유기적 관계를 표현하고 소프트웨어의 설계와 업그레이드를 통제하는 지침과 원칙     
따라서 이를 구현하는 Framework를 사용하면, 서버에서 실행되거나 혹은 빌드 중에도 비동기 component에서 데이터를 가져올 수도 있음     
또한 파일이나 데이터베이스에서 데이터를 읽어와서 대화형 component에 전달할 수도 있음    

#### 모든 플랫폼에서 최고의 성능을 발휘하는 React
React를 사용하면 동일한 기술로도 웹 앱과 네이티브 앱을 모두 구축할 수 있음   
각 플랫폼의 고유한 강점을 활용하여 모든 플랫폼에 잘 어울리는 인터페이스를 구현할 수 있음   

사람들은 웹 앱 페이지가 빠르게 로드되는 것을 원함   
React를 사용하면 서버에서 데이터를 가져오는 동안에도 HTML을 스트리밍하는 것을 시작할 수 있기 때문에, JS 코드가 로드되기 전에 콘텐츠를 점진적으로 채울 수 있음   
또한 클라이언트 측에서는 표준 웹 API를 사용해서, 렌더링 도중에도 UI를 반응하도록 할 수 있음   
이런 동작들은 사람들이 원하는 빠른 렌더링을 도와줌   

##### 진정한 네이티브 UX
사람들은 네이티브 앱이 자신의 플랫폼과 같은 모양과 느낌을 주길 원함
React Native와 Expo를 사용하면 Android, iOS 등을 위한 앱을 React로 빌드할 수 있음
앱이 네이티브처럼 보이고 느껴지는 이유는 UI가 네이티브이기 때문
즉 Web view가 아니라 플랫폼에서 제공하는 Android 및 iOS view를 사용
React를 사용하면 웹 개발자도 네이티브 개발자도 될 수 있음
사용자 경험의 희생 없이 다양한 플랫폼에 앱을 출시할 수 있음
기업에서는 플랫폼 간의 장벽을 허물고, 전체 기능을 협업으로 개발할 수 있는 팀을 구성할 수 있음

<hr>
## 2025-03-13 2주차
## 알아두어야 할 명령어

ls -al : ls는 'list' 현재 디렉토리의 파일과 폴더 목록을 출력, -al에서 a는 'all'으로 숨김 파일도 보여줌, l은 'long format'으로 파일의 자세한 정보(권한, 소유자, 크기, 수정 날짜 등)를 표시   
rm -rf : rm는 'remove' 파일이나 폴더를 삭제, -rf에서 r은 'recursive'으로 디렉토리와 그 안의 파일들을 재귀적으로 삭제, f는 'force'으로 강제 삭제를 의미   
git init : 새로운 Git 저장소(Repository)를 생성하기, 현재 디렉토리를 Git으로 관리할 수 있게 하는 명령어   

예시
$ mkdir my_project   
$ cd my_project   
$ git init   
Initialized empty Git repository in /path/to/my_project/.git/   

mkdir은 'Make Directory', 디렉토리를 만드는 명령어 my_project라는 디렉토리를 생성   
cd는 'Change Directory', 터미널에서 현재 작업 중인 디렉토리를 변경   
초기화하려면 git init, 기존 커밋 기록 등 완전히 삭제하려면 rm -rf .git   

vscode를 이용하고 있다고 가정할 때, 새로운 걸 생성하거나, 삭제를 하는 것은 디렉토리에 변화를 일으킨 것, 그 바뀐 개수를 숫자로 Source Control에 표시   
commit할 때 제목은 52바이트까지, 뭔가 설명할 거 쓰려면 엔터 두 번   
commit할 때 필요한 건 user.name, user.email.   

$ git config user.name '자기 이름'   
$ git config user.email '자기 이메일'로 git에 이름과 이메일을 등록    
$ git config list는 Git의 설정값을 확인하는 명령어     
내용이 길게 나올텐데, q를 누르면 빠져나올 수 있다.   

## github에 publish

로그인이 안되어 있으면 오류 뜸 github에 로그인 해야 함   
Publish Branch 누르고 Public으로 설정     
README.md에서 md 뜻은 마크다운

## React?

### Node.js란?

React는 Node.js에서 파생된 것   
Apache 서버는 요청을 처리할 때 블로킹 방식으로 동작한다는 문제가 존재, 따라서 비동기 방식의 서버를 개발   
Javascript, 크롬의 v8엔진을 통해 제작   

#### Node.js는 어디에 활용?

웹 서버 (Express.js, Nest.js) → REST.API, GraphQL API 개발   
실시간 애플리케이션 (Socket.io) → 채팅, 실시간 알림, 스트리밍   
서버리스(Serverless) 환경 → AWS Lambda 같은 FaaS(Function as a Service)

#### Node.js가 인기를 끄는 이유
빠른 성능 : V8 엔진 기반 + 비동기 논 블로킹 방식으로 고성능 처리 가능   
JavaScript 풀스택 개발 : 프론트엔드와 백엔드를 같은 언어로 개발 가능   
활발한 생태계 : npm을 통해 다양한 패키지 사용이 가능   
실시간 애플리케이션에 강함 : WebSocket, Socket.io 지원   
마이크로 서비스 및 서버리스 환경과의 조화로운 연동

#### Node.js는 앞으로도 계속 발전할까?
Deno의 등장 : Node.js 창시자가 만든 Deno가 경쟁자로 떠오르고 있지만, Node.js의 생태계가 훨씬 크고 안정적   
ES 모듈 지원 강화 : 기존의 CommonJS에서 ES 모듈로의 전환 진행 중   
클라우드 및 서버리스 환경 확대 : AWS, Azure, GCP   

#### Node.js의 장단점
장점   
1. 비동기 논 블로킹 I/O로 높은 성능 제공
2. JavaScript 풀스택 개발이 가능하여 생산성이 향상됨
3. npm의 방대한 생태계를 활용 가능
4. 경량 서버 개발에 적합
5. 실시간 데이터 처리가 강력함

단점
1. CPU 집약적인 작업에 부적합 : 싱글 스레드 기반이라 멀티스레딩 성능이 부족함   
2. 콜백 지옥 문제 : 해결책으로 async/await과 Promise 사용   
3. 보안 취약점 : npm 패키지의 보안 문제가 자주 발생   

### React의 진화
Facebook에서 조던 윌케가 개발   
복잡한 UI를 쉽게 관리하기 위해 만들어짐  
React 0.3 (2013): 컴포넌트 기반 아키텍처 도입   
React 15 (2016): 성능 개선과 효율적인 렌더링   
React 16 (2017): Fiber 아키텍처 도입으로 비동기 렌더링 가능   
React 16.8 (2019): Hooks 도입으로 함수형 컴포넌트에서 상태 관리와 사이드 이펙트 처리 가능   
React 17 (2020): 주요 변경 사항 없이 업그레이드 용이성 개선   
React 18 (2024): Concurrent Mode 도입, React Server Components 지원   
2024년 12월 5일 19.0.0 버전이 공개됨   
React의 주 버전은 2, 3년에 한 번씩, 부 버전은 1년에 수차례 업그레이드   
React는 front end 개발자의 필수 과목   
공식문서를 통해 공부할 예정   

### React 개발환경 구축
Node.js(LTS) 다운로드   
node -v를 통해 버전 확인   
npm -v, npx -v : Node Package Manager, Node Package eXecute의 버전 확인   
node 설치하면 npm과 npx 자동 설치   
working project 만들기   
npx create-react-app '프로젝트 이름'으로 설치     

### React Project의 구조 
my-project: project 이름
node_modules/: 프로젝트에 설치된 모든 패키지들이 저장되는 폴더, npm install하면 여기에 저장됨
public/ 정적 파일을 저장   
  &nbsp;&nbsp;&nbsp;&nbsp;favicon.ico:브라우저 탭에 표시되는 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;index.html: React 앱이 마운트되는 html 파일 div id="root"가 포함   
  &nbsp;&nbsp;&nbsp;&nbsp;logo192.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;logo512.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;manifest.json: PWA 관련 설정 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;robots.txt: 검색 엔진 크롤러의 접근을 제어하는 파일   
src/: React 앱의 주요 코드가 위치하는 폴더   
  &nbsp;&nbsp;&nbsp;&nbsp;App.css: App.js에 적용되는 스타일   
  &nbsp;&nbsp;&nbsp;&nbsp;App.js: 메인 컴포넌트   
  &nbsp;&nbsp;&nbsp;&nbsp;App.test.js: Jest를 사용한 기본 테스트 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;index.css: 전역 스타일   
 &nbsp;&nbsp;&nbsp;&nbsp; index.js: React 앱의 진입점. ReactDOM.createRoot를 사용하여 App.js를 렌더링함       
  &nbsp;&nbsp;&nbsp;&nbsp;logo.svg: 기본 로고 이미지   
 &nbsp;&nbsp;&nbsp;&nbsp; reportWebVitals.js: 성능 측정 관련 코드   
 &nbsp;&nbsp;&nbsp;&nbsp; setupTests.js: 테스트 환경 설정 파일   
.gitignore: Git에 추가하지 않을 파일 목록을 정의   
package-lock.json: 프로젝트에 설치된 정확한 패키지 버전과 의존성 정보를 기록    
package.json: 패키지(라이브러리)와 프로젝트 정보를 관리하는 파일    
README.md:    
&nbsp;&nbsp;&nbsp;&nbsp;  프로젝트 개요: 프로젝트가 무엇을 하는지 간략히 설명    
&nbsp;&nbsp;&nbsp;&nbsp;  설치 방법: 프로젝트를 실행하려면 어떻게 해야 하는지, 필요한 환경은 무엇인지 등    
 &nbsp;&nbsp;&nbsp;&nbsp; 사용법: 프로젝트를 어떻게 사용하는지, 주요 기능은 무엇인지    
&nbsp;&nbsp;&nbsp;&nbsp;  기여 방법: 프로젝트에 기여하는 방법, 코드 작성 규칙 등    
 &nbsp;&nbsp;&nbsp;&nbsp; 라이센스: 프로젝트의 라이센스 정보    
  기타 정보: 프로젝트의 버전, 개발자 연락처, FAQ 등    
