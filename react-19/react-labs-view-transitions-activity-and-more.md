# [```⬅️ GO BACK```](./index.md#-%EF%B8%8F-reactjs-blog-react-labs-view-transitions-activity-and-more)

## ```📘 React Labs: View Transitions, Activity, and more     ```
### [Link to the blog](https://react.dev/blog/2025/04/23/react-labs-view-transitions-activity-and-more)

# ✨ AI Research

The **React Labs update** introduces exciting experimental features, including **View Transitions** and **Activity Components**, aimed at improving UI performance and interactivity.  

### **Key Features in the Update:**  
- **View Transitions API** – Enables **smooth animations** between UI states, making transitions more fluid.  
- **Activity Components** – A new way to **manage state and interactions**, simplifying complex UI behaviors.  
- **Performance Enhancements** – Optimizations to **reduce re-renders** and improve responsiveness.  

These features are still **experimental**, but they signal React’s continued focus on **better animations, state management, and developer experience**. 

---

There are two different concepts at play here:

1. **View Transitions API (CSS-based)**: This is a browser-level feature that enables smooth animations when transitioning between different views. React is integrating this API through new components, allowing developers to easily apply transitions without manually managing CSS animations.

2. **React Transitions (State Prioritization)**: This is a React feature that lets developers mark state updates as **low priority**, meaning they won’t block urgent interactions. Previously, all state updates were treated as urgent, but now developers can defer UI changes to enhance performance.

While both concepts help improve UI transitions, they work at different layers—**one is about animations (CSS), the other is about rendering performance (React state updates)**. React is making it easier to use both seamlessly.

React’s use of `startTransition` in two different contexts can make things feel unnecessarily tangled!

Here’s what’s happening:
- **Originally (`startTransition`)**: It was introduced to control **state update priority**, allowing developers to mark updates as low-priority so UI interactions remain smooth.
- **Now (`startTransition` with View Transitions)**: React also integrates the **View Transitions API**, a browser feature for handling animated page transitions, and somehow chose to **reuse the same function name (`startTransition`)**.

### Why would React do this?
The logic is that both cases deal with **transitions**—one in terms of **rendering priority**, the other in **visual animations**. Since View Transitions often involve state updates (like changing a page view), React is leveraging `startTransition` to manage both at once.

### How does it work in View Transitions?
In this new use case, `startTransition` helps:
1. **Trigger a View Transition** when switching UI states.
2. **Ensure smooth rendering**, allowing animations to play while React efficiently updates the DOM.

---
## Syntax Podcast #901: JS News: New React & Svelte APIs, RSC Updates, Redwood and Storybook
---

Experimental React features for animations and optimized rendering.

It was presented is, like, no other framework has animation features quite like this. I don't know about that, but this has given you a nice smooth way of using view transitions in React JS well as another new API called activity.

So view transition is a element that will wrap, I guess, a component that will wrap your components to make view transitions easier within React.
And then add a transition type is a function that lets you, specify the the reason for a transition, just give you a little bit more control. But if you haven't been using view transitions, they're a really well supported browser API that allows for awesome animation features, transitioning in your app. And a lot of people get this wrong where they think it's just for when you change pages. But in reality, anytime you're doing any state change on your application, you can wrap it in a view transition, and it can either interpolate that transition or you can write custom CSS to do that transition.

And activity is kind of interesting because what it does is it basically allows you to prerender a component without having it be visible.
So it's basically an activity element with a mode prop, and that mode prop can either accept a visible or a hidden value were visible allows it to be just rendered as normally, where hidden is going to, keep the component unmounted, but, save its state and continue to render at a lower priority than anything visible on the screen. Basically, it's preheating your elements for you. So that way, if you have already loaded it, then it, like, kind of stays loaded. Or if you are going to load it, then that way, it's going to, like, make that transition for when it actually shows up to be, very fast. So, so it says that you could use the activity to save parts of the UI that the user isn't using or prerender parts that a user is likely to use next.

Parcel now supports React Server Components. And this is kind of exciting because the thing about doing React server components is, like, your both your client and your server needs to know about your entire application, and you need something that can, like, transpile JSX and on the server.
So if your server is is written in in Hono or Express or whatever, you need some sort of integration to that to sort of mate them together.
So Parcel released React Server component support. It's both a package you could just import on your server as well as part of the bundler.