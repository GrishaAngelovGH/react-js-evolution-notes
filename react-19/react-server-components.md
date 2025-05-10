# [```⬅️ Go Back```](./features.md)

---

### Resource 1: [🔗 Making Sense of React Server Components w/ Josh Comeau](https://www.joshwcomeau.com/react/server-components/)

### Resource 2: [React for Two Computers /w Dan Abramov](#react-for-two-computers-w-dan-abramov)

### Resource 3: [From the blog](#from-the-blog)

### Resource 4: [AI Research](#%EF%B8%8F-react-server-components--suspense-workflow)

### Resource 5: [Syntax Podcast](#syntax-podcast-718-react-server-components)

### Resource 6: [RSC: A New Way to Build Fast and Interactive Web Apps w/ Aurora Scharff](#rsc-a-new-way-to-build-fast-and-interactive-web-apps-w-aurora-scharff)

---
# ✨ AI Research

### How React Server Components Blend Different Rendering Methods

* **MPA (Multi-Page Application)**

  * *Where:* The **server controls the overall navigation and routing**.
  * *Integration:* Each page request hits the server, which generates a fresh React tree on the server. So the initial page load behaves like a traditional MPA, sending a full HTML response.

* **SSR (Server-Side Rendering)**

  * *Where:* React components (including server components) are **rendered to HTML on the server** before sending to the client.
  * *Integration:* The server sends fully rendered HTML with minimal JavaScript. This enables fast initial loads and great SEO.

* **Client-Side Rendering (CSR)**

  * *Where:* Only **interactive parts (client components)** hydrate and run JavaScript on the client.
  * *Integration:* After receiving the server-rendered HTML, React hydrates just the necessary parts on the client, allowing interactivity without sending the full app bundle.

### React Server Components provide:

* **Partial hydration:** The server sends HTML for non-interactive components and hydrates only interactive parts on the client (bridging SSR + CSR).
* **Streaming:** Server streams HTML and component data progressively, improving perceived load time (leveraging SSR’s streaming).
* **Reduced client JavaScript:** Because logic and data fetching happen server-side, less JS is sent to the browser (like in MPA, which sends fresh pages from the server).
* **Seamless transitions:** Navigation feels as smooth as CSR, but with server-rendered content replacing the full page (blending MPA navigation with CSR speed).

---

## React for Two Computers /w Dan Abramov

### [Link to YouTube](https://www.youtube.com/watch?v=ozI4V_29fj4&ab_channel=ReactConf)  
### [Link to the blog](https://overreacted.io/react-for-two-computers/)

The talk explains the basic principle of RSC.

The relationship between two parts communicating with one another follows a common pattern across different fields, including computing—where, for example, a server sends data or HTML to a client.

The demo begins with an app in which the data is initially hardcoded on the client. In a real app, we don't want hardcoded values, and actually, we will use some dynamic values, i.e., fetched from a database.

So, traditionally, there would be a defined server endpoint to which the client will make a request. But now the behavior of the app is changed: when the UI is rendered, there is no data immediately available to be visualized. It needs to be fetched, which takes time, and under poor network conditions, the app would be significantly slower.

In a lot of cases, this is fine, but we want to have control over the UI, where we want to be able to say: this particular interaction has to be synchronous, like we want the data to be already there with that screen, and we don't want to wait for it to come later. We want to be able to ensure low latency for specific interactions.

So the question is: how do we achieve this?

So there is not much that we could do from the client-side perspective because this is the workflow, i.e., wait for the data to be fetched and visualize it after that. This is why we go to the server.

In the demo, the server has a defined endpoint for URL "/api/cat-names," and for "/", it returns an HTML page.

Previously, we have looked at the server code and client code as two separate programs that talk to each other. But now, the different perspective is: a single program that spans two devices, i.e., computers—server and client machines. And it is evaluated in steps.

This approach provides optimization opportunities, i.e., to simplify it. Similar to "lifting the state up" in React, we could do "lifting the data up", i.e., move the fetching of the data to the server, where we would just get it directly from, let's say, a database and not have to call another API endpoint to do it.

And now we don't have an additional network request from the client because the data is already there on the server, and it includes it in the HTML that will return to the client.

And for the server to have data ahead of time, it means that the server could process it and return only the relevant data, i.e., a small amount if needed, to the client, i.e., from the whole array of cat names that was sent before, it could send only one name.

So we have code that executes first on the server, and it emits another code that will be sent to the client and executed later, and we could pass some data together with this client code.

---

## From the blog

Server Components are a new option that allows rendering components ahead of time, before bundling, in an environment separate from your client application or SSR server. This separate environment is the “server” in React Server Components. Server Components can run once at build time on your CI server, or they can be run for each request using a web server.

## ✅ Server Components without a Server 

Server components can run at build time to read from the filesystem or fetch static content, so a web server is not required. For example, you may want to read static data from a content management system.

