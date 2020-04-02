## useContext
- context를 이용하면 props를 통해 데이터를 넘겨주지 않아도 하위 컴포넌트에서 모두 데이터를 공유할 수 있다   
- context를 많이 사용하면 컴포넌트 재사용이 어려워질 수 있고, 컴포넌트 합성이 더 간단한 해결책이 될 수 있다   

## useState


## useCallback
- 함수형 컴포넌트는 자체가 render함수이기 때문에 계속 렌더링 되는 것을 막기위해 사용하는 훅   
component 참조
```javascript
const func = useCallback(e => {}, [])
// 첫번째 인자 클로저, 두번째 인자 dependency list
// dependency list를 이전에 넘겨받은 값과 비교해서 다르면 새로운 함수 생성 
```

## useMemo
- useCallback과 동일한 방식으로 사용하면 된다   
- useCallback은 prop에 함수를 넘겨줄 때 사용한다면 useMemo는 html이 포함된 구문을 넘겨줄 때 새로 렌더링 방지목적으로 사용한다   
```javascript
const accessary = useMemo(() => { return email !== "" && <button>X</button>}, [email !== ""])
```

## useRef
- DOM객체를 얻기 위해 사용, 렌더링에 영향을 미치지 않는 값을 보관하는 용도로 사용   

## useEffect
- componentDidMount와 componentDidUpdate가 실행되는 시점을 합하면 useEffect가 실행되는 시점이라고 생각하면 된다. 렌더가 완료된 이후 호출되는 콜백   
- data fetching, focus와 같은 side effect를 발생시킬 때 쓰는 훅
```javascript
const Input = ({...,type,value,inputRef}) => {
const inputRef = useRef(null)
useEffect(() => {
    if (autoFocus) {
        inputRef.current.focus()
    }
    return () => {
        console.log('unmount')
    }
}, [autofocus])
render(
    <input type={type} value={value} ref={inputRef} />
)}
// 상위 컴포넌트에서 inputRef props로 true를 넘겨주면 렌더링 되고 포커스가 된다
```
- useEffect의 콜백에 리턴할 때 함수를 넣어주면 unmount되면서 실행된다   


## Custom Hook 
- 중복되는 로직을 묶어서 추상화하고 재사용   
```javascript
import { useState, useCallback } from 'react'
function useInputState(defaultState) {
  const [ state, setState ] = useState(defaultState)
  const handleChangeState = useCallback(({ target: { value }}) => {
    setState(value)
  }, [])
  return [
    state,
    setState,
    handleChangeState,
  ]
}
export default useInputState
```
```javascript
import useInputState from './useInputState'
const [ email, setEmail, handleChangeEmail ] = useInputState('')
const [ password, setPassword, handleChangePassword ] = useInputState('')
 <Input type="email" placeholder="이메일" value={email} onChange={handleChangeEmail} />
```
```javascript
import { useState, useEffect } from 'react'
function useWindowWidth() {
  const [ windowWidth, setWindowWidth ] = useState(
    window.innerWidth
  )
  useEffect(() => {
    function handleResize() {
      setWindowWidth(window.innerWidth)
    }
    window.addEventListener('resize', handleResize)
    return () => {
      window.removeEventListener('resize', handleResize)
    }
  }, [])
  return windowWidth
}
export default useWindowWidth
// 이 커스텀훅을 사용할 component에서 import한 뒤에 const windowWidth = useWindowWidth();로 사용
```

출처: <https://hyunseob.github.io/2019/06/02/react-component-the-right-way/>