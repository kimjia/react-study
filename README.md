# 리액트를 다루는 기술

# **3장** 컴포넌트

- ## 클래스형 컴포넌트

```javascript
import { Component } from "react";

class App extends Component {
  render() {
    const name = "react";
    return <div className="react">{name}</div>;
  }
}

export default App;
```

- ## 함수형 컴포넌트

```javascript
const MyComponent = () => {
  return <div>my component</div>;
};

export default MyComponent;
```

- ## props

  부모 컴포넌트에서만 설정 가능  
  JSX 내부에서 {props.name} 형태로 사용  
  컴포넌트 내에서 defaultProps 설정

- ## state

  컴포넌트 내부에서 바뀔 수 있는 값

  - ### 클래스형 컴포넌트(this.setState)

  ```javascript
  import React, { Component } from "react";

  class Counter extends Component {
    state = {
      number: 0,
      fixedNumber: 0,
    };
    render() {
      const { number, fixedNumber } = this.state;
      // state 를 조회 할 때에는 this.state 로 조회합니다.
      return (
        <div>
          <h1>{number}</h1>
          <h2>바뀌지 않는 값: {fixedNumber}</h2>
          <button
            // onClick 을 통하여 버튼이 클릭됐을 때 호출 할 함수를 지정합니다.
            onClick={() => {
              this.setState(
                {
                  number: number + 1,
                },
                () => {
                  console.log("방금 setState 가 호출되었습니다.");
                  console.log(this.state);
                }
              );
            }}
          >
            +1
          </button>
        </div>
      );
    }
  }

  export default Counter;
  ```

  - ### 함수형 컴포넌트(useState)

  ```javascript
  import { useState } from "react";

  const Say = () => {
    const [message, setMessage] = useState("");
    const onClickEnter = () => setMessage("안녕하세요!");
    const onClickLeave = () => setMessage("안녕히가세요!");
    return (
      <div>
        <button onClick={onClickEnter}>입장</button>
        <button onClick={onClickLeave}>퇴장</button>
        <h1>{message}</h1>
      </div>
    );
  };

  export default Say;
  ```

  <br>

# **4장** 이벤트 핸들링

- ## onChange, onClick, onKeyPress

```javascript
import React, { useState } from "react";

const EventPractice = () => {
  const [form, setForm] = useState({
    username: "",
    message: "",
  });

  const { username, message } = form;

  const onChange = (e) => {
    setTimeout(() => console.log(e), 500);
    const nextForm = {
      ...form, // 기존의 form 내용을 이 자리에 복사 한 뒤
      [e.target.name]: e.target.value, // 원하는 값을 덮어씌우기
    };
    setForm(nextForm);
  };

  const onClick = () => {
    alert(username + ": " + message);
    setForm({
      username: "",
      message: "",
    });
  };

  const onKeyPress = (e) => {
    if (e.key === "Enter") {
      onClick();
    }
  };
  return (
    <div>
      <h1>이벤트 연습</h1>
      <input
        type="text"
        name="username"
        placeholder="유저명"
        value={username}
        onChange={onChange}
      />
      <input
        type="text"
        name="message"
        placeholder="아무거나 입력해보세요"
        value={message}
        onChange={onChange}
        onKeyPress={onKeyPress}
      />
      <button onClick={onClick}>확인</button>
    </div>
  );
};
export default EventPractice;
```

<br>

# **5장** ref: DOM에 이름 달기

- ## ref

  DOM 요소에 접근할 때 사용  
   **_클래스형 컴포넌트에 사용_** (함수 컴포넌트에서는 Hooks 사용)

  - ### 콜백 함수를 통한 ref 설정

  ```javascript
  <input
    ref={(ref) => {
      this.input = ref;
    }}
  />
  ```

  this.input은 앞으로 input 요소의 DOM을 가리킴

  - ### createRef를 통한 ref 설정

  ```javascript
  import { Component } from "react";

  class RefSample extends Component {
    input = React.createRef();

    handleFocus = () => {
      this.input.current.focus();
    };

    render() {
      return (
        <div>
          <input ref={this.input} />
        </div>
      );
    }
  }

  export default RefSample;
  ```

  컴포넌트 내부에서 멤버 변수로 React.createRef()를 실행하고,  
   해당 멤버 변수를 ref를 달고자 하는 요소에 ref props로 넣어줌  
   이 DOM에 접근하려면 **_this.input.current_** 를 조회하면 됨.  
   **_뒷부분에 꼭 .current 넣어줘야 함_**
  <br>
  <br>

