# Event Handling

In the previous examples, clicking the buttons does nothing. After all, we haven't attached an event handler to the button! Traditionally, responding to user input seems to be the place where complexity creeps into the application. In Elm, for example, this is the place where you would reach out for the *Elm Architecture*.

Handling external events is where Concur shines. It does away with all the incidental complexity and lets you focus on the program logic. I'll make a claim that handling generic user events in Concur is simpler than any other library/framework in any programming language ever. If you know of anything else simpler, please let me know.

In Concur, you can just attach tags to the widgets to indicate the events you wish to handle, and then handle them synchronously in the program flow. Concur takes care of all the plumbing and concurrency for you. This is part of where Concur's name comes from - its support for Concurrency. The other reason for Concur's name is due to what I call aliasing of types - where adjacent types and properties must agree (concur) before they can be composed, which leads to increased type safety and powerful composition primitives. We'll discuss concurrency and composition primitives later in this guide.

Let's say we want to show a greeting to the user when the button is is clicked. We can do so very easily, by attaching an `onClick` attribute to the button. We have to use `button` instead of `button'`. (`button'` is defined simply as `button []` for convenience).

```purescript
hello :: forall a. Widget HTML a
hello = do
  void $ button [onClick] [text "Say Hello"]
  text "Hello Sailor!"
```

That's right, `text` is also a complete widget on its own, which we are composing with the `button` widget. Here we use do-notation to compose two widgets *in time*. The text widget never runs at the same time as the button, and only shows up when, and as soon as, the button is clicked.

The final effect is that we see a button with the text "Say Hello", which when clicked gets replaced by text "Hello Sailor!". Ain't that easy!

Again, pay attention to the final type of the widget. It's the same as before (`forall a. Widget HTML a`) even though this time we *did* attach an event handler. However, we handle the event entirely internally, and simply change to a static widget (`text`) in response. So from the perspective of the rest of the program, the widget never returns.

### A digression on the type of a plain button widget

So what *is* the type when you don't handle the button click event internally? To be specific, what is the type of the following widget -

```purescript
button [onClick] [text "Say Hello"]
```

The type is `Widget HTML SyntheticMouseEvent`, where the `SyntheticMouseEvent` is the type synonym for a record that basically maps a javascript mouse event.

In Concur, the standard pattern is `Widget a = someDomElementWrapper [Prop a, Prop a, ...] [Widget a, Widget a,...]`. i.e. to get the return type of a Widget, you just need to look at the return type of any of the props passed to it, or the return type of any child widgets passed to it. In Concur, the types of the props, the children, and the parent widget itself must agree (or... concur, pun intended).

So the return type of `button [onClick] [text "Say Hello"]` is necessarily the same as the return type of `onClick` which is defined in `Concur.React.Props`. Which you will find is `SyntheticMouseEvent`.

If you try to check the type yourself in the purescript repl with :t, you would see the properties you can access on the event object -

```
> :t button [onClick] [text "Say Hello"]
Widget (Array ReactElement)
  (SyntheticEvent_
     ( altKey :: Boolean
     , button :: Number
     , buttons :: Number
     , clientX :: Number
     , clientY :: Number
     , ctrlKey :: Boolean
     , getModifierState :: String -> Boolean
     , metaKey :: Boolean
     , pageX :: Number
     , pageY :: Number
     , relatedTarget :: NativeEventTarget
     , screenX :: Number
     , screenY :: Number
     , shiftKey :: Boolean
     , detail :: Number
     , view :: NativeAbstractView
     , bubbles :: Boolean
     , cancelable :: Boolean
     , currentTarget :: NativeEventTarget
     , defaultPrevented :: Boolean
     , eventPhase :: Number
     , isTrusted :: Boolean
     , nativeEvent :: NativeEvent
     , target :: NativeEventTarget
     , timeStamp :: Number
     , type :: String
     )
  )
```

Here the `Array ReactElement` is the same as `HTML`, and everything after that is the mouse event.
