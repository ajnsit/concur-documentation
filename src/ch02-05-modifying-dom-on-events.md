# Modifying DOM on Events

What if we want to modify the original button, instead of replacing it entirely? Surely that would require having some sort of "architecture" or design pattern in place? As it happens to be, even in that case we don't need a separate workflow. Instead, we exploit a little trick of virtual dom, and replace the original button with a conceptually different button, and Concur takes care of updating the button efficiently instead of replacing it entirely.

```purescript
hello :: forall a. Widget HTML a
hello = do
  void $ button [onClick] [text "Say Hello"]
  button' [text "Hello Sailor!"]
```

What if you wanted to do different things based on the user input itself. For example, you want to show a different greeting depending on which button was pressed.

```purescript
hello :: forall a. Widget HTML a
hello = do
  greeting <- div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
  text (greeting <> " Sailor!")
```

You can see that layout and event handling seemlessly combine in Concur. We use `<$` operator to assign a return value to each of the buttons. Since the buttons are composed in space using the HTML DSL, they are both shown together. Then the return value of the combined widget is the return value of whichever button is clicked. We then fetch the return value (i.e. the greeting), and display it as a part of our text message.

Everything works predictably, and using the tools you already have.

I must mention that it's quite possible to compose widgets together without wrapping them in a div. Use the operator `<|>` to accomplish that. For example, composing two buttons directly -

```purescript
hello :: forall a. Widget HTML a
hello =
  button' [text "Ahoy Port!"]
    <|>
  button' [text "Ahoy Starboard!"]
```

You can also use `orr` which composes any number of widgets in a list.

```purescript
hello :: forall a. Widget HTML a
hello = orr
  [ button' [text "Ahoy Port!"]
  , button' [text "Ahoy Starboard!"]
  , button' [text "One more button!"]
  ]
```

Indeed, both `<|>` and `div` (and all other HTML DSL wrappers) are implemented in terms of `orr`.
