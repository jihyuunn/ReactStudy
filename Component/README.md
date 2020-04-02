## Component
컴포넌트는 props, state와 같은 데이터를 입력받아서 DOM node를 출력하는 함수   
(react hooks API가 생긴 이후로 HOC은 좋은 접근 방법이 아니다 대부분 훅으로 대체 가능)   
- controlled components : 우리가 보통 만드는 state를 가진 컴포넌트   
- uncontrolled components : state가 없는 컴포넌트, 그래서 state의 변화가 없기 때문에 render함수가 실행되지 않는다   
state로 email, password을 만들어 놓고 input에서 value={email}, value={password}로 주면, email input에 입력할 때 email, password가 모두 렌더링 된다. 그래서 필요한 부분만 렌더링이 되게 하려면 state를 없애고 input에 key값을 주면 된다   
```javascript
const SignUp = () => {
    // 하나의 state가 아니어서 각각 해당하는 Input컴포넌트만 렌더링 된다
    let email
    let password
    const [reset, setReset] = useState(0)
    return (
        <form onSubmit={formHandler}>
            <Input key={${reset}+email} type='email' onChange={emailHandler} /> 
            <Input key={${reset}+password} type='password' onChange={passwordHandler} />
            <input type='submit' value='submit' />
        </form>
    )
}
```
- key property는 보통 array에 들어있는 요소들을 이용하여 map으로 컴포넌트 렌더링 할 때 필요한 값이다. 원래는 배열안에 특정 값을 identify하기 위한 도구이다. 그래서 초기화를 할 때마다 state reset 값이 하나씩 올라가고 key값이 변경되어 react가 그냥 다른 노드로 인식을 하게 된다. 1email이 2email이 되면 1email이 아예 사라졌다고 생각해 인스턴스를 지우고 새로 2email을 그리는 것   
#### 이렇게 uncontrolled component를 만들게 되면 state가 외부에 공개되지 않아서(캡슐화) 장점

## Pure Component
부모에게서 전달되는 props값과 state값을 비교하여 변하지 않으면 다시 렌더링 되지 않는 컴포넌트   
부모 컴포넌트에서 state값이 바뀔 때 functional component로 되어있는 자식 컴포넌트는 새로 렌더링이  되지만, pure component는 부모 컴포넌트의 state가 바뀌더라도 자식에게 전달되는 props가 변하지 않으면 다시 렌더링 되지 않는다   
```javascript
<Input key={${reset}+email} type='email' onChange={e => setValue(e.target.value)} /> 
<Input key={${reset}+password} type='password' onChange={e => setValue(e.target.value)} />
```
이렇게 작성시 Input이 pure component여도 두개가 동시에 계속 렌더링 된다. 인라인 함수를 적으면 매 render실행마다 Input에 내려가는 props가 계속 달라진다는 의미  

## Component 합성
컴포넌트 상속이 지향되는 경우는 거의 없고 대부분의 경우가 합성으로 해결 가능하다   

## Functional Component
Hooks가 추가되면서 함수형 컴포넌트만으로 앱을 구성할 수 있게 되었다   

## React.memo
pure component의 함수형 버전을 사용하기 위해서는 함수형 컴포넌트를 react.memo로 감싸기만 하면 된다   
(동작하는 방식이 함수형 프로그래밍의 memoization과 비슷하기 때문, 입력이 같으면 반환하는 값이 똑같다는 점을 이용해 한번 계산한 반환값을 저장하고 다음 입력이 들어올 때 저장된 값을 반환하는 것)   
=> props와 state가 같으면 같은 렌더링 결과를 이용하는 것, 다만 저장했다가 반환하는 것이 아니라 렌더링을 새로 하지 않는 것   
```javascript
import React from 'react'
function Input = ({ type, placeholder, value, onChange }) => {
    return (
        <input type={type} placeholder={placeholder} value={value} onChange={onChange}>
    )
};
export default React.memo(Input)
```

## useCallback
함수형 컴포넌트는 함수의 본문자체가 render 함수이기 때문에 이벤트핸들러를 어디서 만들던지 계속 새로운 함수를 만들게된다   
(아까 pure component에 inline함수를 쓰면 계속 렌더링 되는 것과 같은 이유로..)   
이 때 useCallback을 써주면 된다   
```javascript
function Form = () => {
    const [email, setEmail] = useState()
    const [password, setPassword] = useState()
    const emailHandler = useCallback(e => {setEmail(e.target.value)}, []);
    const passwordHandler = useCallback(e => {setPassword(e.target.value), []});
    const submitHandler = useCallback(e => {console.log(email, password), [email, password]});

    render(
        <form>
            <Input type='email' placeholder='email' value={email} onChange={emailHadler}/>
            <Input type='password' placeholder='password' value={password} onChange={passwordHadler}/>
        </form>
    )
}
```
##### useCallback의 첫번째 인자는 클로져여서 이 함수가 생성될 때의 상태를 기억한다. 그래서 두번째 인자가 없으면 함수가 생성된 초기상태(e.g. 이메일과 패스워드가 null일 때)를 계속 기억한다. 두번째 인자인 dependency list에 의존 값을 넣어주면 이 리스트를 이전에 받은 값과 shallow compare로 비교해서 다르면 새로운 함수를 생성하고 같으면 함수생성 하지 않는다   


