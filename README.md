<h1 align="center">
  Concur Documentation
</h1>
<p align="center">
   <img src="logo.png" height="100">
</p>
<p align="center">
  <a href="https://gitter.im/concurhaskell" rel="nofollow">
      <img src="https://camo.githubusercontent.com/9fb4e2dde684214e7454d930a369f97190d1ecf2/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f6769747465722d6a6f696e253230636861742532302545322538362541332d626c75652e737667" alt="Join the chat at https://gitter.im/concurhaskell" data-canonical-src="https://img.shields.io/badge/gitter-join%20chat%20%E2%86%A3-blue.svg" style="max-width:100%;">
   </a>
   <a href="https://www.reddit.com/r/concurhaskell/" rel="nofollow">
      <img src="https://img.shields.io/badge/reddit-join%20the%20discussion%20%E2%86%A3-1158c2.svg" alt="Join the chat at https://gitter.im/concurhaskell" style="max-width:100%;">
   </a>
</p>

## Introduction

**Concur** is a UI development framework for functional languages like Haskell and Purescript. It is primarily used for client side web development, but there are efforts underway to port it to other platforms such as those for native desktop and mobile applications.

1. For learners - Concur focuses on **ease of development without sacrificing power**. Simplicity is the number one goal. Concur provides a consistent set of tools which can be combined in predictable ways to accomplish any level of functionality ranging from the simplest hello world example to the most complex frontend for a web app. Due to its extremely gentle learning curve, Concur is well suited for learners of functional programming (replacing console applications for learners).

2. For experienced folks - Assuming you are already familiar with functional programming, Concur will provide a satisfying development experience. Concur does not artificially constrain you in any form. You are encouraged to use your FP bag of tricks in predictable ways, and that is the standard way of doing things so you are never going against the grain. It's a *library* in spirit, rather than a framework.

3. For professional development - Concur scales linearly with program complexity. Simple things are easy, complex things are just as complex as the problem itself, no more. Reusing existing widgets, and refactoring existing code is extremely easy. Concur is a great choice to build your next enterprise application on.

4. Multiplatform development - Concur works with Haskell and Purescript. It provides a React backend, and a virtual-dom backend, with other native backends in the works. It provides great support for integrating existing React widgets. Note that Concur code in all those platforms looks (or will look) exactly the same!

## Installation

