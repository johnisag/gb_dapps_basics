# React - Next.js

### WTF is React?

React is a web framework that makes it easy to build and reason about the 'view' of your web app.&#x20;

React essentially just gives you a template language, and you can **create Javascript functions that return HTML.**

React basically combined **Javascript and HTML** to produce a language they call **JSX**. In JSX, Javascript-like functions return HTML instead of regular Javascript things.

The combination of Javascript functions that return HTML are called **Components**.&#x20;

Example of Component:

```jsx
import "./styles.css";

function ShoppingList() {
  const items = ["Apples", "Bananas", "Grapes"];

  return (
    <div className="shopping-list">
      <h1>Shopping List</h1>
      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

&#x20;In JSX, you can write Javascript inside HTML by wrapping the JS code in curly braces `{` and `}`. What is inside  { }, asuuming that returns HTML, is essentially another component.

**Embedding components inside other components is the power of React.** This is called **Composition**.

### Data Passing between Components

Dynamic pages require components to render different data. The main use case of having components is being able to write code that is reusable and can, within it, contain different information without rewriting the entire code again.

**To generate this HTML:**

```html
<div class="App">
  <div class="cards">
  
    <div class="card">
      <img src="img_avatar.png" alt="Avatar" />
      <div class="container">
        <h4><b>Alice</b></h4>
        <p>Frontend Developer</p>
      </div>
     </div>
  
      <div class="card">
        <img src="img_avatar.png" alt="Avatar" />
        <div class="container">
          <h4><b>Bob</b></h4>
          <p>Backend Developer</p>
      </div>
      
    </div>
</div>
```

**We can use this:**

```javascript
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <Cards />
    </div>
  );
}

function Cards() {
  return (
    <div className="cards">
      <!--Data is passed to children through HTML attributes-->
      <Card name="Alice" job="Frontend Developer" />
      <Card name="Bob" job="Backend Developer" />
    </div>
  );
}

// Card receives an object as an argument
// We can destructure the object to get specific variables
// from inside the object - name and job
function Card({ name, job }) {
  return (
    <div className="card">
      <img src="https://picsum.photos/100" alt="avatar" />
      <div className="container">
        <h4>
          <b>{name}</b>
        </h4>
        <p>{job}</p>
      </div>
    </div>
  );
}
```

### Interactive Components

In Javascript, functions can contain functions within them. Example:

```javascript
function someFunc() {
  function otherFunc() {
    console.log("Hello!");
  }
  otherFunc();
}

someFunc(); // will print "Hello!"
otherFunc(); // will throw an error! undefined here
```

**Interactivity without input**

```javascript
function Button() {
  function handleClick() {
    console.log("Hello");
  }

  return (
    <button className="button" onClick={handleClick}>
      Click Me!
    </button>
  );
}
```

**Interactivity with input**&#x20;

