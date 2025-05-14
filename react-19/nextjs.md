# [```⬅️ Go Back```](./react-server-components.md#resource-8-nextjs-docs)

### ⚠️ Disclaimer: These notes highlight only React 19-related features, other features and versions are omitted.

## [**NextJS Versions**](./nextjs-versions.md)

## Docs
>### [🔗 Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)

- When to use Server and Client Components?
- Passing data from Server to Client Components
- Interleaving (mixing two or more things in an alternating or intertwined way) Server and Client Components
- Context providers
- Third-party components
- Preventing environment poisoning i.e.  use the ```server-only``` package

---

>### [🔗 Fetching data](https://nextjs.org/docs/app/getting-started/fetching-data)

### Server Components
- The ```fetch``` API
- An ORM or database

### Client Components
- React's ```use``` hook
- A community library like SWR or React Query

### Streaming
- Wrapping a page with a ```loading.js``` file (to stream the entire page while the data is being fetched)
- Wrapping a component with ```<Suspense>``` (allows you to be more granular about what parts of the page to stream)

---	

>### [🔗 Updating data](https://nextjs.org/docs/app/getting-started/updating-data)

### Server Functions

A Server Function is an asynchronous function that is executed on the server. Server Functions are inherently asynchronous because they are invoked by the client using a network request. 
### **```When invoked as part of an action, they are also called Server Actions.```**

By convention, an action is an asynchronous function passed to startTransition. Server Functions are automatically wrapped with startTransition when:

- Passed to a ```<form>``` using the action prop,
- Passed to a ```<button>``` using the ```formAction``` prop
- Passed to ```useActionState```

### Creating Server Functions

A Server Function can be defined by using the ```use server``` directive. You can place the directive at the top of an asynchronous function to mark the function as a Server Function, or at the top of a separate file to mark all exports of that file.

### Invoking Server Functions

There are two main ways you can invoke a Server Function:
- Forms in Server and Client Components
- Event Handlers in Client Components

### **```Good to know: When passed to the action prop, Server Functions are also known as Server Actions.```**

Examples: 
- Showing a pending state  
```import { useActionState } from 'react'```
- Revalidating the cache  
```import { revalidatePath } from 'next/cache'```
- Redirecting  
```import { redirect } from 'next/navigation'```