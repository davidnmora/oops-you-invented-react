It's the 2020s, and React.js is the most popular frontend framework. Everyone's using it. Everyone's hiring for it.

And everyone *has no idea how it works.*

But not you. Why? Because, way back in 2010, you accidentally invented React...

******

## It's 2010...
Bieber is in full swing, you definitely don't have a crush on your programming buddy Alejandra, and web dev looks like this:

```html
<div id="html-and-js-root" style="border: 1px dotted red; margin: 12px">
	<div class="seconds-container">
		<span class="seconds-number"></span>
		<span style="font-style: italic">seconds</span>
	</div>
</div>

<script>
	const seconds = document.querySelector('#html-and-js-root .seconds-container .seconds-number')
	seconds.innerHTML = (new Date()).getSeconds().toString()
</script>

```
#### Here's what you love about it:
1. HTML is super declarative: it shows you exactly the structure of the page.
2. JS is dynamic. You can update stuff on the fly.

#### And here's what sucks about it:
1. HTML is static. And repetitive. Want 20 images? Get ready to copy and paste. Want to update them dynamically based on data? No can do. *Ahh, but isn't that where JS comes into play?* Sure, but it sucks...
2. Writing JS feels like being a surgeon who blindly reaches into his HTML patient's body, slices stuff up, and hopes it works.


## Then you have an 💡: let's do everything in JS

JS is dynamic, event-driven, and easy to compose into modular functions and files. A pure JS approach could solve all the pitfalls of working in both HTML & JS!

*But can we create HTML elements with only JS?* We can!

## ... and it's an imperative, ugly mess 😱

```javascript
const secondsContainer = document.createElement('div')
secondsContainer.setAttribute('class', 'seconds-container')


const secondsNumber = document.createElement('span')
secondsNumber.setAttribute('class', 'year')
secondsNumber.innerText = (new Date()).getSeconds().toString()

secondsContainer.append(secondsNumber)


const secondsText = document.createElement('span')
secondsText.setAttribute('style', 'font-style: italic')
secondsText.innerText = ' seconds'

secondsContainer.append(secondsText)


const root = document.querySelector('#js-root')
root.append(secondsContainer)
```

You're kind of crushed: your 💡 wasn't so great.

Then you squint at your code. And it hits you -- you're basically doing 3 things over and over again:
1. creating DOM element of a certain type
2. setting its properties
3. appending it to a parent DOM element

And since you're a programmer, you realize this is the perfect time to create re-usable functions that abstract those 3 things:

🎉 Since you're a human you decide to name your baby 🍼: this "re-hacked" version of web dev shall be called `Rehact`.

Then, since you're still also a programmer, you pre-maturely add two wrapper objects in a way that feels right for reasons you can't explain: `Rehact` and `RehactDOM`.

 ```javascript
const Rehact = {
	createElement: (tagName, props, children) => {
		const protectedPropNames = {className: 'class'}
		const kebabCase = str => str.replace(/([a-z0-9]|(?=[A-Z]))([A-Z])/g, '$1-$2').toLowerCase()
		const cssifyObject = (object) => Object.keys(object).reduce((accumulator, prop) => `${kebabCase(prop)}: ${object[prop]}; `, '')
		const element = document.createElement(tagName)

		Object.keys(props || {}).forEach(
			propName => {
				const propValue = propName === 'style' ? cssifyObject(props[propName]) : props[propName]
				element.setAttribute(protectedPropNames[propName] || propName, propValue)
			}
		)

		children.forEach(child => {
			if (typeof(child) === 'string') {
				element.innerHTML += child
			} else {
				element.append(child)
			}
		})

		return element
	}
}


const RehactDOM = {
	render: (container, root) => root.append(container)
}
```

And here it is in action!

```javascript 
const secondsNumber = Rehact.createElement('span', {className: 'seconds-number'}, [(new Date()).getSeconds().toString()])
const secondsLabel = Rehact.createElement('span', {style: {fontStyle: 'italic'}}, [' seconds'])
const secondsContainer = Rehact.createElement('div', {className: 'seconds-container'}, [secondsNumber, secondsLabel])

RehactDOM.render(
	secondsContainer,
	document.querySelector('#rehact-root')
)
```

# You've abstracted away the ugly, repetitive parts of DOM creation. But can you get the re-usable, declarative feel of HTML?

// TODO: UPDATE THIS TO NEW, SIMPLER UI
For example, what if you want use `orchid` colored `h1` titles throughout your codebase? Could you create a simple function to encapsulate that code?

You can! So you decide to wrap `Rehact.createElement` in a tiny, simple functions you can re-use, and which can contain any logic specific to itself.


