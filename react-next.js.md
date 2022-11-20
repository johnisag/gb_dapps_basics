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
import "./styles.CSS";

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

```javascript
function PrintTypedText() {
  function handleOnChange(text) {
    console.log(text);
  }

  return <input type="text" onChange={(e) => handleOnChange(e.target.value)} />;
}
```



### React Hooks - useState and useEffect

What about updating variables?

When updating regular variables from within a React component, even though the value is updated, React does not re-render the view of the web app. It does not automatically update the HTML view of the page.

#### useState

We can use the **`useState` ** hook to maintain a variable which automatically re-renders the HTML displayed on the screen every time its value is changed.&#x20;

```javascript
function ThisWorks() {
  // myNumber is the variable itself (state variable)
  // setMyNumber is a function that lets us update the value
  // useState(0) initializes the React Hook
  // with the starting value of 0
  const [myNumber, setMyNumber] = useState(0);

  function increment() {
    // Sets the new value to the old value + 1
    setMyNumber(myNumber + 1);
  }

  return (
    <div>
      <p>{myNumber}</p>
      <button onClick={increment}>Increment!</button>
    </div>
  );
}
```

```javascript
function StateWithInput() {
  // myName is the variable
  // setMyName is the updater function
  // Create a state variable with initial value
  // being an empty string ""
  const [myName, setMyName] = useState("");

  function handleOnChange(text) {
    setMyName(text);
  }

  return (
    <div>
      <input type="text" onChange={(e) => handleOnChange(e.target.value)} />
      <p>Hello, {myName}!</p>
    </div>
  );
}
```

**useState with complex objects**

```javascript
function StateArray() {
  const [fruits, setFruits] = useState([]);
  const [currentFruit, setCurrentFruit] = useState("");

  function updateCurrentFruit(text) {
    setCurrentFruit(text);
  }

  function addFruitToArray() {
    // The spread operator `...fruits` adds all elements
    // from the `fruits` array to the `newFruits` array
    // and then we add the `currentFruit` to the array as well
    // DO NOT UPDATE STATE VARIABLE DIRECTLY WITH PUSH
    // IT IS INVALID STATE UPDATE WITH UNEXPECTED OUTCOME
    const newFruits = [...fruits, currentFruit];
    setFruits(newFruits);
  }

  return (
    <div>
      <input type="text" onChange={(e) => updateCurrentFruit(e.target.value)} />
      <button onClick={addFruitToArray}>Add Fruit</button>

      <ul>
        {fruits.map((fruit, index) => (
          <li key={index}>{fruit}</li>
        ))}
      </ul>
    </div>
  );
}ja
```

### useEffect

* Need to automatically run some code when the page is first loaded
* Need to automatically run some code when a certain state variable changes

These type of functions are called side effects. React gives us the **`useEffect` ** hook which allows us to write these kinds of effects. **`useEffect` takes 2 arguments**

* **A function:** The code that runs when the effect is run&#x20;
* **A dependency Array:** It specifies _when_ to trigger the side effect
  * An empty array means the code run once on PageLoad
  * Having multiple variables in the array means that the effect will be triggered in each of any of these variables

**Run once in page load example:**

```javascript
function LoadDataFromServer() {
  // Create a state variable to hold the data returned from the server
  const [data, setData] = useState("");
  // Create a state variable to maintain the loading state
  const [loading, setLoading] = useState(false);

  async function loadData() {
    // Set `loading` to `true` until the API call returns a response
    setLoading(true);

    // Imaginary function that performs an API call to load
    // data from a server
    const data = await apiCall();
    setData(data);

    // We have the data, set `loading` to `false`
    setLoading(false);
  }

  // loadData is the function that is run
  // An empty dependency array means this code is run
  // once when the page loads
  useEffect(() => {
    loadData();
  }, []);

  // Display `"Loading..."` while `loading` is `true`,
  // otherwise display `data`
  return <div>{loading ? "Loading..." : data}</div>;
}
```

**Run each time a state variable changes example:**

```javascript
function DependentEffect() {
  const names = ["Alice", "Bob", "Charlie", "David", "Emily"];

  const [recommendations, setRecommendations] = useState([]);
  const [searchText, setSearchText] = useState("");

  useEffect(() => {
    // If user is not searching for anything, don't show any recommendations
    if (searchText.length === 0) {
      setRecommendations([]);
    }
    // Else, find recommendations
    else if (searchText.length > 0) {
      const newRecs = names.filter((name) =>
        name.toLowerCase().includes(searchText.toLowerCase())
      );
      setRecommendations(newRecs);
    }
  }, [searchText]);

  return (
    <div>
      <input type="text" onChange={(e) => setSearchText(e.target.value)} />
      <h2>Recommendations:</h2>
      <ul>
        {recommendations.map((rec, index) => (
          <li key={index}>{rec}</li>
        ))}
      </ul>
    </div>
  );
}
```
