# [```⬅️ Go Back```](./features.md)


# Actions

### ```From the blog                                                       ```

A common use case in React apps is to perform a data mutation and then update state in response. For example, when a user submits a form to change their name, you will make an API request, and then handle the response. In the past, you would need to handle pending states, errors, optimistic updates, and sequential requests manually.

```js
// Before Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, setIsPending] = useState(false);
  ...
}
```

In React 19, we’re adding support for using async functions in transitions to handle pending states, errors, forms, and optimistic updates automatically.

```js
// Using pending state from Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);
      if (error) {
        setError(error);
        return;
      } 
      redirect("/path");
    })
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

By convention, functions that use async transitions are called “Actions”. 

---

# ✨ AI Research

**Originally**, `startTransition()` was introduced in React **to manage state update priority**—it allowed developers to mark **certain state updates as low priority**, preventing UI lag during expensive renders.

Later, with **React 19**, the concept of **"Actions"** was added, extending `startTransition()` to work seamlessly with **async functions**. This introduced automatic **pending states, error handling, and optimistic updates**, making it much easier to handle async operations without manual state management.

So the evolution looks like this:
1. **React 18** → `startTransition()` used **only** for **low-priority state updates**.
2. **React 19** → Expanded to **"Actions"**, allowing better async state management inside transitions.

It’s an interesting progression, but the core behavior remains—any **state update inside `startTransition()` is still low priority**, whether it's used with async functions or not.

---

Exactly — you're spot on, and your understanding aligns perfectly with how **React 19** (with the new Actions and useTransition patterns) and **Next.js App Router** concepts are evolving.

---

### Let's clarify with the right terminology:

### 🔹 `action` in **React 19**:

* A **client-side function** used with `<form action={...}>` or `useFormState`, `useFormStatus`, etc.
* It **initiates a transition** — potentially async — and is often tied to UI state.
* It might:

  * Call a **server action**
  * Handle client state updates (like loading, optimistic updates)
  * Then transition UI after receiving a response

### 🔹 `server action` in **Next.js / React 19**:

* A function marked with `'use server'`.
* Runs **only on the server** (e.g. accessing DB, secure APIs).
* You can call it from a client component using `form` actions or JavaScript directly.


### So yes — you often have:

> **Client `action`** → calls → **Server `action`**

### Example

#### `app/actions.js` — Server Action

```tsx
'use server';

export async function createNoteServerAction(formData) {
  const title = formData.get('title');
  // Save to DB
  await db.notes.create({ data: { title } });
}
```

#### `app/components/CreateNoteForm.jsx` — Client Component (with Client Action)

```tsx
'use client';

import { useTransition } from 'react';
import { createNoteServerAction } from '../actions';

export default function CreateNoteForm() {
  const [isPending, startTransition] = useTransition();

  const handleSubmit = (formData) => {
    startTransition(() => {
      // This is the "action" in React 19 sense
      createNoteServerAction(formData);
    });
  };

  return (
    <form action={handleSubmit}>
      <input name="title" required placeholder="Note title" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Creating...' : 'Create Note'}
      </button>
    </form>
  );
}
```

| Concept           | Where It Runs | Purpose                                          |
| ----------------- | ------------- | ------------------------------------------------ |
| **React action**  | Client        | Manages transitions, handles form submissions    |
| **Server action** | Server only   | Secure logic: DB writes, secrets, etc.           |