Without Server Components, it’s common to fetch static data on the client with an Effect:

```js
// bundle.js
import marked from 'marked'; // 35.9K (11.2K gzipped)
import sanitizeHtml from 'sanitize-html'; // 206K (63.3K gzipped)

function Page({page}) {
  const [content, setContent] = useState('');
  // NOTE: loads *after* first page render.
  useEffect(() => {
    fetch(`/api/content/${page}`).then((data) => {
      setContent(data.content);
    });
  }, [page]);
  
  return <div>{sanitizeHtml(marked(content))}</div>;
}
// api.js
app.get(`/api/content/:page`, async (req, res) => {
  const page = req.params.page;
  const content = await file.readFile(`${page}.md`);
  res.send({content});
});
```

This pattern means users need to download and parse an additional 75K (gzipped) of libraries, and wait for a second request to fetch the data after the page loads, just to render static content that will not change for the lifetime of the page.

With Server Components, you can render these components once at build time:

```js
import marked from 'marked'; // Not included in bundle
import sanitizeHtml from 'sanitize-html'; // Not included in bundle

async function Page({page}) {
  // NOTE: loads *during* render, when the app is built.
  const content = await file.readFile(`${page}.md`);
  
  return <div>{sanitizeHtml(marked(content))}</div>;
}
```
The rendered output can then be server-side rendered (SSR) to HTML and uploaded to a CDN. When the app loads, the client will not see the original Page component, or the expensive libraries for rendering the markdown. The client will only see the rendered output:

```html
<div><!-- html for markdown --></div>
```

This means the content is visible during first page load, and the bundle does not include the expensive libraries needed to render the static content.

Note
You may notice that the Server Component above is an async function:

```js
async function Page({page}) {
  //...
}
```

Async Components are a new feature of Server Components that allow you to await in render.

## ✅ Server Components with a Server 

Server Components can also run on a web server during a request for a page, letting you access your data layer without having to build an API. They are rendered before your application is bundled, and can pass data and JSX as props to Client Components.

Without Server Components, it’s common to fetch dynamic data on the client in an Effect:

```js
// bundle.js
function Note({id}) {
  const [note, setNote] = useState('');
  // NOTE: loads *after* first render.
  useEffect(() => {
    fetch(`/api/notes/${id}`).then(data => {
      setNote(data.note);
    });
  }, [id]);
  
  return (
    <div>
      <Author id={note.authorId} />
      <p>{note}</p>
    </div>
  );
}

function Author({id}) {
  const [author, setAuthor] = useState('');
  // NOTE: loads *after* Note renders.
  // Causing an expensive client-server waterfall.
  useEffect(() => {
    fetch(`/api/authors/${id}`).then(data => {
      setAuthor(data.author);
    });
  }, [id]);

  return <span>By: {author.name}</span>;
}
// api
import db from './database';

app.get(`/api/notes/:id`, async (req, res) => {
  const note = await db.notes.get(id);
  res.send({note});
});

app.get(`/api/authors/:id`, async (req, res) => {
  const author = await db.authors.get(id);
  res.send({author});
});
```
With Server Components, you can read the data and render it in the component:

```js
import db from './database';

async function Note({id}) {
  // NOTE: loads *during* render.
  const note = await db.notes.get(id);
  return (
    <div>
      <Author id={note.authorId} />
      <p>{note}</p>
    </div>
  );
}

async function Author({id}) {
  // NOTE: loads *after* Note,
  // but is fast if data is co-located.
  const author = await db.authors.get(id);
  return <span>By: {author.name}</span>;
}
```

The bundler then combines the data, rendered Server Components and dynamic Client Components into a bundle. Optionally, that bundle can then be server-side rendered (SSR) to create the initial HTML for the page. When the page loads, the browser does not see the original Note and Author components; only the rendered output is sent to the client:

```html
<div>
  <span>By: The React Team</span>
  <p>React 19 is...</p>
</div>
```

Server Components can be made dynamic by re-fetching them from a server, where they can access the data and render again. This new application architecture combines the simple “request/response” mental model of server-centric Multi-Page Apps with the seamless interactivity of client-centric Single-Page Apps, giving you the best of both worlds.

## ✅ Adding interactivity to Server Components 

Server Components are not sent to the browser, so they cannot use interactive APIs like useState. To add interactivity to Server Components, you can compose them with Client Component using the "use client" directive.

Note:
There is no directive for Server Components. 
A common misunderstanding is that Server Components are denoted by "use server", but there is no directive for Server Components. The "use server" directive is used for Server Functions.

In the following example, the Notes Server Component imports an Expandable Client Component that uses state to toggle its expanded state:

