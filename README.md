# Handling Side Effects, Using Reducers, & Using the Context API - React Complete Guide

[React - The Complete Guide (incl Hooks, React Router, Redux)](https://www.udemy.com/course/react-the-complete-guide-incl-redux/)

Advanced and essential features for working with more complex projects

## Things Learned from this project

### useEffect()
- Effects (useEffect()) are useful to mitigate infinite render loops
  - e.g., If an https request was made upon a component render, then some piece of state was updated, the cycle would continue forever
  ```js
  useEffect(() => { ... }, [ dependencies ])
  ```
  - First argument of `useEffect` is a function that gets executed after every component evaluation IF the specified dependencies changed
  - Second argument is the dependencies
    - NOTE: provide an empty array `[]` as dependencies for the Effect to only run on component FIRST render
    - State updating functions never change, so they do not need to be in dependencies
  - SEE:
    - App.js for useEffect with no dependencies (only runs on first component render)
    - Login.js for useEffect with dependencies that updates on input change
  - Cleanup with useEffect
    - Can do a rigged debouncing (I have only ever used lodash) with setTimeout
      - Put timer in useEffect hook
      - Clear timer every time a new one runs, so there is only ever one timer at any given moment/render
      - can clear timer before the next one is called
    - useEffect can return a function (and only a function) - called a cleanup function
    - SEE: Login.js for useEffect cleanup function

### useReducer()
- useReducer - "more powerful state management" - useful when state becomes complex
  - when you have state updates that depend on other states
  - when you have multiple related states
  - could have problem such as:
    ```js
    setFormIsValid(
      event.target.value.includes('@') && enteredPassword.trim().length > 6
    )
    ```
    - which could cause problems based on it updating from 2 other states, so the function form can't be used because it doesn't depend on it's own previous state, but on the previous state of the 2 others
```js
// useReducer()
const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn)
```
- Reducer function can be defined outside of scope of component, as it doesn't need to interact with any data that is defined inside of the component function
  - All data required by function will be passed into function automatically by react when it is called
- React guarantees that the state passed to the reducer function is the absolute latest state
- Use object destructuring with alias to avoid re-running useEffect too many times
```js
const { attribute: attributeAlias } = someObject
```
  - The dependency array should only be filled with the *properties* that need to be watched for, and not the entire object
  ```js
  // Good Example
  useEffect(() => {
    ...
  }, [somePropertyOfSomeObject]) // alias from destructuring

  // Bad Example
  useEffect(() => {
    ...
  }, [someObject])
  ```

### Context
- React, built-in state storage that can be used to trigger actions to pass data only to the interested component
  - Reduces/removes data chaining
- Use the `Context.Provider` syntax around the components which will need the data
- Use the `Context.Consumer` in the JSX of the component that needs to render based on the data in the context
  - Can return directly in the JSX a function that takes the object provided by the context
  ```js
  <AuthContext.Consumer>
    {(ctx) => {
      return (
        <JSX thatWas={hereBefore}>
          <thatUses>
            {ctx.property && (
              <li>
                Some data
              </li>
            )}
          </thatUses>
        </JSX>
      )
    }}
  </AuthContext.Consumer>
  ```
  - Easier to use with `useContext()` hook
  ```js
  // less convoluted JSX code in the return
  const ctx = useContext(authContext)
  ```
- Can move all of the logic for login/logout from the App component and move it all into the auth-context.js file where everything is consolidated
  - Then export the Provider and wrap the entire (index.js) app in the Provider where every component gets access to the data in auth-context if it uses `useContext` hook within the component
- Limitations:
  - Should not replace component configuration
    - props for configuration, context for state management across the entire app
  - NOT optimiized for high frequency changes
  - Should not be used to replace all component communications and props
    - But still might be useful to replace long component prop chains

### Rules of Hooks
1. Only call React Hooks in React Functions (Component Functions/Custom Hooks)
2. Only call React Hooks at the top level (don't call in nested/block statements)
3. Always add everything referred to inside of useEffect() as a dependency (unless there's a good reason not to, such as state updating functions which are guaranteed to never change)

## Other things to note

- Need to look up more examples/use cases for forwardRef/useImperativeHandle

## To run this project
Clone this repo and cd into the project folder
```bash
npm install
npm start
```