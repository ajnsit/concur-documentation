# Your First Signal

So how do we build signals? The workhorse for that is the function `step`. It is dual to `dyn`, in that it allows creating a Signal from a Widget.

```purescript
step :: forall v a. a -> Widget v (Signal v a) -> Signal v a
```

Each Signal always has a value associated with it. It's a [Comonad](https://pursuit.purescript.org/packages/purescript-control/docs/Control.Comonad#t:Comonad), so the current value of the Signal can be extracted with `extract`.

Step takes the initial value of the signal as its first argument. The second argument is the Widget. This way of creating Signals requires recursion to be implemented in continuation passing style. The Widget passed in needs to perform some work, and then return the continuation Signal.

Let's rewrite our hello example with Signals -

```purescript
hello :: String -> Signal HTML String
hello s = step s do
  greeting <- div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
  text (greeting <> " Sailor!") <|> button [onClick] [text "restart"]
  pure (hello greeting)
```

It's almost the same as the straightforward Widget version! The only things that changed are that we had to specify the initial value of the greeting (by passing it as an argument to `step`), and we had to change the explicit recursion to CPS by returning `hello` instead of directly calling `hello`.

Now we can use it with `dyn` -

```purescript
helloWidget :: forall a. Widget HTML a
helloWidget = dyn $ hello ""
```
