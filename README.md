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

- ## Sass

  ```css
  @import "~include-media/dist/include-media";
  @import "~open-color/open-color";

  $red: #fa5252;
  $orange: #fd7e14;
  $yellow: #fcc419;
  $green: #40c057;
  $blue: #339af0;
  $indigo: #5c7cfa;
  $violet: #7950f2;

  @mixin square($size) {
    $calculated: 32px \* $size;
    width: $calculated;
    height: $calculated;
  }
  ```

  ```css
  .SassComponent {
    display: flex;
    background: $oc-gray-2;
    @include media("<768px") {
      background: $oc-gray-9;
    }
    .box {
      background: red; // 일반 CSS 에선 .SassComponent .box 와 마찬가지
      cursor: pointer;
      transition: all 0.3s ease-in;
      &.red {
        // .red 클래스가 .box 와 함께 사용 됐을 때
        background: $red;
        @include square(1);
      }
      &.orange {
        background: $orange;
        @include square(2);
      }
      &.yellow {
        background: $yellow;
        @include square(3);
      }
      &.green {
        background: $green;
        @include square(4);
      }
      &.blue {
        background: $blue;
        @include square(5);
      }
      &.indigo {
        background: $indigo;
        @include square(6);
      }
      &.violet {
        background: $violet;
        @include square(7);
      }
      &:hover {
        // .box 에 마우스 올렸을 때
        background: black;
      }
    }
  }
  ```

- ## CSS Module

  CSS를 불러와서 사용할 때 클래스 이름을 고유한 값으로 만들어서 이름 중복 방지.  
  [파일 이름]_[클래스 이름]_[해시값] 형태로 저장됨  
  파일 확장자를 .module.css (혹은 .module.scss)로 저장하면 자동으로 적용  
  <br>
  CSSModule.module.scss

  ```css
  /* 자동으로 고유해질 것이므로 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용가능*/

  .wrapper {
    background: black;
    padding: 1rem;
    color: white;
    font-size: 2rem;
    &.inverted {
      // inverted 가 .wrapper 와 함께 사용 됐을 때만 적용
      color: black;
      background: white;
      border: 1px solid black;
    }
  }

  /* 글로벌 CSS 를 작성하고 싶다면 */
  :global {
    // :global {} 로 감싸기
    .something {
      font-weight: 800;
      color: aqua;
    }
  }
  ```

  CSSModule.js

  ```css
  import styles from "./CSSModule.module.scss";
  const CSSModule = () => {
    return (
      <div className={`${styles.wrapper} ${styles.inverted}`}>
        안녕하세요, 저는 <span className="something">CSS Module!</span>
      </div>
    );
  };

  export default CSSModule;
  ```

  - ### 템플릿 리터럴(Template Literal)

    문자열 안에 자바스크립트 레퍼런스 넣기

    ```javascript
    const name = "리액트";
    const message = `제 이름은 ${name}입니다.`;
    ```

  - ### classnames

    ```console
    $ yarn add classnames
    ```

    CSS 클래스를 조건부로 설정할 때 유용한 라이브러리

    ```javascript
    import classNames from "classnames";

    classNames("one", "two"); // = 'one two'
    classNames("one", { two: true }); // = 'one two'
    classNames("one", { two: false }); // = 'one'
    classNames("one", ["two", "three"]); // = 'one two three'

    const myClass = "hello";
    classNames("one", myClass, { myCondition: true }); // = 'one hello myCondition'

    const MyComponent = ({ highlighted, theme }) => (
      <div className={classNames("MyComponent", { highlighted }, theme)}>
        Hello
      </div>
    );
    ```

    classnames 라이브러리 사용하지 않는 경우 아래와 같이 코드를 작성해야하는 번거로움이 있음

    ```javascript
    const MyComponent = ({ highlighted, theme }) => (
      <div className={`MyComponent ${theme} ${highlighted ? : 'highlighted' : ''}`}>
        Hello
      </div>
    );
    ```

    ```javascript
    import classNames from "classnames";
    import styles from "./CSSModule.module.css";

    const cx = classNames.bind(styles); //미리 styles에서 클래스를 받아오도록 설정

    const CSSModule = () => {
      return (
        <div className={cx("wrapper", "inverted")}>
          안녕하세요, 저는
          <span className="something">CSS Module!</span>
        </div>
      );
    };
    ```

