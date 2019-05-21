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

### A popular module

Making your own Virtual DOM can be daunting and/or very time extensive. A popular module that I find great is [virtual-dom](https://www.npmjs.com/package/virtual-dom).

This is one of the most popular vanilla-js virtual doms and has excellent documentation. We will be using this in our example app demo later on.

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

Before talking about Reactivity, we need to talk a little bit about state.

State is a core feature of React. State is used for all kinds of situations, for example, toggling a menu could be managed by state.

There is a good reason for using state, and that reason is called: **a single source of truth.**

You see, when you make an application, you don't want the logic and values to be all over the place. You want the values to come from one place and one place only.

This is where state comes in. We update state values by making a copy of the previous value and updating it, which makes the state immutable. To be clear, you can change values through Javascript directly, but this defeats the whole point.

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

Apparently, with Proxies we can override default behaviours, we can for example intercept an operation on an object to add custom logic on each operation.

##### Example

Here is an example to help us understand this better.

Let's see we have an object, and we want to access a property on that object, if the property exists, we want to return the value, however, if it doesn't exist, we want to return a custom message.

```js
const handler = {
	get: (target, name) => {
		if (name in target) {
			return target[name]
		} else {
			return "Error: this proves this works"
		}
	},
}

let p = new Proxy({}, handler)

p.foo = "bar"

console.log(p.foo) // returns 'bar'
console.log(p.nope) // returns 'Error: this proves this works'
```

---

So, this means that in our example we can have something happen while a function is called.

First we create a function and give it a state parameter, which we we later can fill in with our state.

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

And finally, a function that updates the state based on what we just typed in.

```js
    const listener = (event) => {
      state[event.target.dataset.model] = event.target.value;
    });

   document.querySelector('[data-model="name"]').addEventListener('keyup', listener);
   document.querySelector('[data-model="title"]').addEventListener('keyup', listener);
```

And there we have it! A simple and effective way to have Reactivity and state management.

---

## Making a demo

We are going to be creating a todo app using all of the things we just learned.

First off, we are going to define our HTML structure.

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Todo-list</title>
		<meta charset="UTF-8" />
	</head>

	<body>
		<div id="app"></div>

		<script src="src/index.js"></script>
	</body>
</html>
```

**The final result will be:**
```
A title
An input
A submit button

The todo task - Delete button
The todo task - Delete button
The todo task - Delete button
```

![example](http://i.imgur.com/vJkCNqK.jpg)

For this demo we will be using the virtual-dom module we saw earlier. Here is an example of how this module works.

```js
import { create, h } from "virtual-dom"

const render = state => {
	const children = state.list.map(t => h("li", {}, [t]))
	return h("ul", {}, children)
}

const INITIAL_STATE = {
	list: ["first", "second"],
}

let tree = render(INITIAL_STATE)
let rootNode = create(tree)

document.body.appendChild(rootNode)
```

Which will return a ul with li children using the 'first' and 'second' as data as defined in the INITIAL_STATE variable.

![image](http://i.imgur.com/mNlTn3s.jpg)

### Updating the DOM

We need a method to update the DOM when something changes, for example, when a new `<li>` gets added to the list.

Virtual-dom adds a function called `patches` that updates the DOM to what the state is. It also adds a function called `diff` that looks at the existing DOM and the new data and can spot the differences.

[Source.
](https://github.com/Matt-Esch/virtual-dom/blob/master/vdom/README.md)
We need these to the update the DOM with the newly added todos.

```js
// The function receives the state
const updateDom = state => {
	// newTree is responsible for holding and rendering the new state
	const newTree = render(state)
	// whereas patches is responsible for looking at the old tree and the new tree and looking at what's different
	const patches = diff(tree, newTree)

	// then we update the old tree to hold the new value
	tree = newTree
	// and finally we change the DOM to match with the new data
	rootNode = patch(rootNode, patches)
}
```

---

Since we know what our HTML will look like, we can define the state.

```js
const INITIAL_STATE = {
	todos: [],
	todoInputText: "",
}
```

Now we have the state itself and a method to update the DOM, but no method of updating the state yet. We can go ahead and make this now, using the proxy we learned about earlier.

```js
// this function takes the state and a function as arguments
export default ({ target, listener }) => {
	// we define the observable
	let observable

	const set = (target, name, value) => {
		target[name] = value
		// we pass the value through a function we give as argument, which will be the updateDom function
		listener(observable)
		return true
	}

	const get = (target, name) => {
		return target[name]
	}

	const handler = {
		set,
		get,
	}

	observable = new Proxy(target, handler)

	return observable
}
```

Okay, now we can focus on actually rendering the lists based on our input.

We will create `view` folder, since it is responsible for rendering the list.

In the view folder, we create a list, form and index.js.

---

### List

Here we create a file in which we define what and how the list should render. 

```js
import { h } from "virtual-dom"

export default state => {
	// when clicking on delete, we delete the specific item.
	const onRemove = index => {
		state.todos = [
			// select all of the items until the specific one
			...state.todos.slice(0, index),
			// return all the others after the index
			...state.todos.slice(index + 1),
		]
	}

	// here we define a button and it's logic
	const createDeleteButton = (text, index) =>
		h(
			"button",
			{
				onclick: () => onRemove(index),
			},
			[`${text} (click to delete)`]
		)

	// for every todo item, we create a deletebutton and pass it the index, so that we can delete the correct one.
	const elements = state.todos.map((t, index) => createDeleteButton(t, index))

	return h("div", {}, [h("div", {}, [elements])])
}
```

### Form



```js
import { h } from "virtual-dom"

export default state => {
	//when clicking the add button
	const onAddClick = () => {
		// and if there is text filled into the input
		if (state.todoInputText) {
			// we add that value to the todos state
			state.todos = [...state.todos, state.todoInputText]
			state.todoInputText = ""
		}
	}

	// this is to update the state based on the value
	const onInputValueChange = event => {
		state.todoInputText = event.target.value
	}

	//adds an add button
	const addButton = h(
		"button",
		{
			// which is only enabled once there is state in the input, which means something has been filled in.
			onclick: onAddClick,
			disabled: !state.todoInputText,
		},
		["Add Todo"]
	)

	// defines the input
	const input = h("input", {
		placeholder: "What do you have todo?",
		type: "text",
		// the value matches the state (controlled component)
		value: state.todoInputText,
		// on change it calls the onInputValueChange function
		oninput: onInputValueChange,
	})

	// we put it all together here
	return h("nav", {}, [
		h("div", {}, [
			h("div", {}, [h("a", {}, ["Todo-list"])]),
			h("form", {}, [h("div", {}, [input]), addButton]),
		]),
	])
}
```

And here we pull the previous two functions together and put them in a render function.

```js
import { h } from "virtual-dom"

import form from "./form"
import list from "./list"

export const render = state => {
	return h("div", {}, [form(state), list(state)])
}
```

### index.js

And now we can pull in our functions and make it all work!

```js
import { patch, create, diff } from "virtual-dom"
import { render } from "./view"
import observable from "./observable"

// this gets called when the state changes and is responsible for updating the DOM
const updateDom = state => {
	const newTree = render(state)
	const patches = diff(tree, newTree)

	tree = newTree
	rootNode = patch(rootNode, patches)
}

// we define the initial state here
const INITIAL_STATE = {
	todos: [],
	todoInputText: "",
}

// this is where the state gets watched through a Proxy
const state = observable({
	target: INITIAL_STATE,
	listener: updateDom,
})

let tree = render(state)
let rootNode = create(tree)

document.body.appendChild(rootNode)
```

Final result is available here: https://codesandbox.io/embed/proud-sun-6kt0r?codemirror=1

## Conclusion

As we can see, there is a good argument for using React (or similair frameworks/libraries). It mostly boils down to using an opionated framework that abstracts a lot of the more cumbersome and repetitive work.

It is definitely possible to use vanilla Javascript and depending on the scale of the job it could be even better than using a framework. React also is not the only and best choice, there are many frameworks out there achieving the same and each is great in their own right. React simply is the largest one right now and that's a great reason to work with it.

Let's also not forget how lovely it is to use React when working in teams. React is great for larger teams building a large scale product.

But as always, every choice we make has to be weighed beforehand. React and such are great tools that we use, but each of those choices we are stuck with. It means that you, your team, the customer, the customers developers, future maintainers will all be stuck to the choices we made, and I think it is very important to think of these things.