- ## 컴포넌트에 ref 달고 내부 메서드 사용

```javascript
import React, { Component } from "react";
import ScrollBox from "./ScrollBox";
class App extends Component {
  render() {
    return (
      <div>
        <ScrollBox ref={(ref) => (this.scrollBox = ref)} />
        <button onClick={() => this.scrollBox.scrollToBottom()}>
          맨 밑으로
        </button>
      </div>
    );
  }
}
export default App;
```

scrollToBottom() 함수는 Scrollbox 컴포넌트 내부에 정의된 함수  
부모 컴포넌트에서 해당 함수를 실행하도록 할 수 있음  
onClick={this.scrollBox.scrollToBottom} 으로 작성하면 컴포넌트가 처음 렌더링 될 때 this.scrollBox 값이 undefined이므로 this.scrollBox.scrollToBottom을 읽어오는 과정에서 오류 발생  
화살표 함수 문법을 사용하면 버튼을 누를 때 이미 한 번 렌더링을 해서 this.scrollBox가 설정되어 있어서 오류 발생하지 않음

<br>

# **6장** 컴포넌트 반복

- ## 자바스크립트 배열의 map()함수

  ```javascript
  const IterationSample = () => {
    const names = ["눈사람", "얼음", "눈", "바람"];
    const nameList = names.map((name) => <li>{name}</li>);
    return <ul>{nameList}</ul>;
  };
  ```

  key prop이 없다는 경고 메시지가 나타남

  - ### key 설정

  배열의 각 원소의 고유값인 key를 지정해줘야 함

  ```javascript
  const IterationSample = () => {
    const names = ["눈사람", "얼음", "눈", "바람"];
    const nameList = names.map((name, index) => <li key={index}>{name}</li>);
    return <ul>{nameList}</ul>;
  };
  ```

  index를 키값으로 설정하는 것은 리렌더링이 비효율적.

  ```javascript
  const [names, setNames] = useState([
    { id: 1, text: "눈사람" },
    { id: 2, text: "얼음" },
    { id: 3, text: "눈" },
    { id: 4, text: "바람" },
  ]);

  const namesList = names.map((name) => <li key={name.id}>{name.text}</li>);
  return <ul>{namesList}</ul>;
  ```

  고유 id 값을 지정해서 key값으로 사용

  - ### 데이터 추가(concat), 제거(filter) 기능

  ```javascript
  const IterationSample = () => {
    const [names, setNames] = useState([
      { id: 1, text: "눈사람" },
      { id: 2, text: "얼음" },
      { id: 3, text: "눈" },
      { id: 4, text: "바람" },
    ]);
    const [inputText, setInputText] = useState("");
    const [nextId, setNextId] = useState(5);

    const onChange = (e) => setInputText(e.target.value);

    const onClick = () => {
      const nextNames = names.concat({
        id: nextId,
        text: inputText,
      });
      setNextId(nextId + 1);
      setNames(nextNames);
      setInputText("");
    };

    const onRemove = (id) => {
      const nextNames = names.filter((name) => name.id !== id);
      setNames(nextNames);
    };

    const namesList = names.map((name) => (
      <li key={name.id} onDoubleClick={() => onRemove(name.id)}>
        {name.text}
      </li>
    ));
    return (
      <>
        <input value={inputText} onChange={onChange} />
        <button onClick={onClick}>추가</button>
        <ul>{namesList}</ul>
      </>
    );
  };
  ```

<br>

# **7장** 컴포넌트의 라이프사이클 메서드

- ## 라이프사이클(클래스형 컴포넌트)

  - ### 마운트

    DOM이 생성되고 웹 브라우저상에 나타남

    ```
    컴포넌트 만들기 -> constructor -> getDerivedStateFromProps ->
    render -> componentDidMount
    ```

    - componentDidMount: 컴포넌트가 웹 브라우저상에 나타난 후 호출하는 메서드

  - ### 업데이트

    컴포넌트 정보를 업데이트(리렌더링)

    - 업데이트되는 경우
      1. props가 바뀔 때
      2. state가 바뀔 때
      3. 부모 컴포넌트가 리렌더링 될 때
      4. this.forceUpdate로 강제로 렌더링을 트리거할 때

    ```
    업데이트 발생요인 -> GetDerivedStateFromProps ->
    shouldComponentUpdate (true: render / false: 작업취소) -> render ->
    getSnapshotBeforeUpdate -> 웹 브라우저상의 실제 DOM 변화 ->
    componentDidUpdate

    ```

  - ### 언마운트
    컴포넌트를 DOM에서 제거
    ```
    언마운트하기 -> componentWillUnmount
    ```
    - componentWillUnmount: 컴포넌트가 웹브라우저 상에서 사라지기 전에 호출

