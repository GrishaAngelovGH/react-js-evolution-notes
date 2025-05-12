# [```⬅️ Go Back```](./react-server-components.md#resource-7-streaming-components)

## ✨ AI Research 

### React streams server-rendered responses incrementally

Alright! Let's explore how **React streams server-rendered responses incrementally**, making UI updates **more efficient and smooth**.

---

### **🔹 How React Streams Server Responses**
React Server Components (RSC) use **streaming** to **send parts of the UI progressively** instead of waiting for the full response. This prevents long delays and makes **early parts of the UI available faster**.

#### **Key Benefits of Streaming**
✔ **Faster initial rendering**—users see something **while data is still loading**  
✔ **Progressive hydration**—React attaches interactivity **as content arrives**  
✔ **Efficient large page loads**—no need to block rendering **until everything is ready**  

---

### **✅ Example: Incremental Streaming in React**
Imagine we have a component that fetches **product details** in chunks instead of all at once.

#### **Step 1: Define the Server Component**
```jsx
import { Suspense } from "react";

function ProductPage() {
  return (
    <div>
      <h1>Product Details</h1>

      {/* Streaming: Show basic info first, then load reviews separately */}
      <Suspense fallback={<p>Loading reviews...</p>}>
        <ProductReviews />
      </Suspense>
    </div>
  );
}
```

---

#### **Step 2: Define the Streaming Server Component**
```jsx
"use server"; // Marks this as a server component

async function ProductReviews() {
  await new Promise((resolve) => setTimeout(resolve, 3000)); // Simulate delay

  return (
    <ul>
      <li>⭐️⭐️⭐️⭐️⭐️ - Amazing product!</li>
      <li>⭐️⭐️⭐️⭐️ - Good, but could be better.</li>
    </ul>
  );
}
```

---

### **🔹 How This Works**
1️⃣ **The client receives the initial UI (`ProductPage`) immediately.**  
2️⃣ **The server component (`ProductReviews`) takes longer to load.**  
3️⃣ **The UI doesn’t freeze**—instead, it shows **"Loading reviews..."** temporarily.  
4️⃣ **Once the server resolves `ProductReviews`, React automatically updates the UI.**  

---

### **🔹 Why Streaming is Powerful**
- **Users interact with the page before full content arrives.**
- **Improves performance** by reducing time-to-first-paint.
- **Reduces blocking delays**, especially for **large applications**.

### ▶️ Recently, in React and other contexts, the term 'streaming' components to the client has become common. I think this is just a fancy way of describing something that was used before—what term was used previously?

✨ You're onto something! The term **"streaming components"** in React is a relatively new way of describing an approach that was previously handled through **Server-Side Rendering (SSR) with progressive hydration**.

### **What Was Used Before Streaming?**
Before React introduced **streaming**, developers relied on:
1. **Traditional SSR (Server-Side Rendering)**  
   - The server would generate **a full HTML page** and send it to the client.
   - The browser would then **hydrate** the page by attaching React’s interactivity.
   - The downside? **Users had to wait for the entire page to load before interacting**.

2. **Progressive Hydration**  
   - React improved SSR by **hydrating components incrementally**.
   - This allowed parts of the UI to become interactive **before the full page was ready**.
   - However, it still required **waiting for the full HTML response** before hydration could begin.

### **How Streaming Changed Things**
React **streams UI updates** in chunks, meaning:
- The **server sends parts of the UI progressively** instead of waiting for the full page.
- The browser **renders available content immediately**, improving perceived performance.
- **Suspense works better**, allowing placeholders while waiting for data.

This approach is **not entirely new**—it builds on ideas from **incremental rendering** and **progressive hydration**, but React now makes it **more efficient and automatic**.

### ▶️ Is this streaming related to RSC?

✨ Yes! **Streaming is deeply connected to React Server Components (RSC)**. It’s a fundamental mechanism that makes RSC possible and efficient.