```js
// Server Component
import Expandable from './Expandable';

async function Notes() {
  const notes = await db.notes.getAll();
  return (
    <div>
      {notes.map(note => (
        <Expandable key={note.id}>
          <p note={note} />
        </Expandable>
      ))}
    </div>
  )
}
// Client Component
"use client"

export default function Expandable({children}) {
  const [expanded, setExpanded] = useState(false);
  return (
    <div>
      <button
        onClick={() => setExpanded(!expanded)}
      >
        Toggle
      </button>
      {expanded && children}
    </div>
  )
}
```

This works by first rendering Notes as a Server Component, and then instructing the bundler to create a bundle for the Client Component Expandable. In the browser, the Client Components will see output of the Server Components passed as props:

```html
<head>
  <!-- the bundle for Client Components -->
  <script src="bundle.js" />
</head>
<body>
  <div>
    <Expandable key={1}>
      <p>this is the first note</p>
    </Expandable>
    <Expandable key={2}>
      <p>this is the second note</p>
    </Expandable>
    <!--...-->
  </div> 
</body>
```

## ✅ Async components with Server Components 

Server Components introduce a new way to write Components using async/await. When you await in an async component, React will suspend and wait for the promise to resolve before resuming rendering. This works across server/client boundaries with streaming support for Suspense.

You can even create a promise on the server, and await it on the client:

```js
// Server Component
import db from './database';

async function Page({id}) {
  // Will suspend the Server Component.
  const note = await db.notes.get(id);
  
  // NOTE: not awaited, will start here and await on the client. 
  const commentsPromise = db.comments.get(note.id);
  return (
    <div>
      {note}
      <Suspense fallback={<p>Loading Comments...</p>}>
        <Comments commentsPromise={commentsPromise} />
      </Suspense>
    </div>
  );
}
// Client Component
"use client";
import {use} from 'react';

function Comments({commentsPromise}) {
  // NOTE: this will resume the promise from the server.
  // It will suspend until the data is available.
  const comments = use(commentsPromise);
  return comments.map(commment => <p>{comment}</p>);
}
```

The note content is important data for the page to render, so we await it on the server. The comments are below the fold and lower-priority, so we start the promise on the server, and wait for it on the client with the use API. This will Suspend on the client, without blocking the note content from rendering.

Since async components are not supported on the client, we await the promise with use.

---

### ▶️ React Server Components + Suspense Workflow

### ✨ 🔹 **Traditional CSR Workflow**
**Workflow:**  
1️⃣ The browser loads `index.html` from a static server.  
2️⃣ React **downloads JavaScript** and initializes the app in the client.  
3️⃣ The client makes **API calls** to fetch data.  
4️⃣ React renders everything **on the client**, updating state dynamically.  

**Downside:** The client has to **download a large bundle**, make extra API calls, and do more processing.

### 🔹 **RSC Workflow**

#### 1️⃣ **Client Requests a Page**

Instead of fetching a static HTML file, the client sends a request to a **React-aware server** (e.g., using **Next.js**, running on Node.js or an edge runtime).

This server understands how to process **Server Components** and deliver an **RSC payload**.

#### 2️⃣ **Server Processes Server Components**

The server evaluates **React Server Components**:

* Executes **data fetching**, **logic**, and **component rendering** entirely on the server
* **No Server Component logic or data fetching is sent to the client**

The result is an **RSC payload**—a structured stream of data representing the server-rendered React tree.

#### 3️⃣ **Server Streams the RSC Payload to the Client**

The **RSC payload** is streamed to the client:

* It may include some **HTML** (e.g. for layout, static text)
* It contains **serialized metadata and component instructions** for React to reconstruct the UI
* **Suspense boundaries** allow parts of the UI to progressively load

This approach improves perceived performance by rendering and displaying parts of the UI as soon as they’re ready.

#### 4️⃣ **Client Hydrates and Enhances the UI**

On the client:

* **React reconstructs the UI** from the streamed RSC payload
* **Client Components** (marked with `"use client"`) are hydrated — making them interactive
* These handle **state, user interactions, animations**, etc.

**Server Components remain static** on the client — they are never hydrated or re-executed in the browser.

---

### ▶️ For each client request, does the server run the Server Components and generate a dynamic bundle?

✨ Yes, exactly! Every time the client makes a request, the server **processes the React Server Components (RSC)** dynamically, rendering the necessary UI **before** sending it back to the client. Instead of delivering a **pre-built JavaScript bundle**, the server **generates UI content on demand**, based on the request.

### 🔹 How It Works:
1️⃣ **Client Requests a Page** → The browser sends a request to the React server.  
2️⃣ **Server Executes RSC Logic** → React runs **server components**, fetching data, executing logic, and assembling the UI.  
3️⃣ **Server Sends Rendered Output** → Instead of a full JavaScript bundle, React sends **pre-rendered HTML & lightweight JavaScript**.  
4️⃣ **Client Hydrates the UI** → The browser integrates interactive client-side components where needed.  

### 🔹 Why This Is Different from Traditional Client-Side React:
✅ **Less JavaScript sent** → The server does more work, reducing client-side computation.  
✅ **No full re-fetching of JavaScript** → The server **only regenerates the parts that change**, making updates faster.  
✅ **No unnecessary client-side API calls** → The server **handles data fetching**, so the client doesn’t need extra requests.  

