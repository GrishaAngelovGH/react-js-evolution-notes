# [```⬅️ Go Back```](./features.md#9-server-actions)


### React Docs

Note: Until September 2024, we referred to all Server Functions as “Server Actions”. If a Server Function is passed to an action prop or called from inside an action then it is a Server Action, but not all Server Functions are Server Actions. The naming in this documentation has been updated to reflect that Server Functions can be used for multiple purposes.

Server Functions allow Client Components to call async functions executed on the server.

### Example

* A **Client Component** to handle UI & user interaction.
* A **Server Action** to perform a secure backend task (like writing to a database).

---

### 🗂️ File structure:

```
app/
├── actions.js       ← server actions
├── components/
│   └── CreateNoteButton.jsx  ← client component
├── page.jsx                  ← server component
```

---

### ✅ `app/actions.js` — Server Actions

```tsx
'use server'; // This must be at the top!

import { db } from '@/lib/db'; // your actual DB module

export async function createNoteAction(formData) {
  const title = formData.get('title');

  await db.notes.create({
    data: { title },
  });
}

```

---

### ✅ `app/components/CreateNoteButton.jsx` — Client Component

```tsx
'use client';

import { useRef } from 'react';
import { createNoteAction } from '../actions'; // importing from central actions file

export default function CreateNoteButton() {
  const formRef = useRef(null);

  async function handleSubmit(event) {
    event.preventDefault();

    const formData = new FormData(formRef.current);
    await createNoteAction(formData);

    // Optional: reset form or show feedback
    formRef.current.reset();
  }

  return (
    <form ref={formRef} onSubmit={handleSubmit}>
      <input name="title" placeholder="Note title" required />
      <button type="submit">Create Note</button>
    </form>
  );
}
```

---

### ✅ `app/page.jsx` — Server Component

```tsx
import CreateNoteButton from './components/CreateNoteButton';

export default function Page() {
  return (
    <main>
      <h1>Create a New Note</h1>
      <CreateNoteButton />
    </main>
  );
}
```