- ## styled-components

  ```console
  $ yarn add styled-components
  ```

  자바스크립트 파일 안에서 스타일 선언 (CSS-in-JS)

  ```js
  import React from "react";
  import styled, { css } from "styled-components";

  const sizes = {
    desktop: 1024,
    tablet: 768,
  };

  // 위에있는 size 객체에 따라 자동으로 media 쿼리 함수를 만들어줍니다.
  // 참고: https://www.styled-components.com/docs/advanced#media-templates
  const media = Object.keys(sizes).reduce((acc, label) => {
    acc[label] = (...args) => css`
      @media (max-width: ${sizes[label] / 16}em) {
        ${css(...args)};
      }
    `;

    return acc;
  }, {});

  // 스타일링 된 엘리먼트들

  // 반응형 디자인
  const Box = styled.div`
    /* props 로 넣어준 값을 직접 전달해줄 수 있습니다. */
    background: ${(props) => props.color || "blue"};
    padding: 1rem;
    display: flex;
    width: 1024px;
    margin: 0 auto;
    ${media.desktop`width: 768px;`}
    ${media.tablet`width: 100%;`};
  `;

  const Button = styled.button`
    background: white;
    color: black;
    border-radius: 4px;
    padding: 0.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    font-size: 1rem;
    font-weight: 600;
    /* & 문자를 사용하여 Sass 처럼 자기 자신 선택 가능 */
    &:hover {
      background: rgba(255, 255, 255, 0.9);
    }
    /* 다음 코드는 inverted 값이 true 일 때 특정 스타일을 부여해줍니다. */
    /* props에 따른 조건부 스타일링 */
    ${(props) =>
      props.inverted &&
      css`
        background: none;
        border: 2px solid white;
        color: white;
        &:hover {
          background: white;
          color: black;
        }
      `};
    & + button {
      margin-left: 1rem;
    }
  `;

  const StyledComponent = () => (
    <Box color="black">
      <Button>안녕하세요</Button>
      <Button inverted={true}>테두리만</Button>
    </Box>
  );

  export default StyledComponent;
  ```

  <br>

# **12장** immer를 사용한 불변성 유지

- ## immer를 사용하지 않고 불변성 유지

  ```javascript
  const object = {
    somewhere: {
      deep: {
        inside: 3,
        array: [1, 2, 3, 4],
      },
      bar: 2,
    },
    foo: 1,
  };

  // somewhere.deep.inside 값을 4로 바꾸기
  let nextObject = {
    ...object,
    somewhere: {
      ...object.somewhere,
      deep: {
        ...object.somewhere.deep,
        inside: 4,
      },
    },
  };

  // somewhere.deep.array에 5 추가하기
  let nextObject2 = {
    ...object,
    somewhere: {
      ...object.somewhere.deep,
      array: object.somewhere.deep.array.concat(5),
    },
  };
  ```

- ## immer 사용법

  ```javascript
  import produce from "immer";
  const originalState = [
    {
      id: 1,
      todo: `전개 연산자와 배열 내장 함수로 불변성 유지하기`,
      checked: true,
    },
    {
      id: 2,
      todo: `immer로 불변성 유지하기`,
      checked: false,
    },
  ];

  const nextState = produce((originalState, draft) => {
    // id가 2인 항목의 checked 값을 true로 설정
    const todo = draft.find((t) => t.id === 2); // id로 항목 찾기
    todo.checked = true;
    // 혹은 draft[1].checked = true

    // 배열에 새로운 데이터 추가
    draft.push({
      id: 3,
      todo: `일정 관리 앱에 immer 적용하기`,
      checked: false,
    });

    // id = 1인 항목을 제거하기
    draft.splice(
      draft.findIndex((t) => t.id === 1),
      1
    );
  });
  ```

  produce  
  첫번째 파라미터: 수정하고 싶은 상태  
  두번째 파라미터: 상태를 어떻게 업데이트할지 정의하는 함수  
  <br>
  두번째 파라미터로 전달되는 함수 내부에서 원하는 값을 변경하면,  
  produce 함수가 불변성 유지를 대신해주면서 새로운 상태를 생성해줌

