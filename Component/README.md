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
