# Plan for Composability by Breaking Down Components

Concur has a very simple and consistent Widget model. However there are a few situations where the simplicity of the model leads you down the wrong path in terms of code organisation.

The most common of such situations is when you have a specification for a widget, and you build it in a straightforward manner using Concur's monadic flow with a recursive call at the end. You don't need to create a data structure to represent the internal state of the widget.

Taking the example of the greeting selector we built earlier -

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

Note that the type of the widget is `forall a. Widget HTML a`. This means that the Widget is entirely self contained, which is a good thing for usability, but also bad in terms of composability. You are not able to combine this widget with other widgets in meaningful ways, and other widgets on the page cannot depend on the actual greeting selected. **Note: Actually, it often makes sense to compose never-ending widgets. Look at the next section on "Never-ending Components to see how.**

*The idiomatic and reusable Concur widget does one thing and returns a value*. So to make this reusable, we need to remove the recursive call at the end, and instead return the greeting selected.

```purescript
getGreeting :: Widget HTML String
getGreeting = div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
```

And then having a separate widget which consumes that greeting, and allows the user to exit.

```purescript
showGreeting :: String -> Widget HTML Unit
showGreeting greeting = div'
  [ text (greeting <> " Sailor!")
  , void $ button [onClick] [text "restart"]
  ]
```

And then compose them together when needed.

```purescript
hello :: forall a. Widget HTML a
hello = do
  greeting <- getGreeting
  showGreeting greeting
  hello
```

Now the composition logic and the recursive call is isolated from the rest of the UI code, and can be easily changed if needed.

For example, if the requirement changes to also always display the previously selected greeting, then you can do that without changing `getGreeting`, and `showGreeting`, and making only minimal changes to `hello`. We need to take the previous greeting as a parameter, and then loop on the new greeting when one has been selected.

```purescript
hello :: String -> forall a. Widget HTML a
hello prev = hello =<< div'
  [ text ("Previous greeting - " <> prev)
  , do
      greeting <- getGreeting
      showGreeting greeting
      pure greeting
  ]

helloWithPrev :: forall a. Widget HTML a
helloWithPrev = hello ""
```
