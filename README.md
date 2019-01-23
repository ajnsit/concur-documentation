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

#### A Widget

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

All Widgets in Concur have a *lifecycle*. But unlike the ad-hoc lifecycle in other frameworks, here the lifecycle is principled and reflected in the type. A widget that stops running after a while but doesn't return anything useful (e.g. a button) usually has the type `Unit` (or `()` in Haskell). A widget that displays something and never stops (e.g. a label) has the type `forall a. a` which is equivalent to `Void`, or the uninhabited type. Forever looping widgets will also usually have this type. The type itself shows clearly that nothing scheduled to be run after this widget will ever run.

The type of this widget - `forall a. Widget HTML a` is also automatically inferred by the compiler, so need not be specified. The compiler knows that the return type of this widget is `forall a. a` because we have not attached any event handlers to the widget, so any dom elements created are static and never end or change.

The resulting widget is self contained and can be easily populated on the page (within a div with the id "hello") -

```purescript
main = runWidgetInDom "hello" helloWidget
```

#### Simply Composing Widgets


Widgets can be easily laid out on the page using the HTML DSL. So you can create a layout with two buttons side by side like so -

```purescript
hello :: forall a. Widget HTML a
hello = div'
  [ button' [text "Ahoy Port!"]
  , button' [text "Ahoy Starboard!"]
  ]

```

