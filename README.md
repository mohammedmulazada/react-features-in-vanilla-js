# React features in vanilla Javascript

## Why?

React is an incredibly popular library, rightfully so since it takes away a lot of pain points.

My goal is to become better in Javascript development, and since I have had some experience with Javascript and React, my teacher Joost thought it would be a good idea to look at a few React features and how I can replicate those in Vanilla Javascript.

It's good to know how things work under the hood after all.

---

## Virtual DOM

On the React website there is a great explanation for what a virtual DOM is.

> The virtual DOM (VDOM) is a programming concept where an ideal, or “virtual”, representation of a UI is kept in memory and synced with the “real” DOM by a library such as ReactDOM. This process is called reconciliation.
>
> This approach enables the declarative API of React: You tell React what state you want the UI to be in, and it makes sure the DOM matches that state. This abstracts out the attribute manipulation, event handling, and manual DOM updating that you would otherwise have to use to build your app.

Simply said, you are responsible for writing the logic and what should happen based on things such as state and conditions, and React will match the DOM to that state.

This actually saves a lot of time in larger projects where elements need to change and update based on the data.

### How React does it

I have made a todo list that can be found [here.](https://codesandbox.io/s/qqopp4lq54) It is a bit sloppy since I get the values from the DOM which you normally should not do, but it is fine for these purposes.

As you can see, all I did was define the logic around the todo list. Where the new todo should be added, what to do if the input is empty.

React takes care of the rendering of the todo tasks.

---

## Reactivity

Reactivity is having your data object watched so that for example the DOM can update based on the actual value of your data object.

Here is an example of where this could go wrong normally:

```js
let a = 3

let b = a * 3
console.log(b) // 9
```

Here is what happens if you update `a`

```js
let a = 5

let b = a * 3
console.log(b) // 9
```

As you can see, we updated a, but the value of b is still using the old a.

We could solve this by either retyping `let b = a * 3` or implementing a `update()` function, but that would be incredibly annoying.

This is where reactivity comes in.

---

### How React does it

State is a core feature of React. State is used for all kinds of situations, for example, toggling a menu could be managed by state.

In this React example (using the new Hooks), we are incrementing a value by 1.

The online example can be viewed [here.](https://codesandbox.io/s/5xnrxrn44p)

```js
import React, { useState, useEffect } from "react"
import ReactDOM from "react-dom"

const App = () => {
	const [count, setCount] = useState(0)

	// Similar to componentDidMount and componentDidUpdate:
	useEffect(() => {
		// Update the document title using the browser API
		document.title = `You clicked ${count} times`
	})

	return (
		<div>
			<p>You clicked {count} times</p>
			<button onClick={() => setCount(count + 1)}>Click me</button>
		</div>
	)
}

const rootElement = document.getElementById("root")
ReactDOM.render(<App />, rootElement)
```

As you can see, clicking on the button will call the function `setCount()`, which is responsible for adding 1 to the previous value.

React does not by default watch the data object. This means that if you were to change the value by means other than those that React provices you with, it won't update the value in the DOM.

What happens now is that once you call the function `setCount` and add a value, React will intelligently check the previous state to see if the values are exactly the same or not, and then if needed update the DOM to reflect these changes.

This is why the number changes every time you click.

### Vanilla Javascript

In order to get the same behaviour in Javascript, we need to add some logic that does not exist yet.

```js
```
