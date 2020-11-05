# Anatomy of a Widget

Concur is built around composing `Widget`s. A Widget is something that has a `View` i.e. a User-Interface, can change in response to some events, and finally return some value.

The type Widget is parameterised by the type of the View, and the return type. That is to say - a Concur Widget has the type `Widget HTML a`. Here `HTML` is the type of the view (i.e. client side apps), and `a` is the type parameter that denotes *what gets returned* when the Widget is *done*.

```purescript
-- A Widget with a View of type HTML (i.e. client side web apps), and returning an Int
foo :: Widget HTML Int
```

Especially note that the type of Events handled by the Widget is not a part of its type signature. Nor does the type signature include the type of the `Action`s performed by the Widget. This is important because each Widget in Concur is self sufficient and can be populated on the page without worrying about the inputs and outputs. All the inputs and outputs are wired at the time of Widget definition, and the user does not need to bother with them at the time of usage.

#### Hello World

Here's a simple widget that displays a button with the text "Hello Sailor!" in Concur -

```purescript
hello :: forall a. Widget HTML a
hello = button' [text "Hello Sailor!"]
```

Concur provides a very simple DSL for constructing HTML DOM elements. This widget is a button with some text inside it.