### **🔹 How Streaming Relates to RSC**
React Server Components allow **components to be rendered on the server** and sent to the client **incrementally**—this is where streaming comes in.

1️⃣ **Server Constructs UI in Chunks**  
   - Instead of generating the **entire page at once**, React **streams** components progressively.  
   - This means users **see parts of the page while the rest is still loading**.

2️⃣ **Streaming Improves Server Components Delivery**  
   - RSC avoids sending bulky JavaScript bundles to the browser.  
   - Instead, React streams a **structured component tree** to the client.

3️⃣ **Suspense Handles Streaming UI Smoothly**  
   - React **renders placeholders** while waiting for streamed content.  
   - Once the server sends a component’s data, React swaps in the real UI.

---

### **🔹 Why Streaming is a Game-Changer for RSC**
✔ **Less JavaScript sent to the client** → Faster page loads  
✔ **Server Components can hydrate progressively** → No waiting for full renders  
✔ **User interactions aren’t blocked** → Smooth, fast experiences  

So when React talks about **“streaming components”**, it’s referring to **sending chunks of UI data from the server instead of a full HTML response**, making RSC more efficient! 🚀  

Want an example of how RSC uses streaming in a real-world app? 😊

Awesome! Let's walk through a **real-world example** of React Server Components (RSC) using **streaming** to improve performance.

---

### **🔹 Example: Streaming Server Components for a Product Page**
Imagine an e-commerce website where the product details load **instantly**, but **reviews** take longer to fetch from the server. Instead of making users wait for everything, **React streams the UI in chunks**.

---

#### **Step 1: Define a Server Component that Streams Reviews**
```jsx
"use server"; // This tells React to execute on the server

async function ProductReviews() {
  await new Promise((resolve) => setTimeout(resolve, 3000)); // Simulating delay

  return (
    <ul>
      <li>⭐️⭐️⭐️⭐️⭐️ - Amazing product!</li>
      <li>⭐️⭐️⭐️⭐️ - Great quality but pricey.</li>
    </ul>
  );
}
```

---

#### **Step 2: Use Suspense to Handle Streaming UI**
```jsx
import { Suspense } from "react";

function ProductPage() {
  return (
    <div>
      <h1>Product Details</h1>
      <p>This is a top-rated item available for purchase!</p>

      {/* Reviews are streamed, so users see the page immediately */}
      <Suspense fallback={<p>Loading reviews...</p>}>
        <ProductReviews />
      </Suspense>
    </div>
  );
}
```

---

### **🔹 How This Works**
1️⃣ **Initial content (product details) loads instantly.**  
2️⃣ **Reviews are fetched asynchronously on the server.**  
3️⃣ **Suspense shows "Loading reviews..." temporarily.**  
4️⃣ **Once streaming completes, reviews replace the placeholder!**  

---

### **🔹 Why Streaming Works Well with RSC**
✔ **Users never see a blank page**—the UI is interactive immediately.  
✔ **Less JavaScript sent to the browser**—no need for bulky client-side state updates.  
✔ **Better performance for large applications**—complex data loads **progressively** instead of blocking rendering. 

---

### **🔹 Hydration vs. Streaming in React**
React Server Components (RSC) introduced **streaming**, which helps send UI updates progressively. This differs from traditional **hydration**, where the browser attaches JavaScript to already-rendered HTML.

Here’s a breakdown:

| Feature          | Hydration (Traditional SSR) | Streaming (React Server Components) |
|-----------------|---------------------------|-------------------------------------|
| **How it Works** | Pre-rendered HTML sent first, then JavaScript adds interactivity. | UI components are sent **incrementally** in chunks. |
| **Rendering Approach** | The full page is rendered at once, then hydrated. | Components are **rendered progressively** as data arrives. |
| **JavaScript Behavior** | Browser downloads and executes JavaScript **all at once**. | JavaScript executes **only where needed**, reducing initial load. |
| **User Experience** | Might feel **slow** on large pages with lots of JavaScript. | UI appears **instantly** while deeper content loads. |
| **Use Case** | Good for simple static pages. | Best for **dynamic apps** with large amounts of data. |

