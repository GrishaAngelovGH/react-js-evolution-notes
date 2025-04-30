### React 17 (2020) – "No New Features"

Foundation for gradual upgrades

No new developer-facing features, but:
- New event system (delegated to root rather than document)
- Support for gradual upgrades – multiple React versions can coexist
- Better tooling integration and backward compatibility
- Focused on making it easier for libraries and apps to upgrade over time.

---

### React 16 (2017) – "React Fiber"

Massive internal rewrite (Fiber architecture)

- New Reconciliation Engine ("Fiber") – allows interruption, prioritization, and async rendering.
- Error Boundaries – use componentDidCatch() to handle errors in the component tree.
- Fragments – ```<>...</>``` syntax to return multiple elements without a wrapping div.
- Render Return Types Expanded – can now return arrays, strings, numbers, etc.
- Portals – render children into a DOM node outside the parent hierarchy.
- Complete rewrite of server-side rendering (SSR) – smaller and faster.

---

### React 15 (2016)

Refinement of the DOM rendering model

- Improved DOM nesting validation: More accurate enforcement of HTML rules.
- Better SVG support
- Bug fixes in controlled vs uncontrolled components
- React.createClass deprecated in 15.5 (moved to create-react-class)
- Preparation for future improvements in layout/DOM handling