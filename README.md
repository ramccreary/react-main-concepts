- do this tutorial: https://reactjs.org/tutorial/tutorial.html
- while following along with this guide: https://reactjs.org/docs/hello-world.html
# JSX and Rendering
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
# Components and Props
- you can define a component with a function:
```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
- or an ES6 class:
```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
- Always start component names with a capital letter, or else React will treat your component as DOM tags (which start with lowercase).
- React elements can represent user-defined components:
```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```
- give props (properties) names from the component’s own point of view rather than the context in which it is being used.
- props are read-only. a component must never modify its own props. it needs to act like a pure function. if you use stateful components, they can change their output over time in response to user actions, network responses, and anything else, without violating this rule.
# State and Lifecycle
- React components can have state by setting this.state in their constructors. State is private and fully controlled by the component.
- To convert a function into a class so we can use state / lifecycle methods:
```
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}
```
VVV
```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
- To add state:
1. Replace this.props.date with this.state.date in the render() method
2. Add a class constructor that assigns the initial this.state
3. Remove the date prop from the <Clock /> element
```
// result looks like this

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />, // this used to be Clock date={new Date()}
  document.getElementById('root')
);
```
- All React component classes that have a constructor should start with a super(props) call.
- "mounting" = rendering a component to the DOM for the first time
- "unmounting" = removing a component from the DOM
- componentDidMount() method runs after the component output has been rendered to the DOM
- componentWillUnmount() method runs if the component is removed
- The only place where you can assign this.state is the constructor. To use state elsewhere correctly, do not modify state directly like this:
```
// Wrong
this.state.comment = 'Hello';
```
- instead, use setState():
```
// Correct
this.setState({comment: 'Hello'});
```
- do not rely on the values of this.props or this.state for calculating the next state, because they may be updated asynchronously:
```
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
- instead, use a second form of setState() that accepts a function which receives the previous state as the first argument, and the props at the time the update is applied as the second argument:
```
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```
- When you call setState(), React merges the object you provide into the current state.