- ## useState와 함께쓰기

  - ### useState 함수형 업데이트

    ```javascript
    import { useCallback } from "react";

    const [number, setNumber] = useState(0);
    // prevNumbers는 현재 number 값을 가리킨다.
    const onIncrease = useCallback(
      () => setNumber((prevNumber) => prevNumber + 1),
      []
    );
    ```

    ```javascript
    import produce from "immer";

    const update = produce((draft) => {
      draft.value = 2;
    });

    const originalState = {
      value: 1,
      foo: "bar",
    };

    const nextState = update(originalState);
    console.log(nextState); // { value: 2, foo: 'bar'}
    ```

    immer에서 제공하는 produce 함수를 호출할 때, 첫 번째 파라미터가 함수 형태라면 업데이트 함수를 반환한다.

  ```javascript
  import React, { useRef, useCallback, useState } from "react";
  import produce from "immer";

  const App = () => {
    const nextId = useRef(1);
    const [form, setForm] = useState({ name: "", username: "" });
    const [data, setData] = useState({
      array: [],
      uselessValue: null,
    });

    // input 수정을 위한 함수
    const onChange = useCallback((e) => {
      const { name, value } = e.target;
      setForm(
        produce((draft) => {
          draft[name] = value;
        })
      );
    }, []);

    // form 등록을 위한 함수
    const onSubmit = useCallback(
      (e) => {
        e.preventDefault();
        const info = {
          id: nextId.current,
          name: form.name,
          username: form.username,
        };

        // array에 새 항목 등록
        setData(
          produce((draft) => {
            draft.array.push(info);
          })
        );

        // form 초기화
        setForm({
          name: "",
          username: "",
        });
        nextId.current += 1;
      },
      [form.name, form.username]
    );

    // 항목을 삭제하는 함수
    const onRemove = useCallback((id) => {
      setData(
        produce((draft) => {
          draft.array.splice(
            draft.array.findIndex((info) => info.id === id),
            1
          );
        })
      );
    }, []);

    return (
      <div>
        <form onSubmit={onSubmit}>
          <input
            name="username"
            placeholder="아이디"
            value={form.username}
            onChange={onChange}
          />
          <input
            name="name"
            placeholder="이름"
            value={form.name}
            onChange={onChange}
          />
          <button type="submit">등록</button>
        </form>
        <div>
          <ul>
            {data.array.map((info) => (
              <li key={info.id} onClick={() => onRemove(info.id)}>
                {info.username} ({info.name})
              </li>
            ))}
          </ul>
        </div>
      </div>
    );
  };

  export default App;
  ```

  <br>

# **13장** 리액트 라우터

- ## SPA(Single Page Application)

  리액트 같은 라이브러리 혹은 프레임워크를 사용하여 뷰 렌더링을 사용자의 브라우저가 담당하도록 하고,  
  우선 애플리케이션을 브라우저에 불러와서 실행시킨 후에 사용자와의 인터랙션이 발생하면  
  필요한 부분만 자바스크립트를 사용하여 업데이트 해준다.

  - ### 라우팅
    다른 주소에 다른 화면을 보여주는 것

