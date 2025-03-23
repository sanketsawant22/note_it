# React Interview Questions & Notes

## 1. Difference between `useState` and a normal variable in React
- `useState` is a React Hook that manages state and triggers re-renders when updated.
- A normal variable does not trigger re-renders when changed.
- Example:
  ```jsx
  const [count, setCount] = useState(0); // Updates UI when setCount is called
  let countVar = 0; // UI won't update when changed
  ```

## 2. What is Prop Drilling, and how to avoid it?
- **Prop Drilling**: Passing props down multiple levels in a component tree.
- **Ways to avoid it:**
  - Use **React Context API**
  - Use **Redux** for global state
  - Use **component composition**

## 3. How does lifting state up help in React?
- **Lifting state up** moves state to a common parent component so that multiple child components can share it.
- Prevents duplicate state and ensures a single source of truth.
- Example:
  ```jsx
  function Parent() {
    const [value, setValue] = useState("");
    return <Child value={value} setValue={setValue} />;
  }
  ```

## 4. What is `useEffect`, and when should you use it?
- A Hook used for **side effects** (e.g., fetching data, event listeners).
- Runs after render.
- Common use cases:
  - Fetching data (`useEffect(() => { fetchData(); }, []);`)
  - Subscriptions & cleanup (`useEffect(() => { subscribe(); return cleanup; }, [])`)

## 5. What is `React.memo`, and when should you use it?
- **Higher-order component (HOC)** for memoizing functional components.
- Prevents unnecessary re-renders by comparing previous and new props.
- Use it when a component renders frequently with the same props.
- Example:
  ```jsx
  const MemoizedComponent = React.memo(MyComponent);
  ```

## 6. Difference between controlled and uncontrolled components
- **Controlled Component**: State is managed by React (`useState` or props).
- **Uncontrolled Component**: State is managed by the DOM (`ref`).
- Example:
  ```jsx
  <input value={value} onChange={(e) => setValue(e.target.value)} /> // Controlled
  <input ref={inputRef} /> // Uncontrolled
  ```

## 7. Difference between `useCallback` and `useMemo`
- **`useCallback(fn, deps)`**: Memoizes a function to prevent re-creation.
- **`useMemo(fn, deps)`**: Memoizes the result of a function.
- Example:
  ```jsx
  const memoizedFn = useCallback(() => expensiveFn(), [deps]);
  const memoizedValue = useMemo(() => expensiveComputation(), [deps]);
  ```

## 8. React Reconciliation and Virtual DOM
- **Reconciliation**: Process of updating the DOM efficiently by comparing Virtual DOM.
- **Virtual DOM**:
  - A lightweight copy of the real DOM.
  - React updates only changed elements instead of re-rendering everything.

## 9. What are React Fragments, and why are they useful?
- A wrapper that allows multiple elements without adding extra DOM nodes.
- Example:
  ```jsx
  <>
    <h1>Hello</h1>
    <p>World</p>
  </>
  ```
  Equivalent to `<React.Fragment></React.Fragment>`.

## 10. Difference between `React.createElement` and JSX
- `React.createElement` is the raw API (`React.createElement('div', {}, 'Hello')`).
- JSX is syntactic sugar that compiles to `React.createElement`.
- Example:
  ```jsx
  const element = <h1>Hello</h1>; // Compiles to React.createElement
  ```

## 11. How does React Context API help in state management?
- Provides global state without prop drilling.
- Example:
  ```jsx
  const MyContext = React.createContext();
  <MyContext.Provider value={data}><Child /></MyContext.Provider>
  ```
  Access with `useContext(MyContext)`.

## 12. What is lazy loading in React, and how does it improve performance?
- **Lazy loading** dynamically loads components only when needed, reducing initial load time.
- Implement using `React.lazy()` and `Suspense`:
  ```jsx
  const LazyComponent = React.lazy(() => import('./Component'));
  ```

## 13. Difference between Server-side Rendering (SSR) and Client-side Rendering (CSR)
- **SSR**:
  - Renders on the server, sends HTML to the client.
  - Faster initial load, better SEO.
- **CSR**:
  - Renders in the browser after JavaScript loads.
  - More interactive but slower initial load.

## 14. What is Suspense in React, and how does it work?
- A component that **handles async rendering** (e.g., lazy loading components or data fetching).
- Example:
  ```jsx
  <Suspense fallback={<Loading />}>
    <LazyComponent />
  </Suspense>
  ```

## 15. How do error boundaries work in React?
- **Catch JavaScript errors in components** and prevent UI crashes.
- Implement using `componentDidCatch` in class components.
- Example:
  ```jsx
  class ErrorBoundary extends React.Component {
    state = { hasError: false };
    componentDidCatch(error, info) {
      this.setState({ hasError: true });
    }
    render() {
      return this.state.hasError ? <h1>Something went wrong.</h1> : this.props.children;
    }
  }
  ```