---

### **✅ Example: Hydration vs. Streaming**
#### **Hydration (Traditional SSR)**
```jsx
import { hydrateRoot } from "react-dom";

hydrateRoot(document.getElementById("root"), <App />);
```
🚀 This approach sends the **entire HTML upfront**, then hydrates it with JavaScript **after page load**.

#### **Streaming (React Server Components)**
```jsx
import { Suspense } from "react";

function App() {
  return (
    <div>
      <h1>Page Content</h1>
      <Suspense fallback={<p>Loading data...</p>}>
        <ServerComponent />
      </Suspense>
    </div>
  );
}
```
🚀 **Only some content loads immediately** while React **streams the rest incrementally**, making pages faster!

---

### **🔹 Why Streaming is Better for RSC**
✔ **Less JavaScript shipped** → Faster initial load  
✔ **Improves perceived performance** → Users see content sooner  
✔ **Works seamlessly with Suspense** → Avoids blank screens while waiting for data  

### ▶️ Explain Server-Side Rendering (SSR) with progressive hydration.

✨ **Server-Side Rendering (SSR) with Progressive Hydration** is a technique in React that allows the browser to render a **fully server-generated page** while gradually attaching JavaScript behaviors to different parts of the UI.

---

### **🔹 How It Works**
1️⃣ **SSR Generates Full HTML on the Server**  
   - The server constructs a complete HTML page and sends it to the client.  
   - This ensures that users **see content immediately** rather than waiting for JavaScript to load.  

2️⃣ **Progressive Hydration Attaches Interactivity Over Time**  
   - Instead of hydrating the entire page **at once**, React **gradually hydrates different parts** as JavaScript loads.  
   - This prevents blocking the main thread and improves page responsiveness.  

---

### **🔹 Why Progressive Hydration Is Useful**
✔ **Faster initial page load** → HTML is visible before JavaScript is ready  
✔ **Smooth performance** → Hydration happens in **small chunks** instead of all at once  
✔ **Better user experience** → Users can interact with parts of the UI **while hydration is still happening**  

---

### **✅ Example: SSR with Progressive Hydration**
```jsx
import { hydrateRoot } from "react-dom";

hydrateRoot(document.getElementById("root"), <App />);
```
🚀 **Hydration happens incrementally**, ensuring that the most critical parts of the UI become interactive first.

---

### **🔹 SSR vs. Progressive Hydration vs. Streaming**
| Feature                | Traditional SSR | Progressive Hydration | Streaming (React Server Components) |
|------------------------|----------------|----------------------|-------------------------------------|
| **Rendering Approach** | Full page sent at once | Parts hydrate gradually | UI updates **stream progressively** |
| **JavaScript Execution** | Blocks until hydration completes | Attaches interactivity in chunks | Minimal JS needed for rendering |
| **User Experience** | Feels slow if JS is large | Feels smoother | Feels instant and dynamic |

### ▶️ What is meant by Server-Side Rendering (SSR) with progressive hydration? Why is it separated like that?

✨ Great question! The reason **Server-Side Rendering (SSR) and Progressive Hydration** are often discussed together but **treated as distinct concepts** is because **SSR handles the rendering phase**, while **progressive hydration manages the transition to interactivity** in the browser.

### **🔹 Why We Separate SSR & Progressive Hydration**
1️⃣ **SSR (Server-Side Rendering) → Generates the Initial HTML**
   - The **server** constructs a fully rendered HTML page.
   - This means users **see content immediately** before any JavaScript runs.
   - But this initial page is **static**—it doesn’t have React’s interactive behavior yet.

2️⃣ **Progressive Hydration → Gradually Adds Interactivity**
   - The browser **attaches JavaScript to different parts of the UI step by step**, rather than all at once.
   - React **prioritizes critical components first**, improving responsiveness.
   - This **prevents blocking the main thread**, ensuring smoother performance.

