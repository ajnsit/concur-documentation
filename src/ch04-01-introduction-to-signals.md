# Introduction to Signals

To see why we need Signals, let's reconsider the greeting selector example from the previous section. We need to display a greeting selector, and then greet the user with that selected greeting, and finally allow the user to restart the cycle.

The most straightforward way of writing this is directly in one go using monadic recursion -

```purescript
hello :: forall a. Widget HTML a
hello = do
  greeting <- div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
  text (greeting <> " Sailor!") <|> button [onClick] [text "restart"]
  hello
```

As we discussed in the previous section, to make this widget composable and maintainable, we should separate the recursive call from the rest of the code. However there are two things that are unsatisfactory about that -

1. The most straightforward way to write something is not the idiomatic way to write something. We have lost a property that Concur prides itself on.
2. The complete behaviour of the widget includes the UI *and* the recursion. It feels unsatisfactory to *have to* separate the two in order to be able to compose things.

**Enter Signals.**

In essence, A Signal is a recursive / never ending widget which can still be composed seamlessly. Like a Widget, a Signal is also parameterised on the View, and the return value. A Signal is of type `Signal v a`, where `v` is the type of the view, and `a` is the return value. However, a signal does not "end" after returning a value, but is instead free to keep processing. Apart from being able to compose with other Signals, a Signal is conceptually equivalent to a never-ending Widget `forall a. Widget HTML a`.

So we have a function called `dyn` which can take a Signal and converts it to a never-ending Widget so it can be displayed.

```purescript
dyn :: forall v a b. Signal v a -> Widget v b
```

Here we ignore the return value of the Widget, since we can't make use of it in Widget space, and return a widget with an indeterminate value `b`, i.e. the Widget is never-ending.
