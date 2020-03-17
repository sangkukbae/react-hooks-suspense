# react-hooks-suspense

## :page_facing_up: 목차
* [useState](#usestate)
* [useEffect](#useeffect)
* [useRef](#useref)
* [useReducer](#usereducer)
* [React.Memo](#reactmemo)
* [React.lazy and Suspense](#reactlazy-and-suspense)
* [Suspense](#suspense)


## useState
```jsx
import React, {useState} from 'react'

function Counter() {
  const [count, setCount] = useState(0)
  const increment = () => setCount(count + 1)
  return <button onClick={increment}>{count}</button>
}

export default Counter

```
[https://4xp1q68kjx.csb.app/](https://4xp1q68kjx.csb.app/)

**testing**
```jsx
import React from 'react'
import {render, fireEvent} from 'react-testing-library'
import Counter from '../lessons/02-testing-hooks'

test('counter increments the count', () => {
  const {container} = render(<Counter />)
  const button = container.firstChild
  expect(button.textContent).toBe('0')
  fireEvent.click(button)
  expect(button.textContent).toBe('1')
})

```

**Custom hooks**
```jsx
import React, {useState} from 'react'

function useCounter({initialState, step}) {
  const [count, setCount] = useState(initialState)
  const increment = () => setCount(count + step)
  return {count, increment, setCount}
}

function Counter() {
  const {count, increment} = useCounter({initialState: 5, step: 3})
  return <button onClick={increment}>{count}</button>
}

export default Counter
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

## useEffect
```jsx
import React, {useState, useEffect} from 'react'

function Counter() {
  const [count, setCount] = useState(() =>
    Number(window.localStorage.getItem('count') || 0),
  )
  const increment = () => setCount(count + 1)
  useEffect(
    () => {
      window.localStorage.setItem('count', count)
    },
    [count],
  )
  return <button onClick={increment}>{count}</button>
}

export default Counter
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

**testing**
```jsx
import React from 'react'
import {render, fireEvent} from 'react-testing-library'
import Counter from '../lessons/05-testing-effects'

afterEach(() => {
  window.localStorage.removeItem('count')
})

test('counter increments the count', () => {
  const {container} = render(<Counter />)
  const button = container.firstChild
  expect(button.textContent).toBe('0')
  fireEvent.click(button)
  expect(button.textContent).toBe('1')
})

test('reads and updates localStorage', () => {
  window.localStorage.setItem('count', 3)
  const {container, rerender} = render(<Counter />)
  const button = container.firstChild
  expect(button.textContent).toBe('3')
  fireEvent.click(button)
  expect(button.textContent).toBe('4')
  expect(window.localStorage.getItem('count')).toBe('4')
})
```

## useRef
```jsx
import './06-vanilla-tilt.css'
import React, {useRef, useEffect} from 'react'
import VanillaTilt from 'vanilla-tilt'

function Tilt(props) {
  const tiltRef = useRef()
  useEffect(() => {
    VanillaTilt.init(tiltRef.current, {
      max: 25,
      speed: 400,
      glare: true,
      'max-glare': 0.5,
    })
    return () => tiltRef.current.vanillaTilt.destroy()
  }, [])
  return (
    <div ref={tiltRef} className="tilt-root">
      <div className="tilt-child">{props.children}</div>
    </div>
  )
}

function App() {
  return (
    <div className="totally-centered">
      <Tilt>
        <div className="totally-centered">vanilla-tilt.js</div>
      </Tilt>
    </div>
  )
}

export default App
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)


## Interact and Update State in React with useState
```jsx
import React, {useState, useRef, useEffect} from 'react'

function Stopwatch() {
  const [lapse, setLapse] = useState(0)
  const [running, setRunning] = useState(false)
  const intervalRef = useRef(null)

  useEffect(() => {
    return () => clearInterval(intervalRef.current)
  }, [])

  function handleRunClick() {
    if (running) {
      clearInterval(intervalRef.current)
    } else {
      const startTime = Date.now() - lapse
      intervalRef.current = setInterval(() => {
        setLapse(Date.now() - startTime)
      }, 0)
    }
    setRunning(!running)
  }

  function handleClearClick() {
    clearInterval(intervalRef.current)
    setLapse(0)
    setRunning(false)
  }

  return (
    <div style={{textAlign: 'center'}}>
      <label
        style={{
          fontSize: '5em',
          display: 'block',
        }}
      >
        {lapse}
        ms
      </label>
      <button onClick={handleRunClick} style={buttonStyles}>
        {running ? 'Stop' : 'Start'}
      </button>
      <button onClick={handleClearClick} style={buttonStyles}>
        Clear
      </button>
    </div>
  )
}

const buttonStyles = {
  border: '1px solid #ccc',
  background: '#fff',
  fontSize: '2em',
  padding: 15,
  margin: 5,
  width: 200,
}

export default Stopwatch
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

## useReducer
```jsx
import React, {useReducer, useRef, useEffect} from 'react'

function reducer(currentState, newState) {
  return {...currentState, ...newState}
}

function Stopwatch() {
  const [{running, lapse}, setState] = useReducer(reducer, {
    running: false,
    lapse: 0,
  })
  const intervalRef = useRef(null)

  useEffect(() => {
    return () => clearInterval(intervalRef.current)
  }, [])

  function handleRunClick() {
    if (running) {
      clearInterval(intervalRef.current)
    } else {
      const startTime = Date.now() - lapse
      intervalRef.current = setInterval(() => {
        setState({lapse: Date.now() - startTime})
      }, 0)
    }
    setState({running: !running})
  }

  function handleClearClick() {
    clearInterval(intervalRef.current)
    setState({lapse: 0, running: false})
  }

  return (
    <div style={{textAlign: 'center'}}>
      <label
        style={{
          fontSize: '5em',
          display: 'block',
        }}
      >
        {lapse}
        ms
      </label>
      <button onClick={handleRunClick} style={buttonStyles}>
        {running ? 'Stop' : 'Start'}
      </button>
      <button onClick={handleClearClick} style={buttonStyles}>
        Clear
      </button>
    </div>
  )
}

const buttonStyles = {
  border: '1px solid #ccc',
  background: '#fff',
  fontSize: '2em',
  padding: 15,
  margin: 5,
  width: 200,
}

export default Stopwatch
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

**Custom hooks**
```jsx
import React, {useReducer, useRef, useEffect} from 'react'

function reducer(currentState, newState) {
  return {...currentState, ...newState}
}

function useStopwatch() {
  const [{running, lapse}, setState] = useReducer(reducer, {
    running: false,
    lapse: 0,
  })
  const intervalRef = useRef(null)

  useEffect(() => {
    return () => clearInterval(intervalRef.current)
  }, [])

  function handleRunClick() {
    if (running) {
      clearInterval(intervalRef.current)
    } else {
      const startTime = Date.now() - lapse
      intervalRef.current = setInterval(() => {
        setState({lapse: Date.now() - startTime})
      }, 0)
    }
    setState({running: !running})
  }

  function handleClearClick() {
    clearInterval(intervalRef.current)
    setState({lapse: 0, running: false})
  }

  return {handleRunClick, handleClearClick, lapse, running}
}

function Stopwatch() {
  const stopwatchOne = useStopwatch()
  const stopwatchTwo = useStopwatch()

  return (
    <div style={{textAlign: 'center'}}>
      <label
        style={{
          fontSize: '5em',
          display: 'block',
        }}
      >
        {stopwatchOne.lapse}
        ms
      </label>
      <button onClick={stopwatchOne.handleRunClick} style={buttonStyles}>
        {stopwatchOne.running ? 'Stop' : 'Start'}
      </button>
      <button onClick={stopwatchOne.handleClearClick} style={buttonStyles}>
        Clear
      </button>
      <hr />
      <strong>Lapse Difference:</strong>
      <span>
        {stopwatchOne.lapse - stopwatchTwo.lapse}
        ms
      </span>
      <hr />

      <label
        style={{
          fontSize: '5em',
          display: 'block',
        }}
      >
        {stopwatchTwo.lapse}
        ms
      </label>
      <button onClick={stopwatchTwo.handleRunClick} style={buttonStyles}>
        {stopwatchTwo.running ? 'Stop' : 'Start'}
      </button>
      <button onClick={stopwatchTwo.handleClearClick} style={buttonStyles}>
        Clear
      </button>
    </div>
  )
}

const buttonStyles = {
  border: '1px solid #ccc',
  background: '#fff',
  fontSize: '2em',
  padding: 15,
  margin: 5,
  width: 200,
}

export default Stopwatch
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

## React.Memo
```jsx
import React, {useState} from 'react'

const Upper = React.memo(function Upper({children}) {
  const [count, setCount] = useState(0)
  return (
    <div>
      Uppercase version: {children.toUpperCase()}{' '}
      <button onClick={() => setCount(count + 1)}>{count}</button>
    </div>
  )
})

function App() {
  const [first, setFirstName] = useState('')
  const [last, setLastName] = useState('')
  return (
    <div>
      <label htmlFor="first-name-input">First Name</label>
      <input
        id="first-name-input"
        onChange={e => setFirstName(e.target.value)}
      />
      <Upper>{first}</Upper>
      <hr />
      <label htmlFor="last-name-input">Last Name</label>
      <input id="last-name-input" onChange={e => setLastName(e.target.value)} />
      <Upper>{last}</Upper>
    </div>
  )
}

export default App
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

## React.lazy and Suspense
```jsx
import React, {useState, Suspense} from 'react'

const Tilt = React.lazy(() => import('./12-react-lazy.tilt'))

function useToggle(init = false) {
  const [on, setOn] = useState(init)
  const toggle = () => setOn(!on)
  return [on, toggle]
}

function App() {
  const [showTilt, toggleTilt] = useToggle()
  return (
    <div>
      <label>
        show tilt
        <input type="checkbox" checked={showTilt} onChange={toggleTilt} />
      </label>

      <div style={{height: 150, width: 200}} className="totally-centered">
        {showTilt ? (
          <Suspense fallback={<div>loading...</div>}>
            <Tilt>
              <div className="totally-centered">vanilla-tilt.js</div>
            </Tilt>
          </Suspense>
        ) : null}
      </div>
    </div>
  )
}

export default App
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

## Suspense
```jsx
import React, {useState, Suspense} from 'react'
import fetchPokemon from './fetch-pokemon'

const cache = {}

function PokemonInfo({pokemonName}) {
  const pokemon = cache[pokemonName]
  if (pokemon === undefined) {
    const promise = fetchPokemon(pokemonName).then(
      p => (cache[pokemonName] = p),
    )
    throw promise
  }
  return <pre>{JSON.stringify(pokemon || 'Unknown', null, 2)}</pre>
}

function App() {
  const [pokemonName, setPokemonName] = useState(null)
  function handleSubmit(e) {
    e.preventDefault()
    setPokemonName(e.target.elements.pokemonName.value)
  }
  return (
    <div>
      <form onSubmit={handleSubmit}>
        <label htmlFor="pokemonName-input">Pokemon Name (ie Pikachu)</label>
        <input id="pokemonName-input" name="pokemonName" />
        <button type="submit">Submit</button>
      </form>
      <div>
        {pokemonName ? (
          <Suspense fallback={<div>loading...</div>}>
            <PokemonInfo pokemonName={pokemonName} />
          </Suspense>
        ) : null}
      </div>
    </div>
  )
}

export default App
```
[https://6jmlvqr043.csb.app/](https://6jmlvqr043.csb.app/)

:memo: **참조 자료**   
* [https://egghead.io/playlists/react-hooks-and-suspense-650307f2](https://egghead.io/playlists/react-hooks-and-suspense-650307f2)

