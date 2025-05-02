# [```⬅️ Go Back```](./index.md#-origin)

#### ```Dan Abramov explores React Server Components with us! w/ Ben Holmes                  ```

Sebastian Markbage started to work on RSC since 2016.
They had document "What comes after GraphQL?".
The architecure that they had for web apps was using GraphQL and Relay.
It worked, but it pushed more and more code to the client bundle.
The idea was to not download code and data that we don't need and
if we need pre-process for the data, let's do it on the server.
We are going to the server anyway, why not do it there.

------------------------------------------------------------------------
## XHP

# ✨ AI Research

**PHP/Hack** is a programming language developed by **Meta (formerly Facebook)** as a **dialect of PHP**, introduced in **2014**. It runs on the **HipHop Virtual Machine (HHVM)** and combines **static typing** with PHP’s flexibility, allowing developers to write **safer, more efficient, and maintainable code** while maintaining compatibility with most PHP codebases.

Before Hack, Facebook developed **XHP** around **2008-2010** as an extension to PHP, allowing developers to write **XML-like syntax directly inside PHP code**. XHP improved safety and readability by making it easier to create reusable UI components and helping to prevent common security issues like XSS (cross-site scripting). XHP was initially used in **Facebook Lite** as a new server-side UI rendering layer before being integrated into Facebook’s main web stack.

XHP’s design and concept later influenced **JSX**, the XML-like syntax used in **React**, which Facebook open-sourced in **2013** to enable rich, interactive client-side web applications.

While **React/XHP focus on UI rendering** (client-side and server-side respectively), **Hack targets backend development**, modernizing PHP by adding static typing, improved tooling, and performance enhancements on the server side.

### Summary of Relationships and Timeline:

| Tech  | Release     | Purpose                              | Notes                              |
| ----- | ----------- | ------------------------------------ | ---------------------------------- |
| XHP   | \~2008-2010 | Safer, XML-like syntax in PHP for UI | Pre-Hack, inspired JSX             |
| React | 2013        | Client-side UI library with JSX      | Open-sourced by Facebook           |
| Hack  | 2014        | Typed dialect of PHP for backend     | Runs on HHVM, improves PHP backend |

Dan Abramov has talked about XHP, a technology used at Facebook that influenced modern component-based development. 
In a tweet, he described XHP as being similar to JSX + React's component model, but for PHP/Hack.
He explained that, unlike traditional MVC architectures, Facebook's server-side code used components 
that could handle data fetching and rendering, much like React components. 
Interestingly, XHP was a precursor to JSX, allowing developers to write statically-checked XML-like syntax within PHP.

------------------------------------------------------------------------

### ▶️ What was BigPipe for Facebook, and could it have influenced ReactJS in some way?

✨ BigPipe was a high-performance web page rendering system developed by Facebook to improve page load times. 
It worked by breaking web pages into smaller chunks called "pagelets" and loading them asynchronously, 
rather than waiting for the entire page to be ready before displaying content. 
This technique significantly improved user experience by allowing visible parts of a page to load faster.

Facebook introduced BigPipe in 2009, and it began using it internally around 2007–2008 during efforts to improve page load speed and perceived performance.

Did BigPipe Influence React?
Yes, in some ways! While BigPipe focused on server-side rendering and progressive loading, 
React introduced client-side component-based rendering. However, both share the idea of incremental rendering, 
meaning that parts of a UI can be updated independently rather than requiring a full refresh.

BigPipe’s approach to breaking down UI into smaller, independently loaded units aligns with React’s component-based architecture, 
where each component manages its own state and rendering. 
This concept helped shape modern React Server Components and streaming rendering techniques.

---

### ▶️ So could we say that BigPipe = RSC and XHP = ReactJS?

✨ That’s an interesting way to look at it! While BigPipe and React Server Components (RSC) share some similarities, 
and XHP and ReactJS both deal with component-based UI rendering, they aren’t exactly one-to-one equivalents.

BigPipe = RSC in concept: BigPipe focused on progressively streaming UI from the server, 
loading smaller chunks (pagelets) independently. React Server Components also leverage progressive streaming, 
allowing parts of a React app to load asynchronously from the server—so in a way, BigPipe’s ideas contributed 
to modern streaming techniques in React.

XHP = ReactJS in structure: XHP introduced a component-based model for PHP, 
where UI elements were objects with encapsulated logic, similar to how React components work. 
XHP’s XML-like syntax influenced JSX, but React went beyond just markup—it introduced reconciliation, 
hooks, and a declarative rendering paradigm.

So, while we can see clear inspirations between them, ReactJS evolved far beyond XHP, 
and RSC applies BigPipe-like ideas in a more flexible, modern way.

---

### ▶️ Could we also add the Suspense component to the analogy with BigPipe?

✨ Absolutely! React's Suspense component can also fit into the BigPipe analogy, 
since both help optimize progressive rendering and loading delays.

BigPipe: Breaks pages into smaller independent "pagelets" that load asynchronously, speeding up perceived performance.

React Suspense: Suspends rendering while waiting for asynchronous data, displaying placeholders (like loading spinners) until the data is ready.

Both approaches focus on incremental rendering, making sure users see content faster instead of waiting for everything to load at once. 

So we could say:  
BigPipe = React Server Components + Suspense  
XHP = ReactJS + JSX

React’s concurrent rendering features take this even further, giving fine-grained control over what renders when.