- ## react-router-dom

  ```console
  $ yarn add react-router-dom
  ```

  src/index.js

  ```javascript
  import React from "react";
  import ReactDOM from "react-dom";
  import "./index.css";
  import App from "./App";
  import { BrowserRouter } from "react-router-dom";

  ReactDOM.render(
    <BrowserRouter>
      <App />
    </BrowserRouter>,
    document.getElementById("root")
  );
  ```

  - ### Route 컴포넌트로 특정 주소에 컴포넌트 연결

    ```javascript
    import { Route } from "react-router-dom";

    const App = () => {
      return (
        <div>
          <Route path="/" component={Home} exact={true} />
          <Route path="/about" component={About} />
        </div>
      );
    };

    export default App;
    ```

  - ### Link 컴포넌트로 주소 이동

    ```javascript
    <Link to="/">홈</Link>
    ```

  - ### Route 하나에 여러개의 path 설정

    ```javascript
    <Route path={["/about", "/info"]} component={About} />
    ```

    path props를 배열로 설정해준다

  - ### URL 파라미터

    ```javascript
    <Route path="/profile/:username" component={Profile} />
    ```

    Profile.js

    ```javascript
    import React from "react";

    const data = {
      donghyun: {
        name: "백동현",
        description: "리액트를 공부하는 학생",
      },
      gildong: {
        name: "홍길동",
        description: "고전 소설 홍길동전의 주인공",
      },
    };

    const Profile = ({ match }) => {
      const { username } = match.params;
      const profile = data[username];

      if (!profile) {
        return <div>존재하지 않는 사용자입니다.</div>;
      }
      return (
        <div>
          <h3>
            {username}({profile.name})
          </h3>
          <p>{profile.description}</p>
        </div>
      );
    };

    export default Profile;
    ```

    URL 파라미터를 사용할 때는 라우트로 사용되는 컴포넌트에서 받아 오는 **_match_**라는 객체 안에 params 값을 참조한다.

    match 객체 안에는 현재 컴포넌트가 어떤 경로 규칙에 의해 보이는지에 대한 정보가 들어 있다.

    path 규칙에는 /profile/:username 이라고 하면 된다.  
    match.params.username 값을 통해 현재 username 값을 조회할 수 있다.

  - ### URL 쿼리

    쿼리는 **_location_** 객체에 들어 있는 **_search_**값에서 조회할 수 있다.  
    location 객체는 라우트로 사용된 컴포넌트에게 props로 전달, 웹 애플리케이션의 현재 주소에 대한 정보를 지님  
    <br>
    http://localhost:3000/about?detail=true에 접속했을 때loaction의 형태

    ```json
    {
      "pathname": "/about",
      "search": "?detail=true",
      "hash": ""
    }
    ```

    쿼리 문자열을 객체로 변환할 때는 qs 라는 라이브러리 사용

    ```console
    $ yarn add qs
    ```

    ```javascript
    import React from "react";
    import qs from "qs";

    const About = ({ location }) => {
      const query = qs.parse(location.search, {
        ignoreQueryPrefix: true, // 이 설정을 통해 문자열 맨 앞의 ?를 생략
      });
      const showDetail = query.detail === "true"; // 쿼리의 파싱 결과 값은 문자열이다.

      return (
        <div>
          <h1>소개</h1>
          <p>About, 리액트 라우터 기초를 실습해 보는 예제 프로젝트</p>
          {showDetail && <p>detail 값을 true로 설정하셨군요!</p>}
        </div>
      );
    };

    export default About;
    ```

    쿼리 문자열을 객체로 파싱하는 과정에서 결과 값은 언제나 문자열  
    ?value=1 혹은 ?value=true와 같은 숫자나 논리 자료형을 사용한다고 해서 해당 값이 원하는 형태로 변환되는 것이 아니라, "1", "true"와 같이 문자열 형태로 받아짐  
    숫자를 받아 와야 하면 parseInt 함수를 통해 꼭 숫자로 변환  
    논리 자료형 값을 사용해야 하는 경우 "true" 문자열이랑 일치하는지 비교

  - ### history

    라우트로 사용된 컴포넌트에 match, location과 함께 전달되는 props 중 하나  
    컴포넌트 내에 구현하는 메서드에서 라우터 API를 호출

    ```javascript
    import React, { Component } from "react";

    class HistorySample extends Component {
      // 뒤로가기
      handleGoBack = () => {
        this.props.history.goBack();
      };

      // 홈으로 이동
      hadleGoHome = () => {
        this.props.history.push("/");
      };

      componentDidMount() {
        // 이것을 설정하고 나면 페이지에 변화가 생기려고 할 때마다 정말 나갈 것인지를 질문
        this.unblock = this.props.history.block("정말 떠나실 건가요?");
      }

      componentWillUnmount() {
        // 컴포넌트가 언마운트되면 질문을 멈춤
        if (this.unblock) {
          this.unblock();
        }
      }

      render() {
        return (
          <div>
            <button onClick={this.handleGoBack}>뒤로</button>
            <button onClick={this.hadleGoHome}>Home</button>
          </div>
        );
      }
    }

    export default HistorySample;
    ```

  - ### withRouter

    라우트로 사용된 컴포넌트가 아니여도 match, location, history 객체를 접근할 수 있게 해 준다.

    ```javascript
    import React from "react";
    import { withRouter } from "react-router-dom";

    const WithRouterSample = ({ location, match, history }) => {
      return (
        <div>
          <h4>location</h4>
          <textarea
            value={JSON.stringify(location, null, 2)}
            rows={7}
            readOnly={true}
          />
          <h4>match</h4>
          <textarea
            value={JSON.stringify(location, null, 2)}
            rows={7}
            readOnly={true}
          />
          <button onClick={() => history.push("/")}>Go to Home</button>
        </div>
      );
    };

    export default WithRouterSample;
    ```

    JSON.stringify(location, null, 2): JSON에 들여쓰기가 적용된 상태로 문자열이 만들어 짐

    ```javascript
    import React from "react";
    import { withRouter } from "react-router-dom";
    import WithRouterSample from "./WithRouterSample";

    const data = {
      donghyun: {
        name: "백동현",
        description: "리액트를 공부하는 학생",
      },
      gildong: {
        name: "홍길동",
        description: "전래동화 흥부전의 주인공",
      },
    };

    const Profile = ({ match }) => {
      const { username } = match.params;
      const profile = data[username];
      if (!profile) {
        return <div>존재하지 않는 사용자입니다.</div>;
      }
      return (
        <div>
          <h3>
            {username}({profile.name})
          </h3>
          <p>{profile.description}</p>
          <WithRouterSample />
        </div>
      );
    };

    export default withRouter(Profile);
    ```

    match는 현재의 라우트 컴포넌트를 기준으로 전달됨.

  - ### Switch
    Switch 컴포넌트는 여러 Route를 감싸서 그중 일치하는 단 하나의 라우트만을 렌더링 시킨다.  
    Switch를 사용하면 모든 규칙과 일치하지 않을 때 보여줄 Not Found 페이지 구현 가능
    ```js
    <Switch>
      <Route path="/" component={Home} exact={true} />
      <Route path={["/about", "/info"]} component={About} />
      <Route path="/profiles" component={Profiles} />
      <Route path="/history" component={HistorySample} />
      <Route
        // path를 따로 정의하지 않으면 모든 상황에 렌더링됨
        render={({ location }) => (
          <div>
            <h2>이 페이지는 존재하지 않습니다.</h2>
            <p>{location.pathname}</p>
          </div>
        )}
      />
    </Switch>
    ```
  - ### NavLink

    현재 경로와 Link에서 사용하는 경로가 일치하는 경우  
    특정 스타일 혹은 CSS 클래스를 적용할 수 있는 컴포넌트  
    NavLink에서 링크가 활성화되었을 때 스타일 적용하려면 -> activeStyle  
    CSS 클래스를 적용할 때 -> activeClassName  
    값을 각각 props로 넣어주면 된다

    profiles.js

    ```js
    import React from "react";
    import { NavLink, Route } from "react-router-dom";
    import Profile from "./Profile";

    const Profiles = () => {
      const activeStyle = {
        background: "black",
        color: "white",
      };
      return (
        <div>
          <h3>사용자 목록:</h3>
          <ul>
            <li>
              <NavLink activeStyle={activeStyle} to="/profiles/donghyun" active>
                donghyun
              </NavLink>
            </li>
            <li>
              <NavLink activeStyle={activeStyle} to="/profiles/gildong">
                gildong
              </NavLink>
            </li>
          </ul>

          <Route
            path="/profiles"
            exact
            render={() => <div>유저를 선택해주세요.</div>}
          />
          <Route path="/profiles/:username" component={Profile} />
        </div>
      );
    };

    export default Profiles;
    ```
