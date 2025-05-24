### [Link to the blog](https://overreacted.io/jsx-over-the-wire/)

### Recap: JSON as Components

For any UI, the data begins its life as Models and ends its life as ViewModels. The transformation between Models and ViewModels has to happen somewhere.

The shape of ViewModels is fully dictated by the design of our user interface. This means that they will evolve over time together with our designs. Also, different screens need different ViewModels aggregated from the same underlying Models.

Modeling data from the server as REST Resources creates a tension. If REST Resources are close to raw Models, it may require multiple roundtrips and complex ad-hoc conventions to obtain the necessary ViewModels for a screen. If REST Resources are close to ViewModels, they get too coupled to the initial screens they were designed to represent, and don’t evolve together with the needs of the client.

We can resolve this tension by creating another layer—a Backend For Frontend (BFF). The job of the BFF is to translate the needs of the client (“give me data for this screen”) to REST calls on the backend. A BFF can also evolve beyond being a facade for REST, and instead load data directly using an in-process data layer.

Since the BFF’s job is to return all the data needed for each screen as a piece of JSON, it is natural to split up the data loading logic into reusable units. A screen’s ViewModel can be decomposed into a tree of ViewModels, corresponding to the pieces of server data that different components will want to receive on the client. These individual ViewModels can then be recombined and composed together.

These ViewModel functions can pass information to each other. This lets us customize the JSON we’re sending depending on the screen. Unlike with REST, we’re no longer trying to design canonical shapes like a “a post object” used throughout all responses. At any point, we can diverge and serve different ViewModels for the same information to different screens—whatever they want. These ViewModels are view models. They can—should?—have presentation logic.

We’re beginning to realize that ViewModels form a very similar structure to React components. ViewModels are like components, but for generating JSON. However, we still haven’t figured out how to actually pass the JSON they’re generating on the server to the components that need it on the client. It’s also annoying to deal with two parallel hierarchies. We’re onto something, but we’re missing something.

### Recap: Components as JSON

From the beginning of time, making web apps involved responding to request for a specific screen with all the data needed for that screen. (HTML is data, too.)

From the beginning of time, people looked for ways to make the generation of that “data” dynamic, to split it into reusable logic, and to pass parameters to that logic.

In the early days of the web, it was common to compose HTML by string manipulation. Unfortunately, it was easy to mess up and led to many issues.

This led many in the web community to banish markup to templates. But at Facebook, XHP proposed another approach: markup that produces objects.

It turns out that making markup a first-class coding primitive naturally leads to tags “returning” other tags—instead of MVC, we got functional composition.

XHP evolved into Async XHP, which allowed to keep the logic for rendering some UI close to the logic for loading the data it needs. This was extremely powerful.

Unfortunately, producing HTML as the primary output format is a dead end for interactive applications. You can’t “refresh” HTML in-place without blowing away the state, and state is important.

However, nothing actually constraints us to HTML. If tags are objects, they can be sent as JSON. Many of the most successful native apps are built this paradigm. (And if you need HTML, you can always turn JSON into HTML later on.)

Returning a tag of client primitives as a JSON tree is a nice way to represent “passing props” to the client.

### Recap: JSX Over The Wire

React Server Components solve the problems outlined in the first part by using techniques outlined in the second part. In particular, they let you “componentize” the UI-specific parts of your API and ensure they evolve together with your UI.

This means that there is a direct connection between your components and the server code that prepares their props. You can always “Find All References” to find from where on the server the data is flowing into each of your components.

Because React Server Components emit JSON, they don’t “blow away” the state of the page on refetches. Your components can receive fresh props from the server.

React Server Components emit JSON, but that JSON can also be (optionally) turned to HTML for first render. It’s easy to make HTML out of JSON, but not the inverse.

React Server Components let you create self-contained pieces of UI that take care of preparing their own server data. However, all this preparation occurs within a single roundtrip. Although your code is modular, their execution is coalesced.