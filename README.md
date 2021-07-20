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
# Handling Events
-  If you forget to bind this.handleClick and pass it to onClick, this will be undefined when the function is actually called. Binding in the constructor is recommended, to avoid performance problems:
```
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }
```
- to pass extra parameters:
```
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```
- with bind, any further arguments are automatically forwarded.
# Conditional Rendering
- In rare cases you might want a component to hide itself even though it was rendered by another component. To do this return null instead of its render output:
```
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }
```
- Returning null from a component’s render method does not affect the firing of the component’s lifecycle methods. For instance componentDidUpdate will still be called.
# Lists and Keys
- You can build collections of elements and include them in JSX using curly braces {}, then include the entire listItems array inside a ul element, and render it to the DOM:
```
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
- But keys should be given to the elements inside the array to give the elements a stable identity. They help React identify which items have changed, are added, or are removed:
```
<li key={number.toString()}>
  {number}
</li>
```
- The best way to pick a key is to use a string that uniquely identifies a list item among its siblings. Most often you would use IDs from your data as keys:
```
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```
- Using indexes as keys is not recommended.
- Keys don’t need to be globally unique.
- A good rule of thumb is that elements inside the map() call need keys:
```
function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()} value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
- Keys serve as a hint to React but they don’t get passed to your components. If you need the same value in your component, pass it explicitly as a prop with a different name. Here, the Post component can read props.id, but not props.key:
```
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```
- JSX allows embedding any expression in curly braces so we could inline the map() result, which sometimes results in clearer code:
```
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```
# Forms
- In HTML, form elements such as input, textarea, and select typically maintain their own state and update it based on user input. In React, mutable state is typically kept in the state property of components, and only updated with setState(). We can combine the two by making the React state be the “single source of truth”. Then the React component that renders a form also controls what happens in that form on subsequent user input. An input form element whose value is controlled by React in this way is called a “controlled component”:
```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
# Lifting State Up
- Often, several components need to reflect the same changing data. It's recommended to lift the shared state up to their closest common ancestor:
```
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
    const temperature = this.props.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}

class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = {temperature: '', scale: 'c'};
  }

  handleCelsiusChange(temperature) {
    this.setState({scale: 'c', temperature});
  }

  handleFahrenheitChange(temperature) {
    this.setState({scale: 'f', temperature});
  }

  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

    return (
      <div>
        <TemperatureInput
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange} />
        <TemperatureInput
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange} />
        <BoilingVerdict
          celsius={parseFloat(celsius)} />
      </div>
    );
  }
}
```
- To collect data from multiple children, or to have two child components communicate with each other, you need to declare the shared state in their parent component instead. The parent component can pass the state back down to the children by using props; this keeps the child components in sync with each other and with the parent component.
- There should be a single “source of truth” for any data that changes in a React application. This allows less room for bugs.
- Usually, the state is first added to the component that needs it for rendering. Then, if other components also need it, you can lift it up to their closest common ancestor.
- If something can be derived from either props or state, it probably shouldn’t be in the state.