<br>

- ## 라이프사이클 메서드

  - ### render 함수

    이 메서드 안에서는 이벤트 설정이 아닌 곳에서 setState를 사용하면 안 됨  
    브라우저의 DOM에 접근해서도 안 됨  
    DOM 정보를 가져오거나 state에 변화를 줄 때는 componentDidMount에서 처리해야 함

  - ### 라이프사이클 메서드 예시

        ```javascript
        import React, { Component } from "react";

        class LifeCycleSample extends Component {
          state = {
            number: 0,
            color: null,
          };

          myRef = null; // ref를 설정할 부분

          constructor(props) {
            super(props);
            console.log("constructor");
          }

          static getDerivedStateFromProps(nextProps, prevState) {
            console.log("getDerivedStateFromProps");
            if (nextProps.color !== prevState.color) {
              return { color: nextProps.color };
            }
            return null;
          }

          componentDidMount() {
            console.log("componentDidMount");
          }

          shouldComponentUpdate(nextProps, nextState) {
            console.log("shouldComponentUpdate", nextProps, nextState);
            // 숫자의 마지막 자리가 4면 리렌더링하지 않습니다.
            return nextState.number % 10 !== 4;
          }

          componentWillUnmount() {
            console.log("componentWillUnmount");
          }

          handleClick = () => {
            this.setState({
              number: this.state.number + 1,
            });
          };

          getSnapshotBeforeUpdate(prevProps, prevState) {
            console.log("getSnapshotBeforeUpdate");
            if (prevProps.color !== this.props.color) {
              return this.myRef.style.color; //업데이트하기 전의 색상 snapshot
            }
            return null;
          }

          componentDidUpdate(prevProps, prevState, snapshot) {
            console.log("componentDidUpdate", prevProps, prevState);
            if (snapshot) {
              console.log("업데이트되기 직전 색상: ", snapshot);
            }
          }

          render() {
            console.log("render");
            const style = {
              color: this.props.color,
            };
            return (
              <div>
                <h1 style={style} ref={(ref) => (this.myRef = ref)}>
                  {this.state.number}
                </h1>
                <p>color: {this.state.color}</p>
                <button onClick={this.handleClick}>더하기</button>
              </div>
            );
          }
        }
        export default LifeCycleSample;
        ```

        ```javascript
        import React, { Component } from "react";
        import LifeCycleSample from "./LifeCycleSample";
        import ErrorBoundary from "./ErrorBoundary";

        // 랜덤 색상을 생성합니다.
        function getRandomColor() {
          return "#" + Math.floor(Math.random() * 16777215).toString(16);
        }

        class App extends Component {
          state = {
            color: "#000000",
          };

          handleClick = () => {
            this.setState({
              color: getRandomColor(),
            });
          };

          render() {
            return (
              <div>
                <button onClick={this.handleClick}>랜덤 색상</button>
                <ErrorBoundary>
                  <LifeCycleSample color={this.state.color} />
                </ErrorBoundary>
              </div>
            );
          }
        }
        export default App;
        ```

    <br>

# **8장** Hooks

- ## useState

  함수 컴포넌트에서 state 값 변경

  ```javascript
  import { useState } from "react";

  const Info = () => {
    const [name, setName] = useState("");
    const onChange = (e) => {
      setName(e.target.value);
    };

    return (
      <div>
        <input name="name" value={name} onChange={onChange} />
      </div>
    );
  };

  export default Info;
  ```