### 🔹 Does This Mean No More Bundles?
Not entirely! Client-side components **still require JavaScript**, but React **splits the workload**:
- **Server Components** (processed **on the server**, never sent as JavaScript).  
- **Client Components** (interactive elements **hydrated** after receiving server-rendered content). 

---

# ✨ AI Research

In earlier experiments, React Server Components (RSC) allowed components to be async, directly fetching data and returning JSX. This approach made it possible to handle data-fetching within the component itself, reducing client-side complexity.

However, with the latest changes, there is a stronger distinction between server components and client components:
- Server Components: These still handle data-fetching but must return serializable data (not JSX that contains client-side interactivity).

- Client Components: If a component needs interactivity (event handlers, hooks like useState, etc.), it must include "use client" and be imported into the server component.

This shift reinforces React’s architectural intent—keeping the server responsible for rendering static content while ensuring dynamic UI logic stays in client components.

React Server Components (RSC) can still return JSX, but only serializable JSX—meaning it must be pure markup and cannot include client-side interactivity (like event handlers or hooks).

How it works:
- Server Components can render JSX but cannot use hooks (useState, useEffect, etc.) or handle client-side events.
- If a component needs interactivity (buttons, inputs, animations), it must be a Client Component (marked with "use client").
- A Server Component can import Client Components when needed, but it doesn’t always have to. If it’s purely static content, it can render JSX on its own.

Key takeaway: Server Components can still return JSX, but if interactivity is required, you’ll need to import a Client Component.

---

▶️ So, if we fetch albums in a Server Component and iterate over them in a Client Component—returning only the titles—the response sent to the client will contain only the rendered strings, not the full album objects. In that sense, RSC is somewhat similar to GraphQL in its ability to send only the requested data.

✨ Yes, that’s a great observation! React Server Components (RSC) do resemble GraphQL in the way they send only the necessary data to the client.

How RSC Sends Minimal Data:
- If a Server Component fetches a list of albums and passes only the titles to a Client Component, then the response to the client will only contain those titles—not the full album objects.

- This is because RSC serializes the output and only sends what’s actually needed for rendering, rather than exposing the entire dataset to the client.

- Compared to traditional React, where fetched objects are often passed to the client as full JavaScript structures, RSC ensures the client gets only the rendered data—making responses lighter and more efficient.

How This Compares to GraphQL
- GraphQL lets clients request only the specific fields they need, preventing over-fetching.

- RSC naturally does something similar—not by querying the database selectively, but by controlling what gets sent to the client after rendering.

While RSC isn't a direct replacement for GraphQL, it does follow a similar philosophy—only sending the essential pieces of data instead of the entire object.

---

You can't import server component into client component

▶️ There was something that you could pass a server component as a child to client component?

✨ Yes, in React Server Components (RSC), you can pass a Server Component as a child to a Client Component—but not by importing it directly.

How does this work?
Since Server Components return serialized data, they can be rendered inside a Client Component as long as they are passed as JSX from a parent (which is also a Server Component).

The key is that the parent component (on the server) renders both the Server Component and the Client Component together.

---

▶️ Top-down rendering vs nesting in RSC

✨ In React Server Components (RSC), top-down rendering and nesting describe different ways components interact with data-fetching and rendering.

Top-down rendering – This approach means that data is fetched at the top level (like a parent component) and then passed down through props to child components. It keeps components lightweight because children don’t handle their own data-fetching; they simply receive the data they need. This can improve efficiency and reduce unnecessary API calls.

Nesting – Nesting occurs when child components independently fetch their own data, rather than relying on a parent to pass it down. This is useful when components need to function autonomously or when different parts of the UI require separate data sources. However, it can lead to multiple separate API requests and may result in slower performance.

In RSC, top-down rendering is often favored for better control and efficiency, especially when fetching data directly from a server. But nesting has its place when components need to be flexible and independent.

---

▶️ Do you still need a list "key" on the server?

✨ In React, the key attribute is essential for efficiently managing lists of components. It helps React identify which items have changed, been added, or removed, making updates faster and avoiding unnecessary re-renders.

Here’s why key is important:
- Efficient Reconciliation – React uses keys to track components in lists. When an item changes, React only updates the affected elements instead of re-rendering the entire list.
- Maintains Component State – If a list is shuffled or items are removed, React uses keys to preserve the state of each item correctly.
- Prevents Bugs – Without keys, React relies on indexes, which can cause unexpected behavior when items are dynamically added or removed.

In React Server Components (RSC), the role of the key attribute differs slightly compared to traditional client-side rendering.
While React uses key for efficient reconciliation in the browser (to track which list items change, are added, or removed), in RSC, components are fully re-rendered on the server before being sent to the client. This means React doesn’t need key for diffing updates the same way it does with client-side rendering.

