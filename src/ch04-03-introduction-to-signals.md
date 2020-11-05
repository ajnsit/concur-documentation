# Converting Display Widgets

Building Signals from "Display Widgets" i.e. never ending widgets is even easier. Here we can use a function called `display`. It's a convenience function provides the initial value for `step` when its type has only one possible inhabitant (i.e. `Unit`). It is defined like this -

```purescript
display :: Widget HTML (Signal HTML Unit) -> Signal HTML Unit
display = step unit
```

Note that you don't need to worry about the initial value of the signal here. Never ending widgets have a polymorphic return type `forall a. a`, so they can be passed to `display` without issues (the polymorphic `a` is just resolved to a `Signal HTML Unit` by the type checker).

An example of its usage -

```purescript
textSignal :: Signal HTML Unit
textSignal = display (text "Hello Sailor!")
```
