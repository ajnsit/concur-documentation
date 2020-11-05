# Input Output

Concur provides seamless IO at widget boundaries. This is another area where other frameworks falter. Since concurrency is baked into and integral to Concur, IO becomes predictable and powerful. Concur provides lifting functions to lift IO into widgets, and those lifted io widgets can be easily composed just like any other normal widget.

Continuing our previous example, if we want to print the greeting selected by the user to the console.

```purescript
helloWidget :: forall a. Widget HTML a
helloWidget = do
  greeting <- div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
  liftEffect (log ("You chose to say " <> greeting))
  text (greeting <> " Sailor!")
```

Here we use `liftEffect` to convert an effect into a widget. In Haskell, we can use `liftIO`.

Lifting IO to Widgets means we can compose IO in parallel with other Widgets. This can lead to some nice patterns. Two examples -

1. Allow cancelling long running IO actions in response to GUI events, without any boilerplate. Let's say we want to create a timer which can be cancelled. The following widget will return when the timer completes after a specified duration, or when the button is pressed, whichever is first. Whenver the button is pressed, the timer thread is automatically cancelled so you don't have to do that manually.

```purescript
timerWidget ms = liftAff (delay (Milliseconds ms)) <|> button [unit <$ onClick] [text "Cancel"]
```

2. Show a "Loading" screen while performing IO, let's say while making an ajax call.

```purescript
resp <- liftAff (AX.get ResponseFormat.json url) <|> text "Fetching posts from subreddit..."
```

Also note that the ability to perform IO actions from Widgets is very handy when creating bindings to existing native/JS libraries.