However, you still need keys when:
- Rendering lists dynamically – Even though RSC re-renders on the server, keys help maintain consistency when components interact with other UI elements.
- Avoiding unnecessary updates – If a part of the UI hydrates and transitions into a client component, keys ensure proper updates in that transition.

Keys are less crucial in pure RSC, but they remain important for maintaining correct UI behavior, especially when client-side hydration is involved.

---

▶️ Is "boundary" a new term in the RSC workflow?

✨ Yes! In the context of **React Server Components (RSC)**, the term **"boundary"** refers to the distinction between **client components** and **server components**—essentially defining **where the client-server responsibilities are divided** in a React app.

### **What Does "Client/Server Boundary" Mean in RSC?**
- React Server Components allow some parts of the app to run **entirely on the server**, while others remain **interactive on the client**.
- This **boundary** is determined at the **module level**, meaning some components are explicitly marked as **server components** while others are **client components**.
- Directives like `"use client"` (for client components) define **which part of the React tree runs in the browser** versus on the **server**.

### **Why Is This Important?**
- In traditional React apps, the **entire UI runs on the client**.
- With RSC, developers can push data-heavy computations to the **server**, reducing the need for extra API calls.
- The **"boundary"** helps separate logic that benefits from **server-side execution** (database queries, processing, etc.) from **interactive UI components** that must run in the browser.

### **Example of the Boundary in Action**
```jsx
// This runs on the server (no client-side JavaScript)
export default async function ProductList() {
  const products = await fetchProducts();
  return <ul>{products.map(p => <li key={p.id}>{p.name}</li>)}</ul>;
}

// This runs on the client because it includes interactivity
"use client";
import { useState } from "react";

export default function ProductSearch() {
  const [query, setQuery] = useState("");
  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```
Here, **ProductList** is a **server component**, while **ProductSearch** is a **client component**—this defines the **boundary** between them.

So, **yes**, the term "boundary" is **relevant to the RSC workflow** and describes how React manages execution between the **server-side logic** and **client-side interactivity**.

---
## Syntax Podcast #718: React Server Components
---

You're not running this on the client exclusively anymore.

A server component is a component, a react component that JS, here we go, rendered on the server, meaning that it is not rendered on the client.
All of the data fetching, all of the looping, templating, everything that needs to happen happens on the server, and React simply sends HTML from the server to the client and it's just rendered. 
Why would you ever want to do that? First of all, server components are asynchronous, meaning we've never had asynchronous components in React before. And what that means is you're able to simply await a fetch before you return your JSX.

Benefits:
- You don't need a hook that is initially undefined and then when the data fetches, it updates and it re renders the component.

- There's no Loading state because You're simply just waiting for the thing to be fetched before you go ahead

- There's no API endpoints needed in a lot of cases because it's rendered on the server. You can just query your database directly in a function so you don't need to make a REST API. You don't need a GraphQL API.

-You can import all your server side packages right into you your React component (and not to cause big bundle)

- You can put your database logic in a separate file and import that function into it. And you can mark it as "use server" function. It will only ever run it on the server. So you don't have to worry about like it's not running on the client, obviously.

Why would you want to run something server side? Large dependencies do not need to be loaded on the client. If you need a big package to do something on the client, you don't need to load in a meg of JavaScript. You can just use that server side where it's already loaded and simply just send the 8 divs as a result to the actual server. So it's going to be much smaller. You You don't have to find a client friendly version of something and make sure it works in all the browsers.

So client components are components that have state, components that have custom use effects, components that have event handlers onClick, onSubmit, all of that good stuff. And the weird thing about this is that client components are still server rendered initially. However, they are rehydrated on the client, much like you would expect on a traditional like rehydrated server side rendered and then rehydrate on the browser approach. 
Like that's what we've been doing a long time. The big difference here is that Not everything is a client component like it previously was.
And in fact, you have to sort of opt in to client components in React when you do need to do the things that we just mentioned. When you do need custom state, effects, event and event handlers and anything else that like you're accessing the window object or something like that. That's when you would reach for a client component. Initially, I was like, you can't put state in a server component?! You don't need custom state on the server side because you simply just await it. It's asynchronous. So you simply just await the fetch request and then you you have the thing and that that state never updates because It's server rendered.

The first point here is that components are server rendered by default. So when you are building a React application, by default, everything is server rendered. And then you opt in to client side components when you need interactivity and updating state.

React Islands is another example that is very popular where you have an existing monolith application and then part of your application It's built in React because that is the piece that needs to be interactive, right, or JS Sprinkles.

✨ React Islands refers to a technique used in web development where you integrate React components into a larger monolithic application—typically one built with traditional server-side rendering or another framework. These React components, or "islands," handle interactive elements of the application, providing dynamic functionality where needed.
The term "JS Sprinkles" is often used to describe adding small amounts of JavaScript to enhance a mostly static page. In the case of React Islands, this means embedding React into specific parts of an existing app to improve interactivity without rewriting the whole application in React.
This approach allows developers to modernize parts of their applications incrementally, rather than transitioning entirely to a new framework. 

