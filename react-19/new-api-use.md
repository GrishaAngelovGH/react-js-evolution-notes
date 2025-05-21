# [```⬅️ Go Back```](./features.md)

In React 19 we’re introducing a new API to read resources in render: use.

For example, you can read a promise with use, and React will Suspend until the promise resolves:

```js
import {use} from 'react';

function Comments({commentsPromise}) {
  // `use` will suspend until the promise resolves.
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}
```

You can also read context with use, allowing you to read Context conditionally such as after early returns:

```js
import {use} from 'react';
import ThemeContext from './ThemeContext'

function Heading({children}) {
  if (children == null) {
    return null;
  }
  
  // This would not work with useContext
  // because of the early return.
  const theme = use(ThemeContext);
  return (
    <h1 style={{color: theme.color}}>
      {children}
    </h1>
  );
}
```