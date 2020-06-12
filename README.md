## Life Cycle
[컴포넌트 생성초기]    
1. Constructor(컴포넌트 생성자 함수, 지역 state를 초기화할 때 setState없이 state에 직접 할당)   
componentWillMount(화면에 나가기 직전 호출되는 API:현재 Deprecated)    
2. static getDerivedStateFromProps()    
3. render()   
4. componentDidMount(컴포넌트가 화면에 나타나게 됐을 때 호출, 해당 컴포넌트에서 필요로 하는 데이터 요청을 위해 axios를 통해 ajax요청을 하거나 DOM의 속성을 읽거나 직접변경하는 작업 진행)   


[컴포넌트 업데이트]   
- setState() / props업데이트 / forceUpdate()인 경우     
1. static getDerivedStateFromProps(props로 받아온 값을 state로 동기화하는 작업을 해줘야 하는 경우, setState를 하는 것이 아니라 특정 props가 바뀔 때 설정하고 싶은 state값을 리턴하는 형태로 사용, props에 state가 의존하는 드문 케이스를 위한 것)   
2. shouldComponentUpdate(기본적으로 true를 반환, 그러나 false를 반환하면 render함수를 호출하지 않는다 부모컴포넌트가 리렌더링 될 때 자식을 렌터링 시키고 싶지 않을 때 사용 CPU처리량 줄일 수 있다 -> 오직 성능 최적화만을 위한 것)    
3. render()     
4. getSnapshotBeforeUpdate(render()함수 호출 이후 DOM에 실제 변화 발생 사이, 그래서 DOM변화 직전의 DOM상태를 가져와서 여기서 리턴값을 componentDidUpdate의 3번째 파라미터(snapshot)로 받아올 수 있다 예를들어 새 데이터가 추가 되어도 스크롤바 유지하고싶을 때)   
- render() -> getSnapshopBeforeUpdate() -> 실제 DOM에 변화 발생 -> componentDidUpdate   
5. componentDidUpdate(prevProps, prevState, snapshot)    


[컴포넌트 제거]   
componentWillUnmount(이벤트, setTimeOut, 외부 라이브러리 인스턴스 제거)   
[오류 처리]   
자식 컴포넌트를 렌더링 하거나, 자식 컴포넌트가 생명주기 메서드를 호출하거나 생성자 메서드를 호출하는 과정에서 오류 발생시 호출
클래스 컴포넌트에 밑의 두가지를 정의할 경우 해당 컴포넌트는 Error Boundary가 된다.   
Static getDerivedStateFromError()   
componentDidCatch()   

## 조건부 렌더링
```javascript
const Main = () => {
    let button;
    if (loggedIn) {
        button = `<button onClick={logOut}>Log out</button>`
    } else {
        button = `<button onClick={logIn}>Log in</button>`
    }
    return (
        <div>
            {button}
        </div>
    )
}

const Main = () => {
    const ShowBanner = () => {
        if (!warn) {
            return null;
        } 
        return (
            <div>Banner</div>
        )
    }
    const [warn, setWarn] = useState(false);
    const toggleBanner = () => {
        setWarn(!warn)
    }
    return (
        <div>
            <ShowBanner /> //대문자로 시작해야 한다
            <button onClick={toggleBanner}>{warn ? 'hide':'show'}</button>
        </div>
    )
}
```

## Form 
- Form Value 가져다 넣기
- Form Value 접근
- Form에 빈칸이 있으면 해당 칸으로 focus
```javascript
const Form = () => {
    const [myName, setMyName] = useState('Shack');
    const [myAge, setMyAge] = useState(18)
    const name = useRef()
    const age = useRef()
    const handleSubmit = (e) => {
        e.preventDefault()
        console.log(name.current.value)
        console.log(age.current.value)
        if (name.current.value.length === 0) {
            name.current.focus()
        } else if (age.current.value.length === 0) {
            age.current.focus()
        };
    }
    useEffect(() => {
        name.current.value = myName;
        age.current.value = myAge;
    }, [])
    return (
        <div>
            <form onSubmit={handleSubmit}>
                <input type="text" ref={name}></input>
                <input type="number" ref={age}></input>
                <input type="submit" value="submit!" /> 
            </form>
        </div>
    )
}
```