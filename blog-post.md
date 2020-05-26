It's the 2020s, and React.js is the most popular frontend framework. Everyone's using it. Everyone's hiring for it.

*And everyone has no idea how it really works.*

But not you. Why? Because, way back in 2010, you accidentally invented React...

<sub>[Everything that follows is real code. Play with it here!](https://github.com/davidnmora/oops-you-invented-react)</sub>

******

## It's 2010...
Bieber is in full swing, you definitely don't have a crush on your friend Alejandra, and web dev looks like this:

```html
<div id="root">
	<div class="seconds-container">
		<span class="seconds-number"></span>
		<span style="font-style: italic">seconds</span>
	</div>
</div>

<script>
	const secondsNumber = document.querySelector('#root .seconds-container .seconds-number')
	secondsNumber.innerHTML = (new Date()).getSeconds().toString()
</script>

```
#### Here's what you ❤️ about it:
1. **HTML** is super declarative: it shows you exactly the structure of the page.
2. **JS** is event-driven & composable. You can update stuff on the fly.

#### And here's what sucks about it: 😤
1. **HTML** is static. And repetitive. Want 20 images? Get ready to copy and paste. Want to update them dynamically based on data? No can do. *Ahh, but isn't that where JS comes into play?* Sure, but it sucks...
2. Writing & executing **JS** feels like being a surgeon who blindly reaches into her HTML patient's body, slices stuff up, and hopes it works.


## Then you have an 💡: let's do everything in JS!

*But can we create HTML elements with only JS?* 

We can!

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


const root = document.querySelector('#root')
root.append(secondsContainer)
```

Turns out your 💡 wasn't so great. 😥

Then you squint at your code and something hits you -- you're doing 3 things over and over again:
1. creating DOM element of a certain type
2. setting its properties
3. appending it to a parent DOM element

So let's create a little library that abstract those 3 things!

What to call it? This is a "re-hacked" version of web dev, so how about `Rehact.js`?

You suspect this library might get huge, so you preemptively split the library in two: `Rehact` for element creation and `RehactDOM` for hooking into the existing DOM:

 ```javascript
const Rehact = {
	createElement: (tagName, props, ...children) => {
		const protectedPropNames = {className: 'class'}
		const kebabifyCase = str => str.replace(/([a-z0-9]|(?=[A-Z]))([A-Z])/g, '$1-$2').toLowerCase()
		const cssifyObject = (object) => Object.keys(object).reduce((accumulator, prop) => `${kebabifyCase(prop)}: ${object[prop]}; `, '')
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

And *my!* isn't it easier on the eyes...

```javascript 
const secondsNumber = Rehact.createElement('span', {className: 'seconds-number'}, [(new Date()).getSeconds().toString()])
const secondsLabel = Rehact.createElement('span', {style: {fontStyle: 'italic'}}, [' seconds'])
const secondsContainer = Rehact.createElement('div', {className: 'seconds-container'}, [secondsNumber, secondsLabel])

RehactDOM.render(
	secondsContainer,
	document.querySelector('#root')
)
```

# Great, you've abstracted away the repetitive details of DOM creation. But can you get the re-usable, declarative feel of HTML?

For example, what if you wanted to use a standard `SecondsContainer` abstraction throughout your code base?

You decide to wrap `Rehact.createElement` in a simple functions you can re-use:


```javascript
const Text = (props, ...children) => Rehact.createElement('span', props, ...children)
const Container = (props, ...children) => Rehact.createElement('div', props, ...children)

RehactDOM.render(
	Container({className: 'seconds-container',},
		Text({className: 'seconds-number',},
			(new Date()).getSeconds().toString()
		),
		Text({style: {fontStyle: 'italic'}},
			' seconds'
		)
	),
	document.querySelector('#root')
)
```

👀 You look a little closer: your JS indentation is now reminding you of the original HTML in now reproduces. The `Container` function wraps its two indented `Text` children, just like: 

```html
<div class="seconds-container">
	<span class="seconds-number"></span>
	<span style="font-style: italic">seconds</span>
</div>
```

The spriti of HTML now lives in JS! 😁 ✨

## ... except it's still super ugly and no one wants to use it.

Including your best friend and coding mentor Alejandra.

**You**: "Alejandra, I re-invented web dev! It's all JS now!"

**Alejandra**: "You mean you wrecked web dev. It's all ugly now."

**You**: "... uh, so what's the best way to send you the Rehact library? your hotmail?"

**Alejandra**: `$('.rehact').forgetIt()`


## Forget Alejandra. She wasn't *that* cool anyway... (plus, you definitely never had a crush on her)

But after blocking Alejandra on Myspace (and then un-blocking her to get some debugging help), you realize she was onto something:

#### If the user interface sucks, your product fails.

That goes for websites, devices, and (it turns out) programming libraries.

So you send Alejandra another message:

**You**: "I get that Rehact is a tangle of braces and parens. But it's powerful. How might I make it more enjoyable to code with?"
**Alejandra**: "Make it HTML"
**You**: "I hate you"
**Alejandra**: "Anytime"

## Forget Alejandra!!

😤

## 🤔 ... wait, actually don't, that's brilliant! 💡

It's true: people *already* know and love HTML. And Rehact is largely just a JS-flavored way of specifying HTML. 

*So what if you let people just write HTML inside your `Rehact` functions*, and then just transpiled it back to valid `Rehact` JS code for execution?

Not only could you let people write HTML elements like `div` or `h2`, but you could also let people represent `Rehact` functions as if they were HTML, eg re-writing `Container({className: 'container'})` as `<Container class="container" />`.

You could call the transpiler `JSH`: JS + HTML. (Or maybe `JSX`, for JS + XML.)

It's a programming "user interface" that'll make `Rehact` a joy to adopt!

But before you can begin on the `JSX` transpiler, you get a message from Alejandra:

## "Oh, and please stop messaging me on Myspace. It's the 2020s, for gosh sakes get on a platform that's actually relevant."

You freeze.

You've been known to be absent-minded, but how did you accidentally miss *a decade of web dev evolution*?

But surely, even in a decade, no one's thought of something as genius as `Rehact`: it's declarative, component-based, and easy to learn once and use anywhere.

Scanning the web for popular libraries, `React.js` catches your eye.

You open the homepage:

![image](https://user-images.githubusercontent.com/6570507/82911380-af7a0200-9f20-11ea-9095-3da44ce0ab42.png)

Then you scroll down and see:

![image](https://user-images.githubusercontent.com/6570507/82912345-d8e75d80-9f21-11ea-9a0f-7ff1fea45653.png)

you toggle off `JSX` and to your amazement find `React.createElement()` transpiled underneath!

![image](https://user-images.githubusercontent.com/6570507/82921169-eeae5000-9f2c-11ea-95e6-420f66a03b9c.png)

Your head is spinning. You grab distribution links to React, ReactDOM, and JSX, throw it into your Rehact HTML file, delete the 'h' from `Rehact`, refresh your browser and...

### ... everything still works.

```javascript
const Text = (props, ...children) => React.createElement('span', props, ...children)
const Container = (props, ...children) => React.createElement('div', props, ...children)

ReactDOM.render(
	Container({className: 'seconds-container',},
		Text({className: 'seconds-number',},
			(new Date()).getSeconds().toString()
		),
		Text({style: {fontStyle: 'italic'}},
			' seconds'
		)
	),
	document.querySelector('#root')
)
```

## ... even the `JSX` "user interface":

```javascript
const Text = (props) => <span {...props}>{props.children}</span>
const Container = (props) => <div {...props}>{props.children}</div>

ReactDOM.render(
	<Container className="seconds-container">
		<Text className="seconds-number">{(new Date()).getSeconds().toString()}</Text>
		<Text style={{fontStyle: 'italic'}}>{' seconds'}</Text>
	</Container>,
	document.querySelector('#root')
)
````

You lean back in your chair and smile.

"Oops," you chuckle, "I guess I accidentally invented React.js..."

An email notification chimes. Alejandra's inviting you to some platform called "Facebook." You scoff.

Who needs "Facebook" when you've got `React.js`?

****

This post is a distillation of [a talk I gave at an Inland Empire Software Development meetup](https://youtu.be/3MXrYVqYklg?t=37) .

The code was directly inspired by [Kent C. Dodds' talk, "The introduction to React you've been missing,"](https://youtu.be/SAIdyBFHfVU) and the story was based loosely off [this account by React's creator](https://youtu.be/5fG_lyNuEAw?t=702) (no, not Dan Abramov, silly)

Please note this article is meant as **an incomplete, rough introduction to React's origin**. While [all the code really works](https://github.com/davidnmora/oops-you-invented-react), it completely skips over many things, most glaringly React's "virtual DOM."

However, omitting class-based components *was* intentional. Let's please forget those ever existed. 😉

