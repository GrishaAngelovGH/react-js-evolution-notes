# [```⬅️ Go Back```](./features.md)

---

### Resource 1: [🔗 Making Sense of React Server Components w/ Josh Comeau](https://www.joshwcomeau.com/react/server-components/)

### Resource 2: [React for Two Computers /w Dan Abramov](#react-for-two-computers-w-dan-abramov)

### Resource 3: [From the blog](#from-the-blog)

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