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

React takes care of the rendering of the todo tasks. In the example below, you see a simplified method of rendering each todo value in a paragraph element.

```js
<div className='todos'>
	{todos && todos.maptodo => (<p>{todo.value}</p>)}
</div>
```

All this block of code does is loop over the amount of todos. For every todo, it will create a new div element and adds buttons and the todo value.

This might not seem special, but recreating this in vanilla JS might show why this is so nice.

### Vanilla JS

We can recreate the element rendering based on the data in Vanilla JS.

First, lets look at how one would normally render element based on data, without a library or self written framework.

Let's say you have a todo list as well, and the data looks like this:

```js
const todos = ["Do the dishes", "Write this sentence", "Do groceries"]
```

This is what you would have to do, assuming you have a div in your HTML with a classname of 'todos'

```js
const todos = ["Do the dishes", "Write this sentence", "Do groceries"]
const container = document.querySelector(".todos")

todos.map(todo => {
	const paragraph = document.createElement("p")
	paragraph.textContent = todo

	return container.appendChild(paragraph)
})
```

#### Main differences at this point in time

As you can see, right now, between the two code blocks from React and Vanilla JS, it doesn't take much more work to do it in Vanilla JS. You might wonder, why use React when it can be done this easily in Vanilla JS?

It comes down to Reactivity, which we will cover later.

### Making your own virtual DOM

The code below is a vanilla virtual DOM that will enable you to create HTML Elements with Javascript functions.

```js
;(function() {
	"use strict"

	function getArgs(args) {
		if (args.length === 1) {
			return [undefined, args[0]]
		}

		return [args[0], args[1]]
	}

	function convertToTextNodeIfNodeIsString(possibleString) {
		return typeof possibleString === "string"
			? document.createTextNode(possibleString)
			: possibleString
	}

	function camelToKebab(camel) {
		return camel.replace(/([A-Z])/g, g => `-${g[0].toLowerCase()}`)
	}

	function createElement(tag, ...args) {
		const [attributes, children] = getArgs(args)

		const node = document.createElement(tag)

		if (attributes) {
			for (let attribute in attributes) {
				if (attribute === "className") {
					node.classList.add(attributes[attribute])
				} else {
					node.setAttribute(
						camelToKebab(attribute),
						attributes[attribute]
					)
				}
			}
		}

		if (children) {
			const childNodes = Array.isArray(children) ? children : [children]
			childNodes.forEach(child =>
				node.appendChild(convertToTextNodeIfNodeIsString(child))
			)
		}

		return node
	}

	const div = createElement.bind(null, "div")
	const p = createElement.bind(null, "p")
	const ul = createElement.bind(null, "ul")
	const li = createElement.bind(null, "li")
	const a = createElement.bind(null, "a")

	const items = ["foo", "bar", "baz"]

	const container = div(
		{ className: "hello" },
		div(
			{ className: "ul-container", dataRef: "hello" },
			ul(
				items.map(item =>
					li(a({ className: "li-link", href: `${item}` }, item))
				)
			)
		)
	)

	document.body.appendChild(container)
})()
```

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

I actually found a great example online by Francesco Esposito where he goes into two-way data binding _and_ reactivity. He uses an advanced Javascript future Proxy which I am very excited to try.

[The demo can be found here.](https://stackblitz.com/edit/two-way-data-binding-poc)

Assuming we have an HTML markup like this:

```html
<div class="field">
	<label for="name">Enter your name:</label>
	<input id="name" type="text" name="name" data-model="name" />
</div>

<div class="field">
	<label for="title">Enter your title:</label>
	<input id="title" type="text" name="title" data-model="title" />
</div>

<div class="results">
	<h1 data-binding="name"></h1>
	<h2 data-binding="title"></h2>
</div>
```

And a state where we put the initial values:

```js
const state = {
	name: "Mohammed",
	title: "Front-end Developer",
}
```

It would be easy to add the Javascript values to the HTML elements, **but only the first time**.

```js
document.querySelector('[data-binding="name"]').innerHTML = state.name
document.querySelector('[data-binding="title"]').innerHTML = state.title
document.querySelector('[data-model="name"]').value = state.name
document.querySelector('[data-model="title"]').value = state.title
```

But what we need is a way to reflect any possible changes in the state to the HTML elements.

#### Proxy

For this example, we will be using Javascript Proxies. I want to preface this that I know there are other available options that will work with all browsers. For this example though, I find it more useful to learn and explain something new than it is to use the old way.

##### What does Proxy do?

[According to the MDN Docs on Javascript Proxies:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

_The Proxy object is used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc)._

So, this means that in our example we can have something happen while a function is called.

```js
    const createState = (state) => {
      return new Proxy(state, {
        set(target, property, value) {
          target[property] = value; // default set behaviour
          render(); // updates the view every time the state changes
          return true;
    }
      });
    };

    const state = createState({
      name = 'Mohammed'
      title = 'Front-end Engineer'
    });
```

And then we create a function that is responsible for adding the state values to the HTML objects.

```js
const render = () => {
	document.querySelector('[data-binding="name"]').innerHTML = state.name
	document.querySelector('[data-binding="title"]').innerHTML = state.title
	document.querySelector('[data-model="name"]').value = state.name
	document.querySelector('[data-model="title"]').value = state.title
}
```

And finally, a function that updates the state based on what we just typed inn.

```js
    const listener = (event) => {
      state[event.target.dataset.model] = event.target.value;
    });

   document.querySelector('[data-model="name"]').addEventListener('keyup', listener);  
   document.querySelector('[data-model="title"]').addEventListener('keyup', listener);
```