### **🔹 Why This Matters**
✔ **SSR handles fast first-paint** → Users don’t wait for an empty screen.  
✔ **Progressive hydration prevents slow startup** → JavaScript loads in chunks, avoiding lag.  
✔ **Reduces time-to-interactivity** → Interactive UI elements become functional faster.  

### **✅ Example: Why Progressive Hydration Helps**
Imagine a news website:
- **SSR quickly renders an article's HTML** → Users **see the text immediately**.  
- **Progressive hydration activates features gradually** → Comments **load later**, rather than delaying the whole page.  

------------------------------------------------------------------------

▶️ How does RSC stream the response? Is it because it returns data to the client with placeholders that can be filled later?

✨ Yes, that's a great way to describe it! React Server Components (RSC) achieve streaming by sending data to the client in chunks, including "placeholders" that get filled in later.

How it works:
1. Initial Response with Placeholders – When the server begins responding, it can send a lightweight structure where some data is missing or pending.

2. Streaming Partial Data – As the server fetches more information, it streams those updates to the client.

3. Client Fills in Missing Parts – React can progressively update the UI as chunks arrive, replacing placeholders with actual content.

Why is this powerful?

- The user sees content faster rather than waiting for the entire page to load.
- Components can be lazy-loaded, making interactions smoother.
- Works seamlessly with Suspense, allowing loading states until all data arrives.

---

📘 Good use case to wrapping something into Suspense boundary

# ✨ AI Research