How server components work:

The server will render the component and send the React code over the wire.
And you can essentially think of that as The React server will render it's not HTML, but if you think about it as HTML. You can think of React as just sending the div HTML, and then the React on the client side will say, oh, I got some I got some HTML from the server. Let me just put that into that part of the website where it belongs. And what it is actually sending is a React component with references and all of the data in it.

Streams structured HTML chunks to client.

You can think of your page as a server component.
And then as you get into smaller pieces of your website, those are all server components until you get to one where you say, oh, that needs interactivity. This has buttons. This has drag and drop. This has a map in it. So server components can have client components. However, client components cannot have server components. Client components and server components are separate things. Client components can have server components passed in JS children or as props. You can have a client component and then wrap that client component around another server component or pass it in via props.
To mark something as a client component, you write "use client" at the top of the file, and that makes that file a client. You cannot put a client component and a server component in the same file.

About Forms: You've always had to reach for some sort of third party library or roll your own custom state hook and never been a big fan of that. So part of server components, they have rolled out this thing called server actions and form actions. 

So Form actions are functions that run when your form is submitted and you pass a function to the action prop on a form. So In HTML, you have an action that is a string, and that string is a URL of where the form is submitted to. In React the action property is reference to a function that will run when somebody submits that form. So and then that function itself will give you the entire form data object.

Now those form actions run on the client by default, I said. However, if you mark them with a "use server" in the function, now that function will only be run on the server.

But the initial reaction to this JS, so you're telling me Components are server by default and client you have to opt in to client side with use client. However, Form actions are client by default, and you have to opt in to server when you want them to run on the server.

If you mark it with a "use server", it will then submit the form data to the server. It doesn't do a page reload or anything. It simply just submits the data to the server. And then you can then return data from the server, or you can return an entire component that needs to be re rendered on the page.

I like this a lot where you can simply take a client side variable. Call the the function from the client and that variable will be available to you on the server side, which is just like not mind blowing because we certainly have had things like that. But that like seamless transition of passing a variable from the client side to the server side without having to do any API, no REST API, no JSON stringify.

