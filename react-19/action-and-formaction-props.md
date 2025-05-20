# [```⬅️ Go Back```](./features.md)

React 19 introduces `action` and `formAction` props for `<form>`, `<input>`, and `<button>` elements. These props allow actions to be directly associated with form elements, streamlining form handling.

# ✨ AI Research

In HTML, and now enhanced in **React 19**, the `formAction` attribute on an `<input>` (or `<button>`) is used to **override the parent `<form>`'s `action`** when that specific button is used to submit the form.

### **Conceptually:**

* Normally, a `<form>` tag defines a single `action` URL or function.
* But sometimes, you want **different submit buttons to send data to different targets** (or invoke different actions).
* `formAction` lets each `<input type="submit">` or `<button type="submit">` specify its own action.

### **In React 19**:

* You can now use **functions** (not just URLs) as `formAction` values.
* React handles it declaratively and supports things like async handlers, pending states, and automatic form resets.

### **Example** – Functional `formAction` in React 19:

```jsx
function saveDraft(data) {
  console.log("Saving draft:", Object.fromEntries(data));
}

function publishPost(data) {
  console.log("Publishing post:", Object.fromEntries(data));
}

function PostForm() {
  return (
    <form>
      <input name="title" placeholder="Title" />
      <input name="body" placeholder="Body" />

      <button type="submit" formAction={saveDraft}>
        Save Draft
      </button>

      <button type="submit" formAction={publishPost}>
        Publish
      </button>
    </form>
  );
}
```

### **How it works**:

* Both buttons submit the form, but **React calls the `formAction` function attached to the button** used.
* `formAction` receives a `FormData` object representing the form’s contents.

---

# ✨ AI Research

 HTML has always supported the `action` attribute on `<form>` elements. So when we say:

> “React 19 introduces `action` and `formAction` props for `<form>`, `<input>`, and `<button>` elements…”

we don't mean **HTML** is getting something new — we mean:

---

### What’s Actually New in **React 19**:

React 19 now lets you use **JavaScript functions** as values for `action` and `formAction`, **instead of just URLs**.

---

### Before React 19:

```jsx
<form action="/submit">
  <button type="submit">Submit</button>
</form>
```

* `action` was just passed through as a plain HTML attribute (like in raw HTML).
* It could only be a **URL string** — pointing to a server endpoint.
* You handled submissions via JavaScript with `onSubmit` handlers and state.

---

### Now in React 19:

You can do this:

```jsx
<form action={async (formData) => {
  const data = await submitToServer(formData);
  console.log(data);
}}>
  <button type="submit">Submit</button>
</form>
```

Or even on the button:

```jsx
<button type="submit" formAction={myActionFunction}>
  Save
</button>
```

So now `action` and `formAction` can be:

* **Functions** (even async),
* Automatically receive `FormData`,
* Integrated with **transitions**, **pending states**, **form resets**, and **Suspense**.

---

### In Summary:

> The `action` attribute has always existed in HTML, but **React 19 introduces a new behavior**: the ability to use **function values** for `action` and `formAction` in React components, making form handling more declarative and powerful.