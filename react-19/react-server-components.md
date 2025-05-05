# [```⬅️ Go Back```](./features.md)

---

### Resource 1: [🔗 Making Sense of React Server Components w/ Josh Comeau](https://www.joshwcomeau.com/react/server-components/)

### Resource 2: [React for Two Computers /w Dan Abramov](#react-for-two-computers-w-dan-abramov)

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