---
Note:
```js
"use server"; // Marks this function as a server action

async function submitForm(formData) {
  const username = formData.get("username");
  console.log("Processing on server:", username);
  
  // Example: Store data in a database (hypothetical function)
  await saveToDatabase({ username });

  return { message: "Form submitted successfully!" };
}

export default function MyForm() {
  return (
    <form action={submitForm}>
      <input type="text" name="username" placeholder="Enter your name" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

How This Works:
The submitForm function is marked with "use server", meaning it runs on the server.

When the form is submitted, React passes the form data to submitForm, handling it without needing a separate API endpoint.

This approach reduces client-side JavaScript, improves security, and allows direct database interaction.

Server functions are the conventional way of handling backend logic—they run on the server and are typically accessed via API routes, meaning the client (browser) makes a request to the server, and the server responds with data.

Server Actions, on the other hand, are a newer React feature that allows components to execute server-side logic directly without requiring separate API calls. Instead of calling an external endpoint, the form or UI element directly triggers a function that runs on the server, streamlining data processing and reducing client-side JavaScript overhead.

Terms:
Server Actions → Functions that run directly on the server when triggered, without needing an API endpoint. Used for form submissions, database interactions, etc.

Client Actions → Functions executed in the browser, typically for UI interactivity or client-side validation.

Form Actions → Now integrated with Server Actions in React, where you pass a server function to the action prop on a ```<form>``` element.

So in modern React, Form Actions are essentially powered by Server Actions, eliminating unnecessary API calls while keeping form submissions efficient.

---

Use form hooks for status, errors and optimistic updates

Suspense built-in to handle asynchronous rendering.
So you can take a react suspense, which is a component, and you wrap it.
You wrap components that may take a while to render.
So in an example that I did, I intentionally made a component that takes 4 seconds to render. And if you have a single component on your page that takes 4 seconds to render, guess what is also taking 4 seconds to show up?  The entire page. So one little component could make your whole page extremely slow. It's kind of promised that all the entire thing. So what react suspense will do is you can wrap your possibly slow components or you can wrap anything that fetches data in a suspense component. And what will happen is the server will immediately send a loader component. You specify what shows up when you are loading and you're sort of in a suspended state and you can show a spinner. You can show nothing and wait for 2 seconds and then show a spinner. You can show screen, that's pretty common in applications where it's just gray boxes where the content should show up. 
And then the server will continue working on rendering that component so not to hold up the rest of the actual website. And then the server will stream them from the server into the client when it actually is rendered.

---
## Syntax Podcast #895: React Server Components: Where are we at?
---

Topics:
- Status of React server components - not fully caught on yet
- React server components rendered on server, client components rendered on client
- Usually use server and client components together
- Server components can await data before rendering
- Server components send output to browser similar to server rendered HTML
- Not rehydrated, just sends HTML or React payload to client
- Next.js and Waku have good RSC support currently
- Building RSC support is hard on bundlers
- RSC not standards based, relies heavily on bundlers
- RSC not supported the same in all React frameworks

Server components in React are components that are rendered either as as part of a build step, which is, like, ahead of time. Like, server components don't require a server, which is kinda funny. But in in most cases, you're probably going to be rendered on demand entirely on the server. That differs from client components, which client components are often rendered, obviously, in the client, but they can also still be be server rendered. The difference between the two is that server components simply just send their output to to the browser similar to how server rendering HTML works, versus client components. They bring everything they need in order to render themselves on the client, which it's not really one or the other. And in most cases, you you'll be using both of them, together.
Server components are async, meaning that you can simply just await before you return data, which is amazing API, for many use cases. So you don't have to do, like, a weird loading state. And if it's loading, then return nothing. You can simply just await, like, a database call. And then right once that await is resolved, you can go ahead and render out your markup, your JSX. Server components, they can also be suspended.
So, basically, you can continue rendering the rest of your app while these things are working on their render, so it's not like it's going to hold up if you are, awaiting them. Modern React is often streamed, meaning that the browser will start to render pieces, of the website. And then if you have something that might take a a little while, for example, on my new website, there's a Twitter in the footer. And I don't wanna hold up the entire page while I'm fetching Twitter. What if that takes takes a while? So you can suspend that, and then the rest of the website will get sent. And then when that one component, is done, it's no longer suspended. And then it will it will send the markup from the server to the client and render it on out.

Server components send output to browser similar to server rendered HTML.

Server components, basically, the way that is it sends RSC Wire Format, a React server component payload to browser, it's basically sending text. Not rehydrated, just sends HTML or React payload to client. Because rehydrated assumes that things are bound to it. Rehydrated means that the browsers is then set setting it up. So, server components are simply just sending either their HTML to the browser, or if you are doing something refreshing one of the components, it is simply just sending the outputted, React payload. You can think of it as a server sending HTML. It's not technically HTML. It's technically React code that is used to embed it, but it's not fair to say that it's rehydrated because there's no there's no client side stuff happening on a server function unless you're explicitly using a client component.

Server functions, which are were previously called, server actions, are functions that run on the server and sort of work with RSC. So this could be like a database call, could be, like I said, markdown code highlighting. Server functions are functions that can be called either from the client or on a form submission, meaning that you essentially get, RPC, meaning that you can write a function that runs on the server, and then you can simply just call that from your UI. And instead of it importing that server function into your, client side application, it will just run the RPC back and forth commands between the two and send the data from one to another.

And then there are several hooks you can use for maintaining useActionState() is the big one for maintaining the return what comes back from the response, any pending, like, if something is waiting, clearing caches, all that stuff.

Michael Jackson, who's one of the creators of of React Router, he put out a tweet that says, React server components are nice in theory, but five years in, it's not working out.

The moveBefore() API is a new JavaScript feature that allows developers to move elements within the DOM without losing their state, bindings, or event listeners. Traditionally, moving an element meant removing and reinserting it, which caused animations, focus states, and other properties to reset.

With moveBefore(), you can:

- Move elements without resetting their state.
- Preserve event listeners, animations, and focus.
- Improve performance in dynamic UI updates.

This API is experimental and currently available in Chrome 133.

```js
// Select the parent container
const parent = document.getElementById("container");

// Select the element to move
const elementToMove = document.getElementById("movingElement");

// Select the reference element (where to move before)
const referenceElement = document.getElementById("targetElement");

