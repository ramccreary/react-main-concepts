- do this tutorial: https://reactjs.org/tutorial/tutorial.html
- while following along with this guide: https://reactjs.org/docs/hello-world.html

``` 
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

- A React component class takes in parameters, called props (short for “properties”), and returns a hierarchy of views to display via the render method, which returns a description of what you want to see on the screen (a React element). These are written in JSX. Each element can be stored in a variable and passed around.
- you should wrap your stuff in parenthesis to avoid automatic semicolon insertion
- split these over multiple lines for readability
- use camelCase naming

```
return React.createElement('div', {className: 'shopping-list'},
  React.createElement('h1', /* ... h1 children ... */),
  React.createElement('ul', /* ... ul children ... */)
);
```

- By default, React DOM escapes any values embedded in JSX before rendering them. Thus it ensures that you can never inject anything that’s not explicitly written in your application. Everything is converted to a string before being rendered. This helps prevent XSS (cross-site-scripting) attacks.

```
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

- Babel compiles JSX down to React.createElement() calls. Takes this:

```
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

- And turns it into this, a React element:

```
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

- This is a “root” DOM node because everything inside it will be managed by React DOM:

```
<div id="root"></div>
```

- To render a React element into a root DOM node, pass both to ReactDOM.render(). The ONLY way to update the UI is to create a new element and pass to ReactDOM.render():

```
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

- In practice, most React apps only call ReactDOM.render() once and use stateful components.

