# [```⬅️ Go Back```](./nextjs.md)

## Next.js 15.2 (2025) 

- React View Transitions (experimental): Experimental support for React's new View Transitions API

## Next.js 15.1 (2024)

- React 19 (stable): Support for React 19 is officially available in both Pages Router & App Router.

## Next.js 15 (2024)

- React 19 Support: Support for React 19, React Compiler (Experimental), and hydration error improvements.

## Next.js 14 (2023)

Server Actions (Stable): Progressively enhanced mutations
- Integrated with caching & revalidating
- Simple function calls, or works natively with forms

What if you didn't need to manually create an API Route? Instead, you could define a function that runs securely on the server, called directly from your React components.

The previous example from the Pages Router can be simplified to one file:

```jsx
export default function Page() {
  async function create(formData: FormData) {
    'use server';
    const id = await createItem(formData);
  }
 
  return (
    <form action={create}>
      <input type="text" name="name" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## Next.js 13.4 (2023)

App Router (Stable):
- React Server Components
- Nested Routes & Layouts
- Simplified Data Fetching
- Streaming & Suspense
- Built-in SEO Support
- Server Actions (Alpha): Mutate data on the server with zero client JavaScript. This enables you to mutate data on the server, calling functions directly without needing to create an in-between API layer.
With Server Actions, you have powerful server-first data mutations, less client-side JavaScript, and progressively enhanced forms.

```jsx
import kv from './kv';
 
export default function Page({ params }) {
  async function increment() {
    'use server';
    await kv.incr(`post:id:${params.id}`);
  }
 
  return (
    <form action={increment}>
      <button type="submit">Like</button>
    </form>
  );
}
```

## Next.js 13 (2022)

### New app Directory (Beta)

Today, we're improving the routing and layouts experience in Next.js and aligning with the future of React with the introduction of the app directory.

The app directory includes support for:

- Layouts: Easily share UI between routes while preserving state and avoiding expensive re-renders.
- Server Components: Making server-first the default for the most dynamic applications.
- Streaming: Display instant loading states and stream in units of UI as they are rendered.
- Support for Data Fetching: async Server Components and extended fetch API enables component-level fetching.

### Layouts

The ```app/``` directory makes it easy to lay out complex interfaces that maintain state across navigations, avoid expensive re-renders, and enable advanced routing patterns. Further, you can nest layouts, and colocate application code with your routes, like components, tests, and styles.
Layouts share UI between multiple pages. On navigation, layouts preserve state, remain interactive, and do not re-render.

### Server Components

The ```app/``` directory introduces support for React's new Server Components architecture.

When a route is loaded, the Next.js and React runtime will be loaded, which is cacheable and predictable in size. This runtime does not increase in size as your application grows. Further, the runtime is asynchronously loaded, enabling your HTML from the server to be progressively enhanced on the client.

### Streaming

The ```app/``` directory introduces the ability to progressively render and incrementally stream rendered units of the UI to the client.

With Server Components and nested layouts in Next.js, you're able instantly render parts of the page that do not specifically require data, and show a loading state for parts of the page that are fetching data. With this approach, the user does not have to wait for the entire page to load before they can start interacting with it.

### Data Fetching

- React's recent Support for Promises RFC introduces a powerful new way to fetch data and handle promises inside components.

- The native ```fetch``` Web API has also been extended in React and Next.js. It automatically dedupes fetch requests and provides one flexible way to fetch, cache, and revalidate data at the component level. This means all the benefits of Static Site Generation (SSG), Server-Side Rendering (SSR), and Incremental Static Regeneration (ISR) are now available through one API:

```js
// This request should be cached until manually invalidated.
// Similar to `getStaticProps`.
// `force-cache` is the default and can be omitted.
fetch(URL, { cache: 'force-cache' });
 
// This request should be refetched on every request.
// Similar to `getServerSideProps`.
fetch(URL, { cache: 'no-store' });
 
// This request should be cached with a lifetime of 10 seconds.
// Similar to `getStaticProps` with the `revalidate` option.
fetch(URL, { next: { revalidate: 10 } });
```

In the app directory, you can fetch data inside layouts, pages, and components – including support for streaming responses from the server.

With the app/ directory, you can use a new special file loading.js to automatically create Instant Loading UI with Suspense boundaries.