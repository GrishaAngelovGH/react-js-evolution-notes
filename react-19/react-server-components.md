# [```⬅️ Go Back```](./features.md)

---

### Resource 1: [🔗 Making Sense of React Server Components w/ Josh Comeau](https://www.joshwcomeau.com/react/server-components/)

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