# Why the Concur Model

Taken from my answer to [this question](https://github.com/jviide/flowponent/issues/2)

## Qs. Why the Concur model?

Not a negative sentiment, but legitimately curious.

I checked out the Concur repos and docs + samples to try to get a better understanding of this, but at first-glance it seems like additional complexity that could be handled with plain logical statements in the render method?

If it is not too much trouble, could you explain a bit why using this type of flow is different or better than having a view that gets injected with stateful data and re-renders normally?

## Ans.

The answer I usually give for Concur is that this flow based paradigm is simpler *and* scales better to larger programs. This is usually because our mental model fits synchronous control flow, and free composition of widgets better.

### It's really simple for simple real world code

Think of when a person first learns to program, and writes something beyond plain hello world, which was output only, to something that is interactive.

Say, accept a user's name and then say hello to them. They probably would intuitively write something like this, assuming appropriate `getInput` and `log` functions -

```purescript
name <- getInput "What is your name"
log "Hello "+name
```

This is simple, and works. Hey programming is easy!

But as soon as they start thinking of even slightly larger programs, they have to change their mental model completely. They have to deal with asynchronous behaviour (event handlers), and different input/output modes (interacting with the DOM instead of alert/prompt), and code architecture issues (how do you build a larger program from a smaller one without rewriting completely).

Imagine if writing a simple hello world involved something like this from the get go (In javascript now) -

```javascript
setInitialState({name: null})
onRender() {
  let {name} = getState()
  if(name == null) {
    prompt("What is your name",
      userInput => setState({name: userInput})
    )
  } else {
    alert("Hello "+name)
  }
}
```

It's pretty crummy and hard to understand for new programmers, and hides the actual logic behind a lot of cruft.

Concur/Flowponent lets you have the original easy model or programming, without losing anything.

```purescript
name <- textInputEnter "What is your name?" true []
D.text $ "Hello " + name
```

This code is asynchronous with event handlers, and can output to the dom (`textInputEnter` and `D.div` are a part of Purescript-Concur-React).

Also the logic doesn't rely on a magic `render` method which is called automatically just when you need it to be called (i.e. state changes). Instead, the rendering is explicitly controlled by the program (even if behind the scenes it uses the original render method). I feel it greatly improves comprehension.

**AND it scales linearly with program complexity.**

Take a very simple extension to the previous program. Think about how the code could be modified to handle a list of users. All of them need to be asked their names and greeted. A new programmer would probably think that it would look something like this (In Javascript) -

```javascript
for(user of users) {
  let name = prompt("What is your name")
  alert("Hello "+name)
}
```

It's simple, it's clear, and if it worked it would have had no bugs.

But no. We can't actually write it like that. It takes a great mental leap to get to the state based async model for this functionality.

And because there is so much cruft, there are lots of ways to write this cruft. There isn't one good canonical way. Eventually you have to write something like this. It may have bugs since I just wrote it inline just now (Again in Javascript) -

```javascript
// Don't modify the original users array. Assumes a clone method.
setInitialState({usersLeft: users.clone(), name:null})
onRender() {
  let {usersLeft, name} = getState()
  if(name != null) {
    alert("Hello "+name)
  }
  // No else
  let user = usersLeft.pop()
  if(user != undefined) {
    prompt("What is your name",
      // Don't forget to set the modified users array
      userInput => setState({usersLeft: usersLeft, name: userInput})
    )
  }
}
```

Whereas, Concur lets you keep the simple model with no drawbacks. -

In Purescript this is simply -

```purescript
sequence $ repeat 10 $ do
  name <- textInputEnter "What is your name?" true []
  D.text $ "Hello " + name
```

And even in Javscript it's easy (Using a strange list syntax here because I didn't want to use DOM elements, but hopefully the concept is clear) -

```javascript
name = null
for(user of users) {
  name = yield step =>
     [ if(name != null) alert("Hello "+name)
     , prompt("What is your name", step)
    ]
}
```

Looks like programming IS really easy!