```javascript
const Text = (props, children) => Rehact.createElement('span', props, children)
const Container = (props, children) => Rehact.createElement('div', props, children)

RehactDOM.render(
	Container({className: 'seconds-container',}, [
		Text({className: 'seconds-number',}, [
			(new Date()).getSeconds().toString()
		]),
		Text({style: {fontStyle: 'italic'}}, [
			' seconds'
		])
	]),
	document.querySelector('#rehact-functions-root')
)
```

Then it strikes you: your JS indentation is reminding you of HTML's indentation. You're onto something! You've re-created the same nested structure of HTML -- but in dynamic JS!

## ... except it's super ugly and no one wants to use it.

Including your best friend and coding mentor Alejandra. (But, like, you're *basically* almost as good as her. Like, really really close.)

You: "Alejandra, I re-invented web dev! It's all JS now!"
Alejandra: "You mean you wrecked web dev. It's all ugly now."
You: "... uh, so what's the best way to send you the library? AIM?"
Alejandra: (turns and  types `$('.rehact').never()`)

## Forget Alejandra. She wasn't *that* cool anyway... (plus, you're basically almost as good as her)

But after blocking Alejandra on Myspace (and then un-blocking her to get some debugging help), you realize she was kind of onto something:

#### If the user interface sucks, your product fails.

That goes for websites, devices, and (it turns out) even programming libraries.

So you send Alejandra another message on Myspace:

You: "Thanks for the coding help. Also, how might I make my `Rehact` library prettier and easier to use?"
Alejandra: "Make it HTML"
You: "I hate you"
Alejandra: "Anytime"

## Forget Alejandra!!

Forget her, and forget her stupid code, and her little dog, too, and forget --

## ... wait, actually don't, that's brilliant! 💡

It's true: people *already* know and love HTML. So what if you let people just write HTML inside your `Rehact` functions, and then just ran a transpiler that converted it back to valid `Rehact` JS code?


// todo: SHOULD MABYE I GET INTERRUTED HERE? AND THEN SEE JSX ONLY WHEN I LOOK AT REACT?
## So you create a transpiler that converts any HTML written inside `Rehact` functions to plain `Rehact.createElement` JS, and call it `JSH` (JS + HTML)

(Then you realize XML is more general than HTML, so you rename it JSX.)

And *viola!* you've finally got the programming "user interface" everyone will love to interface with:


```html
stuff
```

Your chest swells. You start to tear up (a little).

Staring at what you've just created, you're looking upon the sunrise of a new era of web dev. At a new era of human progress, a new era of --

A notification pings from your phone. It's a message from Alejandra:

## "Please stop messaging me on Myspace. It's the 2020s, for gosh sakes get on a platform that's actually relevant."

You freeze. Your hands start to sweat (ok, who are we kidding, they were already sweating because you thought you'd just revolutionized web dev.)

You've been known to be absent-minded. But how did you accidentally miss *a decade of web dev evolution*? (And how could Myspace possibly ever stop being **the** hottest space on the web?!)

You take a deep breath. But surely, even in a decade, no one's thought of something as genius as `Rehact`: it's declarative, component-based, and easy to learn and use anywhere.

You start scanning the web for popular libraries, and one catches your eye: `React.js`? (Where is the 'h' and who came up with such dumb name?)

You open the homepage:

[screenshot of the 3 main ideas of react on the webpage]

For a moment you can't move.

Then you get yourself to scroll down and see this:

[JSX example]

*How did they... could it really be the case that...*

Before you can think more, you grab distribution links to React, ReactDOM, and JSX, throw it into your Rehact HTML file, delete the 'h' from `Rehact`, refresh your browser and...

### ... everything still works.

```javascript
const Text = (props, children) => React.createElement('span', props, children)
const Container = (props, children) => React.createElement('div', props, children)

ReactDOM.render(
	Container({className: 'seconds-container',}, [
		Text({className: 'seconds-number',}, [
			(new Date()).getSeconds().toString()
		]),
		Text({style: {fontStyle: 'italic'}}, [
			' seconds'
		])
	]),
	document.querySelector('#react-root')
)
```

You shudder. Then you smile.

"Well," you chuckle to yourself, "I guess I accidentally invented React.js..."

An email notification chimes. You check your phone. Alejandra's inviting you to some platform called "Facebook." You scoff and put the phone down.

Forget "Facebook." Who needs "Facebook" when you've got React.js?

****

This post is a distillation of a talk I gave at an Inland Empire Software Development meetup.

It was directly inspired by this talk, and informed by this talk by the creator of React (no, not Dan Abramov, silly)
