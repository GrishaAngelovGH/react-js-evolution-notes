# [```⬅️ Go Back```](./features.md#9-server-actions)


### React Docs

Note: Until September 2024, we referred to all Server Functions as “Server Actions”. If a Server Function is passed to an action prop or called from inside an action then it is a Server Action, but not all Server Functions are Server Actions. The naming in this documentation has been updated to reflect that Server Functions can be used for multiple purposes.

Server Functions allow Client Components to call async functions executed on the server.

### [NexJS Docs](./nextjs.md#server-functions)

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
---
## Syntax Podcast #766: React Server Components: Form Actions + Server Actions
---

What is the server of a vanilla React site if you're not using something like this (framework)?

It can be a server that is running React on the server. It can be a build step. So, like, React server components don't necessarily need a server.
They can be prerendered as as part of a build step instead of actually being on the server, which is kind of interesting. But, generally, yeah, you're going to reach for a a framework that implements the whole server thing for you.

✅ Topic: Explanation of actions in general beyond just form actions

They are often you're gonna hear the word form action, and you're gonna hear the word server action thrown around.

But we're gonna start peeled back a little bit from form actions and server actions, and we're just gonna call them actions. Because actions are functions that can be run whenever likely when a form is submitted. Like, the the hugest use case for server actions to form actions is when you submit a form and you want to run some code.

This is going to be replacing a lot of your submit logic that you've had, which I was just saying, and they can run either client side or server side. So these are not just if this is not entirely for sending data to the server and getting stuff back, it can just run entirely client side, which is a very common use case where, alright, I have this form or I have this UI. And when somebody does something, I need to run a function that will generate some state.

I see, like, sort of 2 huge benefits to this type of thing. 

As first, actions allow you to tie state, which is your data, your submit handler logic, like, what happens when somebody clicks a button or submits a form, and pending, which is often people use, like, useTransition for to create, like, a pending state. Often, you are cobbling together your logic, your state, your pending into, like, a useEffect or or using a third party library. Actions allow you to sort of put that all together into a single function. 

And then the second huge benefit here is that it allows you to call server side code from the client without any sort of APIs. And and that's the massive benefit to me is that you can wire up a button that says "save user", have a user type into a form input, click that button, and you can bind that button to a function that runs on your server side. That function has database logic validation, anything that you would normally do on a server without having to do a GraphQL API, a REST API, you know, like, all of the the overhead of of that type of thing. So it's a a massive productivity boost to be able to have, like, a RPC, which means remote procedural call. Being able to call server side code from the client is such a benefit. 

So actions, they're often tied to a form submit, but they do not have to be.
Sometimes, some examples of what you might want to use an action for is, at the very basic, you want to have somebody click a button and increment a number by 1.

Using actions. Here's my next point. Actions can be called anywhere, but the most likely are going to be called in these 3 areas:

1) on a form submit. You pass an action to the action attribute of a form element like action={handleSubmit}

2) you might wanna call it programmatically, in an event handler. So you use something like downshift to to create a a combo box, and you type something. And when somebody hits enter on an item in the combo box, you might wanna programmatically submit that event handler. So you that's the example of it doesn't have to be tied to a form. That's why they're not just called form actions. They're just called actions. 

3) you could you could also run them in a useEffect. So if you have some other code that is sort of watching for a a specific change and you wanna you wanna submit this action whenever that item changes, you can call up a custom useEffect to to do that. 

---

So now in order to use an actual action, you can, like I said, pass them to a form's action attribute. But if you want a little bit more control, you're going to use 1 or or all of the following hooks. 

- useActionState() hook (useFormState() was previous name)

```js
import { useActionState } from "react";

async function submitForm(prevState, formData) {
  const username = formData.get("username");
  await new Promise((resolve) => setTimeout(resolve, 1000)); // Simulating async process
  return { message: `Submitted: ${username}` };
}

export default function MyForm() {
  const [state, formAction, isPending] = useActionState(submitForm, { message: "Waiting..." });

  return (
    <form action={formAction}>
      <input type="text" name="username" placeholder="Enter your name" />
      <button type="submit">Submit</button>
      <p>{state.message}</p>
    </form>
  );
}
```

- useFormStatus() hook: This is a hook that allows you to access if the form is being submitted. It must be used inside of a form element, and this will give you the information of if it's pending, so you might wanna use that to, again, disable inputs, show a spinner. 

Running actions on the client vs server side. So actions, like I said, they can run server or on the client. And how do you tell React that you only want this code to run on the server?

Top feature of this is being able to write a function with server side code in it. You can put raw SQL statements inside of this function. You can export it from a file, go into a client component, import it in, and just say, onSubmit or action={saveUser}. And just like that, like, being able to go from the server to the client, of course, if your your whole stack is in JavaScript. So the way that you mark a function as being server only is by either inside of the action itself, And then when I say action, it's a function. You write the words "use server" in quotes, and that marks that function as a server only function, And that will make sure that that code is never run client side. It's not gonna import any of your server dependencies on the client Node. What's more likely is you're gonna have, like a lib folder with all of your functions inside of that. And every single file inside of there, you're just gonna put "use server" at the top of the file. And that marks that whole file as code that should only ever be run on the server. So, usually, I'll have, like, an actions.ts file, mark it as a "use server" at the top, and then you write all of your updater functions and and queries inside of that.

So you have to mark client components with use client, and you have to mark server actions with use server.

- useOptimistic() hook

So Optimistic UI is you know, the word optimistic here comes from the fact that you are optimistically thinking that this server side action that you're doing will be successful. So let's say you are you you have, like, a to do list. You submit that to do list in an ideal situation that to do typically goes to the server, gets loaded in the database, and then that gets populated probably into your UI, like a typical to do list. It would pop up and and, you know, that server process can take some time. There's a round trip there, and users are when they click something and it doesn't happen instantly, your users might try to click it again. That might think your app is broken. Your app is gonna feel slow. On a bad network, that's going to take even longer. So optimistic UI is the process of saying, hey I feel like this thing is going to succeed, and, therefore, I'm going to update the UI immediately with the success state. So even without getting the server database storage of that to do was successful, you're still populating that to do into your UI instantly. And in a situation where that would then fail, you gotta handle that. So it would remove it from the UI or it would give an error message. There's various ways to handle that.