Or one above the other (using div's to separate them) -

```purescript
hello :: forall a. Widget HTML a
hello = div'
  [ div'
      [ button' [text "Ahoy Port!"] ]
  , div'
      [ button' [text "Ahoy Starboard!"] ]
  ]

```

Layout is **composition** *in space*. The two button widgets occupy different parts of the page, depending on the layout, but run together *in time*. Under the hood, composition in space is equivalent to Monoidal composition. The key characteristic of monoidal composition is that the type of the combined widget is the same as the type of the individual widgets (which must match). As we saw earlier, the type of the individual buttons is `forall a. Widget HTML a`, so the type of the combined widget is also the same. So the entire widget can be placed anywhere a single button can be.

This leads to surprising power, since effectively, the return values *bubble* up the DOM tree, and you can then handle the return value at any level of granularity.

We'll further discuss layout, but first, we need to discuss event handling.

#### Event Handling

In the previous examples, clicking the buttons does nothing. After all, we haven't attached an event handler to the button! Traditionally, responding to user input seems to be the place where complexity creeps into the application. In Elm, for example, this is the place where you would reach out for the *Elm Architecture*.

Handling external events is where Concur shines. It does away with all the incidental complexity and lets you focus on the program logic. I'll make a claim that handling generic user events in Concur is simpler than any other library/framework in any programming language ever. If you know of anything else simpler, please let me know.

In Concur, you can just attach tags to the widgets to indicate the events you wish to handle, and then handle them synchronously in the program flow. Concur takes care of all the plumbing and concurrency for you. This is part of where Concur's name comes from - its support for Concurrency. The other reason for Concur's name is due to what I call aliasing of types - where adjacent types and properties must agree (concur) before they can be composed, which leads to increased type safety and powerful composition primitives. We'll discuss concurrency and composition primitives later in this guide.

Let's say we want to show a greeting to the user when the button is is clicked. We can do so very easily, by attaching an `onClick` attribute to the button. We have to use `button` instead of `button'`. (`button'` is defined simply as `button []` for convenience).

```purescript
hello :: forall a. Widget HTML a
hello = do
  button [onClick] [text "Say Hello"]
  text "Hello Sailor!"
```

That's right, `text` is also a complete widget on its own, which we are composing with the `button` widget. Here we use do-notation to compose two widgets *in time*. The text widget never runs at the same time as the button, and only shows up when, and as soon as, the button is clicked.

The final effect is that we see a button with the text "Say Hello", which when clicked gets replaced by text "Hello Sailor!". Ain't that easy!

Again, pay attention to the final type of the widget. It's the same as before (`forall a. Widget HTML a`) even though this time we *did* attach an event handler. However, we handle the event entirely internally, and simply change to a static widget (`text`) in response. So from the perspective of the rest of the program, the widget never returns.

#### Modifying DOM in response to Events

What if we want to modify the original button, instead of replacing it entirely? Surely that would require having some sort of "architecture" or design pattern in place? As it happens to be, even in that case we don't need a separate workflow. Instead, we exploit a little trick of virtual dom, and replace the original button with a conceptually different button, and Concur takes care of updating the button efficiently instead of replacing it entirely.

```purescript
hello :: forall a. Widget HTML a
hello = do
  button [onClick] [text "Say Hello"]
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

#### Handling Multiple Events

You can have mutliple event handlers on the same widget easily.

Here's an example of an input element with both change and focus handlers.

```purescript
data Action = Changed String | Focused

inputWidget :: Widget HTML Action
inputWidget = input [(Changed <<< unsafeTargetValue) <$> onChange, Focused <$ onFocus] []
```
(The `unsafeTargetValue` is equivalent to `event.target.value` in Javascript. It's literally defined as `(unsafeCoerce e).target.value`. It's "unsafe" because it can't statically guarantee that the onChange handler has been called on a target with a value. We are considering implementing a better more-typesafe API for getting the target value.)

Here `inputWidget` will return an `Action`, whenever the text is changed, or when it receives focus.

You don't need an action data type if you decide to process the action in line. For example, if you maintain a state -

```purescript
type State = {focusCount:: Int, currentText :: String}

inputWidget :: State -> Widget HTML State
inputWidget st = input [st {focusCount = st.focusCount+1} <$ onFocus, ((\s -> st {currentText = s}) <<< unsafeTargetValue) <$> onChange] []
```

Now `inputWidget` will return the new application state whenever the text is changed or whenever it receives focus.

#### Composing Widgets and Event Handling

As previously mentioned, Concur Widgets compose seamlessly, in space, and in time, and the composition is dictated by the types of the Widgets. Let's discuss that further.

The following widget will forever move between "Ping" and "Pong" buttons, and will never end. Hence it has the type `forall a. Widget HTML a`

```purescript
pingPong :: forall a. Widget HTML a
pingPong = do
  forever $ do
    button [onClick] [text "Ping"]
    button [onClick] [text "Pong"]
  text "This text will never be shown"
```

Note that all widgets, which are either static, or entirely self contained and run forever, have the type `forall a. Widget HTML a`, which can be composed with any other widget. This means that they can be dropped into any part of the layout without affecting the types or the logic of the rest of the application. So if you have a button in the middle of the UI somewhere - `button [onClick] [text "Blah"]` - and you wanted to add a text label alongside, the combined widget `div_ [text "Click this button" , button [onClick] [text "Blah"]]` will be conceptually equal to the button on its own.

Including a formal notion of a widget lifecycle, and a return type, has distinct advantages. For example, unlike other frameworks, the user never has to manually plumb events, state, and view updates together. So if you have a counter widget which returns an Integer -

```purescript
-- A counter widget takes the initial count as argument, and returns the updated count
counter :: Int -> Widget HTML Int
counter count = do
  button [onClick] [text (show count)]
  pure (count + 1)
```

You can compose an entire list of them easily on a webpage. The return value of the combined widget is the value of the first counter to return.

```purescript
-- Compose a list of counters in parallel
-- The return value is the value of the counter which is clicked
listCounters :: [Int] -> Widget HTML Int
listCounter = orr <<< map counter
```

Now if we want to distinguish which counter was updated, we can use the `Functor` instance of Widgets to tag the return type. Widgets are also `Applicative` (and as we have already seen, `Monad`). These instances, allow a natural handling of return values using `<$>`, `<*>`, `<$`, monadic do-notation, etc. So we can now return the updated count tagged with the index of the Counter which was clicked -

```purescript
-- Compose a list of counters in parallel
-- The return value is the value which was clicked together with its index.
listCounters :: [Int] -> Widget HTML {count: Int, index: Int}
listCounter initialCounts = orr (mapWithIndex mkCount initialCounts)
  where mkCount index = map (\count -> {index, count}) <<< counter
```

See how we map over the widgets to modify the return value to a record with index.

Or even better, you can simply return the modified counts together as a list, so the initial list of counts and the final list of counts have the same type.

```purescript
-- Compose a list of counters in parallel
listCounters :: [Int] -> Widget HTML [Int]
listCounter initialCounts = orr (mapWithIndex (mkCount initialCounts) initialCounts)
  where mkCount initialCountArray index initCount = map (\count -> fromMaybe initialCountArray (updateAt index count initialCountArray)) (counter initCount)
```

It's also important to note, that the new list widget is still short and encapsulated within a single function (**As it really should be**).

Of course, Concur also provides a better way to show multiple widgets in parallel and collect the output from each of them. It's called `andd` and it waits until all the reponses have been received (compare with `orr` which returns the first successful response). With `andd` you can simply write -

```purescript
-- Compose a list of counters in parallel
listCounters :: [Int] -> Widget HTML [Int]
listCounter initialCounts = andd (map counter initialCounts)
```

#### Input Output

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

1. Allow cancelling long running IO actions in response to GUI events, without any boilerplate. Let's say we want to create a timer which can be cancelled. The following widget will return when the timer completes after a specified duration, or when the button is pressed, whichever is first. Whenver the button is pressed, the timer is thread is automatically cancelled so you don't have to do that manually.

```purescript
timerWidget ms = liftAff (delay (Milliseconds ms)) <|> button [unit <$ onClick] [text "Cancel"]
```

2. Show a "Loading" screen while performing IO, let's say while making an ajax call.

```purescript
resp <- liftAff (AX.get ResponseFormat.json url) <|> text "Fetching posts from subreddit..."
```

Also note that the ability to perform IO actions from Widgets is very handy when creating bindings to existing native/JS libraries.

## Architectural Notes

### Replicating Elm Architecture

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

formWidget :: Form -> Widget HTML Form
formWidget form = do
  -- This is like Elm's view function
  res <- div'
    [ Name <$> input [_type "text", value form.name, unsafeTargetValue <$> onChange] []
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

This is surprisingly powerful and composable. Let's build a widget which allows editing an arbitrary list of such forms in a sequence and then returns the list of modified forms. How many more lines of code are needed to accomplish that?

The program is literally two words long i.e. `traverse formWidget`.

```purescript
multiFormWidget :: [Form] -> Widget HTML [Form]
multiFormWidget = traverse formWidget
```

Here, we use the fact that `Widget` also has an `Applicative` instance (along with Functor and Monad instances) -

If instead you wanted to allow editing all the forms together, you would do -

```purescript
multiFormWidget :: [Form] -> Widget HTML [Form]
multiFormWidget = andd <<< map formWidget
```

Compare that with all the plumbing with Events, Actions, and State, that is needed for an equivalent *Elm* program. It would be even worse with something like *Reflex* because you would have to keep track of where you were in the list in the editing process, and also, what the values of the previously submitted forms are, and you would have to do that by composing events while manipulating the DOM (try it).

### Plan for Composability by Breaking Down Components

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
  greeting <- getgreeting
  showgreeting greeting
  hello
```

Now the composition logic and the recursive call is isolated from the rest of the UI code, and can be easily changed if needed.

For example, if the requirement changes to also always display the previously selected greeting, then you can do that without changing `getGreeting`, and `showGreeting`, and making only minimal changes to `hello`. We need to take the previous greeting as a parameter, and then loop on the new greeting when one has been selected.

```purescript
hello :: String -> forall a. Widget HTML a
hello prev = hello =<< div'
  [ text ("Previous greeting - " <> prev)
  , do
      greeting <- getgreeting
      showgreeting greeting
      pure greeting
  ]

helloWithPrev :: forall a. Widget HTML a
helloWithPrev = hello ""
```

### Composing Never-ending Widgets

As the previous section showed, it's recommended to avoid creating widgets which never end, to allow arbitrary composition. However, at this point it must be pointed out that Concur does allow you to compose never-ending widgets in meaningful ways, *as long as you remember that a never ending-widget will only be a Consumer, and not a Producer of meaningful data*. **(PS: There are ways to get data out of a never ending widget as well. See remoteWidget for an example)**.

Composing never ending widgets works because parent widgets completely control the child widgets, and can remove them from the view at will irrespective of whether the widget ended.

So you can imagine a "Dashboard" type widget which displays some complex data and even allows complex user interaction to slice and dice the data, however never supplies any data back to the rest of the app. It may have a type signature like - `dashboard :: CompanyData -> forall a. Widget HTML a`.

You can have a separate widget called `awaitDataChange` that watches for changes to the company data and returns the changed data. Perhaps it shows a UI that allows the user to edit the data themselves, or gets it from a database hook or some other source. You can still compose that with the dashboard widget like so -

```purescript
app data = do
  -- The dashboard widget will be removed as soon as new data comes in
  newData <- dashboard data <|> awaitDataChange
  -- Now we can just restart the dashboard widget with the new data
  app newData
```

## Signals

#### Introduction

Signals are a more recent addition to Concur, and relatively experimental. However, like everything else in Concur, a signal is basically one simple idea that goes a long way.

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
2. The complete behaviour of the widget includes the UI *and* the recursion. It feels unsatisfactory to *have to*** separate the two in order to be able to compose things.

**Enter Signals.**

In essence, A Signal is a recursive / never ending widget which can still be composed seamlessly. Like a Widget, a Signal is also parameterised on the View, and the return value. A Signal is of type `Signal v a`, where `v` is the type of the view, and `a` is the return value. However, a signal does not "end" after returning a value, but is instead free to keep processing. Apart from being able to compose with other Signals, a Signal is conceptually equivalent to a never-ending Widget `forall a. Widget HTML a`.

So we have a function called `dyn` which can take a Signal and converts it to a never-ending Widget so it can be displayed.

```purescript
dyn :: forall v a b. Signal v a -> Widget v b
```

Here we ignore the return value of the Widget, since we can't make use of it in Widget space, and return a widget with an indeterminate value `b`, i.e. the Widget is never-ending.

#### Your first Signal

So how do we build signals? The workhorse for that is the function `hold`. It is dual to `dyn`, in that it allows creating a Signal from a Widget.

```purescript
hold :: forall v a. a -> Widget v (Signal v a) -> Signal v a
```

Each Signal always has a value associated with it. It's a `Comonad`, so the current value of the Signal can be extracted with `extract`.

Hold takes the initial value of the signal as its first argument. The second argument is the Widget. This way of creating Signals requires recursion to be implemented in continuation passing style. The Widget passed in needs to perform some work, and then return the continuation Signal.

Let's rewrite our hello example with Signals -

```purescript
hello :: String -> Signal HTML String
hello s = hold s do
  greeting <- div'
    [ "Hello" <$ button [onClick] [text "Say Hello"]
    , "Namaste" <$ button [onClick] [text "Say Namaste"]
    ]
  text (greeting <> " Sailor!") <|> button [onClick] [text "restart"]
  pure (hello greeting)
```

It's almost the same as the straightforward Widget version! The only things that changed are that we had to specify the initial value of the greeting (by passing it as an argument to `hold`), and we had to change the explicit recursion to CPS by returning `hello` instead of directly calling `hello`.

Now we can use it with `dyn` -

```purescript
helloWidget :: forall a. Widget HTML a
helloWidget = dyn $ hello ""
```

#### Signal Composition

Signals mimic traditional FRP by offering Monadic composition. It's always composition *in space* since Signals don't have a lifecycle, and run forever (until removed entirely from the page). That is to say that Signals don't have (or need) composition *in time*.

Need to display a list of greeting widgets on the page at the same time?

```purescript
helloList :: Array String -> Signal HTML (Array String)
helloList = traverse hello
```

The Signal will show all the individual widgets at the same time, and allow editing them at the same time. Note that we did not have to manage the recursion for individual signals when composing them.

We can convert a simple display widget to a signal with `display`.

```purescript
display :: Widget HTML (Signal HTML Unit) -> Signal HTML Unit
display = hold unit
```

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