Look at the individual pages for [Concur (Haskell)](https://github.com/ajnsit/concur), or [Purescript-Concur](https://github.com/ajnsit/purescript-concur) for installation instructions.

## A Quick Tour of Concur

** To simplify things, all the following examples will be in Purescript, using the React backend **

#### Hello World

Here's a simple widget that displays a button with the text "Hello Sailor!" in Concur -

```purescript
helloWidget = button' [text "Hello Sailor!"]
```

Concur provides a very simple DSL for constructing HTML DOM elements. The resulting widget is self contained and can be easily populated on the page (within a div with the id "hello") -

```purescript
main = runWidgetInDom "hello" helloWidget
```

Widgets can be easily laid out on the page using the HTML DSL. So you can create a layout with two buttons side by side like so -

```purescript
div'
  [ button' [text "Ahoy Port!"]
  , button' [text "Ahoy Starboard!"]
  ]

```

Or one above the other (using div's to separate them) -

```purescript
div'
  [ div'
      [ button' [text "Ahoy Port!"] ]
  , div'
      [ button' [text "Ahoy Starboard!"] ]
  ]

```

Layout is composition *in space*. The two button widgets occupy different parts of the page, depending on the layout, but run together *in time*. Under the hood, composition in space is equivalent to Monoidal composition. The key characteristic of monoidal composition is that the type of the combined widget is the same as the type of the individual widgets. So the entire widget has the same type as that of a single button widget, and can be placed anywhere a single button can be.

We'll further discuss layout later in this guide.

#### Event Handling

In the previous example, clicking the buttons does nothing. After all, we haven't attached an event handler to the button! Traditionally, responding to use input seems to be the place where complexity creeps into the application. In Elm, for example, this is the place where you would reach out for the *Elm Architecture*.

Handling external events is where Concur shines. It does away with all the incidental complexity and lets you focus on the program logic. I'll make a claim that handling generic user events in Concur is simpler than any other library/framework in any programming language ever. If you know of anything else simpler, please let me know.

In Concur, you can just attach tags to the widgets to indicate the events you wish to handle, and then handle them synchronously in the program flow. Concur takes care of all the plumbing and concurrency for you. This is part of where Concur's name comes from - its support for Concurrency. The other reason for Concur's name is due to what I call aliasing of types - where adjascent types and properties must agree (concur) before they can be composed, which leads to increased type safety and powerful composition primitives. We'll discuss concurrency and composition primitives later in this guide.

Let's say we want to show a greeting to the user when the button is is clicked. We can do so very easily, by attaching an `onClick` attribute to the button. We have to use `button` instead of `button'`. (`button'` is defined simply as `button []` for convenience).

```purescript
helloWidget = do
  button [onClick] [text "Say Hello"]
  text "Hello Sailor!"
```

That's right, `text` is also a complete widget on its own, which we are composing with the `button` widget. Here we use do-notation to compose two widgets *in time*. The text widget never runs at the same time as the button, and only shows up when, and as soon as, the button is clicked.

The final effect is that we see a button with the text "Say Hello", which when clicked gets replaced by text "Hello Sailor!". Ain't that easy!

What if we want to modify the original button, instead of replacing it entirely? Surely that would require having some sort of "architecture" or design pattern in place? As it happens to be, even in that case we don't need a separate workflow. Instead, we exploit a little trick of virtual dom, and replace the original button with a conceptually different button, and Concur takes care of updating the button efficiently instead of replacing it entirely.

```purescript
helloWidget = do
  button [onClick] [text "Say Hello"]
  button' [text "Hello Sailor!"]
```

What if you wanted to do different things based on the user input itself. For example, you want to show a different greeting depending on which button was pressed.

```purescript
helloWidget = do
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
button' [text "Ahoy Port!"]
  <|>
button' [text "Ahoy Starboard!"]
```

You can also use `orr` which composes any number of widgets in a list.

```purescript
orr
  [ button' [text "Ahoy Port!"]
  , button' [text "Ahoy Starboard!"]
  , button' [text "One more button!"]
  ]
```

Indeed, both `<|>` and `div` (and all other HTML DSL wrappers) are implemented in terms of `orr`.

#### Handling Multiple Events

You can have mutliple event handlers on the same widget easily.

Here's an example of an input element with both change and focus handlers.

```purescript
data Action = Changed String | Focused
inputWidget = input [Changed <$> onChange, Focused <$ onFocus] []
```

Here `inputWidget` will return an `Action`, whenever the text is changed, or when it receives focus.

You don't need an action data type if you decide to process the action in line. For example, if you maintain a state -

```purescript
type State = {focusCount:: Int, currentText :: String}
inputWidget = input [st {focusCount = st.focusCount+1} <$ onFocus, (\s -> st {currentText = s}) <$> onChange] []
```

Now `inputWidget` will return the new application state whenever the text is changed or whenever it receives focus.

#### Input Output

Concur provides seamless IO at widget boundaries. This is another area where other frameworks falter. Since concurrency is baked into and integral to Concur, IO becomes predictable and powerful. Concur provides lifting functions to lift IO into widgets, and those lifted io widgets can be easily composed just like any other normal widget.

Continuing our previous example, if we want to print to the console, the greeting selected by the user.

```purescript
helloWidget = do
  greeting <- div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
  liftEff (log ("You chose to say " <> greeting))
  text (greeting <> " Sailor!")
```

Here we use `liftEff` to convert an effect into a widget. Once purescript-0.12 support is merged, this will become `liftEffect`. In Haskell, we can use `liftIO`.

This facility for seamless IO is very handy when creating bindings to existing native/JS libraries.

#### Replicating Elm Architecture

Concur is strictly more powerful, while simultaneously being easier to use than Elm. We were able to compose widgets, handle events, and dynamically change the page contents without adopting an "architecture", or manually threading any state or action data structures.

1. With Concur, there's no magic with effects and actions involved. A 'Widget' means a very simple thing - A section of UI which returns a value. Using this single concept, it's trivial to recreate the Elm architecture, but while remaining in full control, and understanding every step of the process.

2. There is no global state, and you can use local state with as fine a granularity as desired. Moreover, the resulting widget can be used within other widgets without having to thread in its state manually. EVerything just works!

Here's an example of building a small form with Concur -

```purescript
-- This is like Elm's State
type Form =
  { name :: String
  , rememberMe :: Bool
  }

-- This is like Elm's Action
data FormAction
  = Name String
  | RememberMe Bool
  | Submit

formWidget form = do
  -- This is like Elm's view function
  res <- div'
    [ Name <$> input [_type "text", value form.name, onChange] []
    , RememberMe <$> input [_type "checkbox", checked form.rememberMe, onChecked] []
    , Submit <$ button [value "Submit", onClick] []
    ]
  -- This is like Elm's update function
  case res of
    Name s -> formWidget (form {name = s})
    RememberMe b -> formWidget (form {rememberMe = b})
    Submit -> pure form
```

Now you can use `formWidget` as a regular widget anywhere else in the rest of your application. Note that the other parts of your application don't need to know about `FormAction` at all.