Suspense waits for an asynchronous component (whether it's fetching data or loading a dynamically imported component).

Suspense can wait for a normal component to load—even if it doesn’t fetch data—as long as that component is asynchronous or is being lazy-loaded. If the component is not async or code-split, Suspense has no effect—it won't introduce a delay.

Usecases:
- If it feels slow because of the data source
- Or have a lot of data that needs to be downloaded

---

▶️ What means "out of order streaming" in the context of RSC?

✨ "Out-of-order streaming" in the context of React Server Components (RSC) refers to the ability to send pieces of a response as they become ready, rather than in a fixed sequential order.

How does it work?
- Normally, traditional server rendering waits for all data to be ready before sending the full response.
- With out-of-order streaming, React can send parts of the UI separately, without delaying the entire page.
- Some components may load faster than others, allowing React to render them immediately, while slower components arrive later.

Example Scenario

Imagine an e-commerce page:
- The header and navigation are ready instantly → Sent first.
- The product list takes longer because it's fetching from a database → Sent later.
- The user reviews arrive even later, but don’t block earlier content.

Why is this powerful?
- Users see content faster, improving perceived performance.
- React prioritizes what’s ready first instead of waiting for everything.
- Works well with Suspense, allowing placeholders while waiting for delayed components.

Out-of-order streaming lets parts of an RSC-powered app load as soon as they are ready, rather than waiting for everything, making pages feel faster and more efficient.

---

▶️ So is it enabled by default in RSC and happens automatically behind the scenes?

✨ Yes!Out-of-order streaming is enabled by default in React Server Components (RSC) and happens automatically behind the scenes whenever you use server rendering.

How It Works Automatically:
- React doesn’t wait for the entire response to be ready before sending data to the client.
- Instead, it streams parts of the UI as soon as they’re ready, ensuring a faster user experience.
- You don’t need to manually enable anything—React handles streaming out of the box.

Does Suspense Help?
While RSC naturally streams responses, Suspense improves the experience by showing fallback UI for components that are still loading.

This makes streaming more user-friendly by preventing incomplete UI flashes.

React automatically streams UI out-of-order, and Suspense helps manage loading states—but no special setup is required.

---
## Syntax Podcast #493: Supper Club × Lee Robinson on React Suspense, Server Components
---

✅ Topic: What is React Suspense?

Especially for folks who've written Node. Js code before, or, you know, browser code that's using async await. As you understand this model of I have some asynchronous code, maybe it's a fetch to some API or a request to go get data out of my database, and I wanna wait for that code to finish and then use that value somewhere else in my application.

And the interesting thing about suspense is it brings that mental model to your React code base.

So for for experienced React developers or or folks who've built apps with a, you know, a client side fetch side of a a useEffect, which Has some nuance to it.

They might have fell into a state where they are Rendering kind of a a placeholder or a loading state. And then when that request resolves, then it swaps in, the state updates, and it shows the new value. The really nice thing about suspense is it's more of a a built in primitive to express asynchronous code in the React ecosystem.

And with React 18 and kind of the future direction of React, It's really unlocking some interesting and unique possibilities. 

✅ Topic: Selective hydration unlocks perf possibilities

So you wrap a component or a, you know, a component tree with a suspense boundary, And you can, you know, progressively hydrate that part of the page as you scroll to it. So for example, on our landing page on the Next. Js landing page, actually, you know, we were loading the JavaScript to hydrate the entire page. And I was looking at the, the lighthouse run or, like, a performance run, and I saw there's an opportunity. Oh, you can improve importance by, you know, Including less JavaScript on the initial page load. And all I had to do was just put a suspense boundary around the components that I wanted to defer The hydration for. And it was wild. I just didn't realize how easy it was to enable that with React 18. 

So hydration isn't a problem necessarily with normal client side render sites. Right? Because The JavaScript loads the the site, and that's the JavaScript. Right? But with when you have a server rendered site, you're using JavaScript to both render the server site as well as Interactivity in the UI. So you render the site, then hydration, comes about. And, basically, it's readding that JavaScript client side To hydrate the DOM with the the the client side JavaScript using the same, essentially, the same React code.

✅ Topic: Next.js makes hydration easy compared to custom React

the big benefit there is just reduced JavaScript load for the user. Right? Reduced initial JavaScript load. Yeah. Specifically, the metric to look at is the time to interactive. So by shipping less JavaScript, you're able to make that page interactive more quickly.

And to your point, when you're server rendering and you're prerendering some HTML from the server, You wait for that JavaScript to load or hydrate so you can actually click on some stuff.

It's better for performance, better for your your customers, your visitors If they can, you know, immediately click somewhere else on the page if they wanna go somewhere, they don't have to wait for

some JavaScript to be loaded and have A lower time to interactive. Oh, that's cool. So, like, the way I'm understanding that is, like, let's say you have, part of your your website that you don't necessarily care to have the client side JavaScript to be rendered because Could it just be like a static component like a nav like a like a menu or something like that? Is that where you'd pop a a suspense component around? That's not obviously not Just what suspense is for, but is that what you're explaining here? So in this instance, the progressive hydration or selective hydration, I'm not sure what the actual specific, description word is there, but you're you're deferring the loading of the the JavaScript there. It's not necessarily that you'll have no JavaScript.

✅ Topic: Deferring JavaScript with Suspense improves interactivity

It's actually you know that you will have JavaScript.

You're just saying it's not important or as important for the initial page load. So in the context of, marketing page, it's like, well, these these sections down towards the bottom, they're not in the initial paint.

So let's just help improve the performance and only load that as we kinda get it into the viewport. Now there is a future improvement to make there that Next. Js can help Unlock with server components, which is for this section, for example, this is just HTML.

There's no JavaScript. Maybe we don't even need to load JavaScript there. Anything at all.

And that that's very similar to The defer attribute in on a script tag as well. Right? Exactly. Exactly.

That makes sense. You're saying, okay. Like, I'd like to load this thing, but it's not, Like, maybe it's analytics or, something that is not that important to get the thing running, and we shouldn't hold everything up, while we download this little bit of JavaScript.

✅ Topic: How does Suspense know when its children are done loading?

✅ Topic: How does all that connects to server components?

At first the vision with RSC was to use data inside your component, 
fetched directly from your database. At the time it was released the react
team said "we heard your feedback - developers want to ship less js on the client side when they are using React and want to have more control".