- ## useEffect

  리액트 컴포넌트가 렌더링될 때마다 특정 작업을 수행하도록 설정할 수 있는 Hook  
   클래스형 컴포넌트의 componentDidMount와 componentDidUpdate를 합친 것과 비슷

  ```javascript
  import { useState, useEffect } from "react";

  const Info = () => {
    const [name, setName] = useState("");

    useEffect(() => {
      console.log("렌더링이 완료되었습니다.");
    });

    const onChange = (e) => {
      setName(e.target.value);
    };

    return (
      <div>
        <input name="name" value={name} onChange={onChange} />
      </div>
    );
  };

  export default Info;
  ```

  - ### 마운트 될 때만 실행 (업데이트시 실행 x)

    ```javascript
    useEffect(() => {
      console.log("렌더링이 완료되었습니다.");
    }, []);
    ```

    두번째 파라미터로 빈 배열 넣어줌

  - ### 특정 값이 업데이트 될 때만 실행

    ```javascript
    useEffect(() => {
      console.log("렌더링이 완료되었습니다.");
    }, [name]);
    ```

    두번재 파라미터로 특정 값 설정

  - ### 뒷정리(cleanup) 함수

    ```javascript
    useEffect(() => {
      console.log("렌더링이 완료되었습니다.");

      return () => {
        console.log("cleanup");
      };
    }, [name]);
    ```

    컴포넌트가 언마운트 되기 전이나 업데이트 되기 직전에 실행  
    언마운트 될 때만 실행하려면 두번재 파라미터 []

- ## useReducer

  state, action 값을 전달받아 새로운 상태 반환하는 함수

  ```javascript
  import { useReducer } from "react";

  function reducer(state, action) {
    switch (action.type) {
      case "INCREMENT":
        return { value: state.value + 1 };
      case "DECREMENT":
        return { value: state.value - 1 };
      default:
        return state;
    }
  }

  const Counter = () => {
    const [state, dispatch] = useReducer(reducer, { value: 0 });

    return (
      <div>
        <p>
          현재 카운터 값은 <b>{state.value}</b>입니다.
        </p>
        <button onClick={() => dispatch({ type: "INCREMENT" })}>+1</button>
        <button onClick={() => dispatch({ type: "DECREMENT" })}>-1</button>
      </div>
    );
  };

  export default Counter;
  ```

  첫번째 파라미터: 리듀서 함수  
  두번째 파라미터: 해당 리듀서의 기본값  
  state: 현재 가리키고 있는 상태  
  dispatch: 액션을 발생시키는 함수  
  dispatch(action)과 같은 형태로 action 값을 넣어주면 리듀서 함수가 호출됨

- ## useMemo

  렌더링하는 과정에서 특정 값이 바뀌었을 때만 연산을 실행하고,  
  원하는 값이 바뀌지 않았다면 이전에 연산했던 결과를 다시 사용

  ```javascript
  import { useState, useMemo, useCallback, useRef } from "react";

  const getAverage = (numbers) => {
    console.log("평균값 계산 중...");
    if (numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
  };

  const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState("");

    const onChange = (e) => {
      setNumber(e.target.value);
    };

    const onInsert = () => {
      const nextList = list.concat(parseInt(number));
      setList(nextList);
      setNumber("");
    };

    const avg = useMemo(() => getAverage(list), [list]);

    return (
      <div>
        <input value={number} onChange={onChange} />
        <button onClick={onInsert}>등록</button>
        <ul>
          {list.map((value, index) => (
            <li key={index}>{value}</li>
          ))}
        </ul>
        <div>
          <b>평균값:</b>
          {avg}
        </div>
      </div>
    );
  };

  export default Average;
  ```

- ## useCallback

  컴포넌트 리렌더링 때마다 함수가 새롭게 만들어지는 것 방지

  ```javascript
  const onChange = useCallback((e) => {
    setNumber(e.target.value);
  }, []);

  const onInsert = useCallback(
    (e) => {
      const nextList = list.concat(parseInt(number));
      setList(nextList);
      setNumber("");
    },
    [number, list]
  );
  ```

  첫번째 파라미터: 생성하고 싶은 함수  
  두번재 파라미터: 배열(어떤 값이 바뀌었을 때 함수를 새로 생성해야 하는지)

- ## useRef

  함수 컴포넌트에서 ref를 쉽게 사용할 수 있게 해줌

  ```javascript
  const inputEl = useRef(null);

  const onInsert = useCallback(
    (e) => {
      const nextList = list.concat(parseInt(number));
      setList(nextList);
      setNumber("");
      inputEl.current.focus();
    },
    [number, list]
  );

  <input value={number} onChange={onChange} ref={inputEl} />;
  ```

<br>

# **9장** 컴포넌트 스타일링

# **12장** immer를 사용한 불변성 유지

# **13장** 리액트 라우터
