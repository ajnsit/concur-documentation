# Jumping Into the Middle of Widgets

Taken from my answer to [this question](https://github.com/jviide/flowponent/issues/2)

## Qs. Jump into the middle of widgets?

One downside that may be observed in practice in that generators can’t be jumped into.

e.g for testing a particular state in a test, need to go through the previous parts of the functions to get there. Or for hot-module-reloading with fast dev reloads. The components can’t be re-hydrated from a previous state.

Though this is purely speculative, certainly not a reason to avoid this approach as the benefits do look good 🙂

## Ans.

**(All code snippets in this answer are in Javascript).**

Hmm, I think you'd find that the flow model is pretty flexible. In particular, it's strictly more powerful than React/Redux/Elm.

#### For example, you can simply define your own redux in 3 lines -

```javascript
function redux(state, render, update) {
  let action = yield* render(state)
  let newState = update(state, action)
  yield* redux(newState, render, update)
}
```

Then you can use it in the usual stateful way! Everything below is business logic -

```javascript
// Initial state (the name of the person)
// To hydrate, you only need to restore this state
let state = null

// Define a render function
// Allow the user to change the name to John
let render = name => step =>
     [ if(name != null) alert("Hello "+name)
     , prompt("Should I call you John instead?", step)
    ]

// Update function
// Change the state (name) to John if the user requested it
let update = response => name =>
  response=="yes"? "john" : name

// Wire everything together!
redux(name, render, update);
```

#### You can also use checkpoints to store the implicit "state".

A checkpoint is defined as a specific series of return values encountered in a widget.

Define a generic `checkpoint` function that can be used in place of `yield`.

```javascript
// This is our implicit state
// To hydrate, you only need to restore this state
let checkpoints = []
// How much state has been restored?
let checkpointIndex = 0

// Yield with checkpointing
function checkpoint(view) {
  let ret = checkpoints[checkpointIndex]
  if(ret !== null) {
    checkpointIndex++
    return ret
  }

  let ret = yield view
  checkpoints.push(ret)
  return ret
}
```

Now write code normally, and you have checkpointing! For example, the users array code from earlier -

```javascript
name = null
for(user of users) {
  name = yield* checkpoint(step =>
     [ if(name != null) alert("Hello "+name)
     , prompt("What is your name", step)
    ])
}
```
