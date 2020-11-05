# Signal Composition

Signals mimic traditional FRP by offering Monadic composition. It's always composition *in space* since Signals don't have a lifecycle, and run forever (until removed entirely from the page). That is to say that Signals don't have (or need) composition *in time*.

Need to display a list of greeting widgets on the page at the same time?

```purescript
helloList :: Array String -> Signal HTML (Array String)
helloList = traverse hello
```

The Signal will show all the individual widgets at the same time, and allow editing them at the same time. Note that we did not have to manage the recursion for individual signals when composing them.

What if we want to also display the currently selected greetings for all the hello widgets together at the top level?

```purescript
helloListWithDisplay :: Array String -> Signal HTML (Array String)
helloListWithDisplay prev = div_ [] do
  traverse hello prev
  display (text ("Previously selected greetings - " <> show prev))
```

Note that we were able to use the same HTML DSL to wrap signals in DOM elements. `div_` is the same as `div`, but requires only a single child element.

To understand how the composition works, think of monadic composition of signals like a waterfall. When you perform Signal `foo` and then Signal `bar`, both `foo` and `bar` are performed continuously. However, every time the upstream Signal `foo` emits a value, the value of downstream `bar` is recomputed.

This leads to the question of layout. Signal views are displayed in the same order as the monadic composition. However, we might require a value from a later Signal to compute the value of an earlier signal. This is the classic problem with mixing monadic composition with layout, and is faced by all current FRP libraries. Concur Signals have a very simple solution for this - Loops.

There is an operator called `loopS` provided which loops back the return value of a signal to the beginning. This is like `MonadFix` but less magical. So you can do this -

```purescript
helloListWithDisplay :: Signal HTML (Array String)
helloListWithDisplay prev = loopS prev \prev' -> div_ [] do
  display (text ("Previously selected greetings - " <> show prev'))
  traverse hello prev'
```
