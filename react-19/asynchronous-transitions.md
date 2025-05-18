# [```⬅️ Go Back```](./features.md)

# ✨ AI Research

React 19 continues to support asynchronous transitions by allowing asynchronous functions to be passed to `startTransition`. This enables non-blocking state updates, enhancing UI responsiveness.

You can now perform data fetching or any async work inside startTransition(), and React will wait for it before updating the UI — making transitions smooth, predictable, and flicker-free.

* This is the **low-level mechanism**.
* You can write:

  ```js
  startTransition(async () => {
    const data = await fetchStuff();
    setState(data);
  });
  ```

Better Integration with Suspense:
React 19 can pause a transition until all ```await```ed data is ready or all Suspense boundaries are resolved. This means:

- Your UI feels smoother
- No partial renders
- Fewer spinners and loading flickers

| Before (React 18)                                   | Now (React 19)                                                |
| --------------------------------------------------- | ------------------------------------------------------------- |
| Async functions in `startTransition()` were ignored | `async` transitions are now fully supported                   |
| UI could update **before** data was ready           | UI waits for **async logic to finish** in transitions         |
| Hard to combine async fetch + optimistic updates    | Easy to combine both with `useOptimistic` + async transitions |