// Move the element before the reference element
parent.moveBefore(elementToMove, referenceElement);
```

React's Activity component.

Vite 6 has this new API called the environment API. And the whole idea with the environment API is that you can run Vite in multiple environments. Vite was simply a browser based bundling tool. And now with the environment API, it can it can work in Node. It can work in on CloudFlare workers. It can run-in on the browser. And that then opens up a lot more for framework authors because now you can control the whole stack of, like, both my server and my client all are running through Vite, instead of having to do some weird, like, bundling.

TanStack, a lot of people use TanStack Query. You can use RSC via Next.JS with TanStack Query currently. There is something called TanStack Start, which is their whole platform. TanStack Start itself does not, support RSC just yet. It does do SSR. I know we talked to Tanner, and there's an emphasis on, like, client side stuff within TanStack Start, but you can do SSR with it. Start does have RSC planned.

And so RSC, a React core feature is only available in Next.JS and Waku and, you know, these kind of fragmented ways. So because of that, it is, like, fracturing the React landscape completely. There's no parity. You're not using React as a platform. You're using, parts of React at any given point.

---

## RSC: A New Way to Build Fast and Interactive Web Apps w/ Aurora Scharff

[Talk at NDC Conference](https://www.youtube.com/watch?v=UBc0IbJUzKA&ab_channel=NDCConferences)

## Rendering Strategies

### 1. Static website rendering

- Pages are pre-assembled and uploaded to a static host
- Simple and efficient
- Struggles with dynamic data and impractical to maintain

### 2. Multi-Page Applications

- HTML and data are dynamically merged on the server
- Serves the merged result on page load
- Page reloads per request

Pros:

- Optimized for SEO
- Unlimited scalability
- More website insights i.e. google analytics

Cons:

- Slower overall performance
- More development time
- Hard to maintain

### 3. Single-Page Applications

Pros:

- Instantaneous, app-like feel
- Easy to debug
- Caching capabilities

Cons:

- Slow initial load
- SEO suffers
- Needs JavaScript
- Favors modern browsers
- Less secure

### 4. Server-Side Rendering (SSR)

- Pre-render the initial HTML on the server
- Fully-formed HTML when opening the page
- JavaScript is fetched alongside it

- Included feature in JavaScript "meta-frameworks" i.e. NextJS, Remix, SvelteKit, NuxtJS
- Build time (SSG) and/or request time (SSR)
- Pages generated in the build can be cached

### 5. Static Site Generation (SSG)

- Pages are pre-assembled and uploaded to a static host
- Requires site redeployment whenever data changes
- JavaScript is fetched alongside it

### SSR Workflow

1. User requests a site
2. Server creates ready HTML files
3. Browser renders HTML but it's not interactive
4. Browser downloads JavaScript
5. Browser executes JavaScript
6. Website is fully interactive

The problem with SSR: When users see the website as fully interactive, but it actually is not, the time they spend waiting for the app to become fully interactive could be even longer than in the SPA version of the app.

### Why does this happen

1. You have to fetch everything before you can show anything
2. You have to load everything before you can hydrate anything
3. You have to hydrate everything before you can interact with anything

### Strategies to improve it

- Partial Hydration: only attach JavaScript to certain components
- Progressive Hydration: lazy load JavaScript to certain components
- Selective Hydration: priority load JavaScript to certain components based on user interactivity

### Streaming

- Available from React 18 and big part of RSC performance
- Split a request into chunks
- Send each chunk as a stream to the client
- Render each chunk as it is streamed in

### React Server Components

They run exclusively on the server and then streamed to the client.
They never re-render just run once on the server to generate the UI.
Their JavaScript is never send to the client.
The server is part of the full-stack framework i.e. NextJS.
The components can run during the build process of the app as an alternative to the server.
The server components eliminate the need to write API.

RSC vs SSR: server components are never hydrated meaning the resulting app is partially hydrated.

Since RSC are exclusively run on the server we can write DB queries directly in the component.
We can't use "useState()", "useEffect()", browser specific APIs (window.location) or JavaScript handlers in them.

When we want that we use client components and they are marked with "use client" directive.

React renders RSC into a special format called RSC Payload. It is streamable format that represents the DOM,
like serialized version of the React tree. The RSC Payload contains the rendered server components with "holes" of
references to client components and the props they passed to them. It's used both to generate the pre-rendered HTML
on the server and to update the DOM on the client without a need for JavaScript.

When RSC needs to be re-rendered due to a data change it refreshes on the server and seamlessly merges into
the existing DOM without a hard refresh, updating only the parts that have changed. As a result the client state
is preserved even as parts of the view are updated from the server. This is possible because the RSC Payload only
contains references to the client components and can leave them untouched.

### Benefits of using RSC:

- Faster data fetching and access to backend resources, before they RSC is send to the client i.e. ahead of time.
- Security: all sensitive logic and API keys are kept on the server witout risk to expose them
- Caching: by rendering on the server the result can be cached and reused on subsequent request
- Bundle size and performance: large dependencies could be kept on the server and to be excluded from the client side bundle
- Streaming and Suspence integration: server components can be streamed meaning they can be sent in chunks and viewed as they
become ready
- Developer experience: a lot less hassle on the client with effects and keeping data in the state

### Drawbacks of RSC:

- Require a framework i.e. NextJS
- Increased complexity with the new concepts
- Learning curve
- Early adoption

Since the client components exists on the client and server components on the server, props need to be serializable.
That means that we can't pass functions through the server boundary. Instead we must create API endpoints or better - server actions to communicate between them when we can't use props.

If we use the same data in multiple components in a tree, we don't have to fetch it on the root and pass it down.
We can fetch it in each component because it is automatically memoized.

### Server Actions are the preferred way to mutate data.

- Define a function/file with "use server"
- NextJS creates "hidden" API endpoint
- Use the function in any component
- Provides a type-safe RPC-experience

Summary: RSC in app router allow you to do whatever you need to do whether is MPA or SPA and include SSR or SEO all in the same stack optimized.