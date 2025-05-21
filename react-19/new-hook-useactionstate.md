# [```⬅️ Go Back```](./features.md)

## ```From the docs                                           ```

Building on top of Actions, React 19 introduces a new hook React.useActionState to handle common cases for Actions. useActionState is a Hook that allows you to update state based on the result of a form action.

This hook provides a way to manage the state of actions, including tracking pending states and handling results. It simplifies the process of managing asynchronous operations within components.

```js
const [state, formAction, isPending] = useActionState(fn, initialState, permalink?);
```

## Reference 

```useActionState(action, initialState, permalink?)```

Call useActionState at the top level of your component to create component state that is updated when a form action is invoked. You pass useActionState an existing form action function as well as an initial state, and it returns a new action that you use in your form, along with the latest form state and whether the Action is still pending. The latest form state is also passed to the function that you provided.

```js
import { useActionState } from "react";

async function increment(previousState, formData) {
  return previousState + 1;
}

function StatefulForm({}) {
  const [state, formAction] = useActionState(increment, 0);
  return (
    <form>
      {state}
      <button formAction={formAction}>Increment</button>
    </form>
  )
}
```

The form state is the value returned by the action when the form was last submitted. If the form has not yet been submitted, it is the initial state that you pass.

If used with a Server Function, useActionState allows the server’s response from submitting the form to be shown even before hydration has completed.

See more examples below.

## Parameters 

**fn:** The function to be called when the form is submitted or button pressed. When the function is called, it will receive the previous state of the form (initially the initialState that you pass, subsequently its previous return value) as its initial argument, followed by the arguments that a form action normally receives.

**initialState:** The value you want the state to be initially. It can be any serializable value. This argument is ignored after the action is first invoked.

**optional permalink:** A string containing the unique page URL that this form modifies. For use on pages with dynamic content (eg: feeds) in conjunction with progressive enhancement: if fn is a server function and the form is submitted before the JavaScript bundle loads, the browser will navigate to the specified permalink URL, rather than the current page’s URL. Ensure that the same form component is rendered on the destination page (including the same action fn and permalink) so that React knows how to pass the state through. Once the form has been hydrated, this parameter has no effect.

## Returns 

useActionState returns an array with the following values:

1. The current state. During the first render, it will match the initialState you have passed. After the action is invoked, it will match the value returned by the action.

2. A new action that you can pass as the action prop to your form component or formAction prop to any button component within the form. The action can also be called manually within startTransition.

3. The isPending flag that tells you whether there is a pending Transition.

## Caveats 

When used with a framework that supports React Server Components, useActionState lets you make forms interactive before JavaScript has executed on the client. When used without Server Components, it is equivalent to component local state.
The function passed to useActionState receives an extra argument, the previous or initial state, as its first argument. This makes its signature different than if it were used directly as a form action without using useActionState.

## Usage 

### Using information returned by a form action 
Call useActionState at the top level of your component to access the return value of an action from the last time a form was submitted.

```js
import { useActionState } from 'react';
import { action } from './actions.js';

function MyComponent() {
  const [state, formAction] = useActionState(action, null);
  // ...
  return (
    <form action={formAction}>
      {/* ... */}
    </form>
  );
}
```

useActionState returns an array with the following items:

The current state of the form, which is initially set to the initial state you provided, and after the form is submitted is set to the return value of the action you provided.
A new action that you pass to <form> as its action prop or call manually within startTransition.
A pending state that you can utilise while your action is processing.
When the form is submitted, the action function that you provided will be called. Its return value will become the new current state of the form.

The action that you provide will also receive a new first argument, namely the current state of the form. The first time the form is submitted, this will be the initial state you provided, while with subsequent submissions, it will be the return value from the last time the action was called. The rest of the arguments are the same as if useActionState had not been used.

```js
function action(currentState, formData) {
  // ...
  return 'next state';
}
```

---

# ✨ AI Research

### About `useActionState` in React 19:

* `useActionState` **does not automatically catch thrown errors** from your action function.
* Instead, it **captures whatever your action function returns** as the state.
* So if you want to track errors with `useActionState`, your action function must **catch errors internally and return an error object** or flag.

### What that means in practice:

```jsx
function myAction(prevState, formData) {
  try {
    // do something that might fail
    return { success: true, data: someData };
  } catch (error) {
    // catch and return error info
    return { success: false, error: error.message };
  }
}

const [state, submitAction, isPending] = useActionState(myAction, {});

if (state.error) {
  // handle error display
}
```

### Important:

* If your action throws an error and you **don't catch it**, it will **bubble up** and React's error boundaries (or Suspense with error fallback) may catch it.
* `useActionState` only tracks **returned state**, not thrown exceptions.


### Summary:

| Aspect               | Behavior in `useActionState`                                                        |
| -------------------- | ----------------------------------------------------------------------------------- |
| Returned values      | Stored and exposed in `state`                                                       |
| Thrown exceptions    | Not caught by `useActionState`; bubble up                                           |
| Error handling style | You need to catch errors in your action and return them if you want them in `state` |