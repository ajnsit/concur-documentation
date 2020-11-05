# Replicating Elm Architecture

Concur is strictly more powerful, while simultaneously being easier to use than Elm. We were able to compose widgets, handle events, and dynamically change the page contents without adopting an "architecture", or manually threading any state or action data structures.

1. With Concur, there's no magic with effects and actions involved. A 'Widget' means a very simple thing - A section of UI which returns a value. Using this single concept, it's trivial to recreate the Elm architecture, but while remaining in full control, and understanding every step of the process.

2. There is no global state, and you can use local state with as fine a granularity as desired. Moreover, the resulting widget can be used within other widgets without having to thread in its state manually. Everything just works!

Here's an example of building a small form with Concur -

```purescript
-- This is like Elm's State
type Form =
  { name :: String
  , rememberMe :: Boolean
  }

-- This is like Elm's Action
data FormAction
  = Name String
  | RememberMe Boolean
  | Submit

formWidget :: Form -> Widget HTML Form
formWidget form = do
  -- This is like Elm's view function
  res <- div'
    [ Name <$> input [_type "text", value form.name, unsafeTargetValue <$> onChange]
    , RememberMe (not form.rememberMe) <$ input [_type "checkbox", checked form.rememberMe, onChange]
    , Submit <$ button [onClick] [text "Submit"]
    ]
  -- This is like Elm's update function
  case res of
    Name s -> formWidget (form {name = s})
    RememberMe b -> formWidget (form {rememberMe = b})
    Submit -> pure form
```

Now you can use `formWidget` as a regular widget anywhere else in the rest of your application ([working example](https://github.com/purescript-concur/purescript-concur-react/blob/master/examples/src/Test/TheElmArchitecture.purs)). Note that the other parts of your application don't need to know about `FormAction` at all.

This is surprisingly powerful and composable. Let's build a widget which allows editing an arbitrary list of such forms in a sequence and then returns the list of modified forms. How many more lines of code are needed to accomplish that?

The program is literally two words long i.e. `traverse formWidget`.

```purescript
multiFormWidget :: Array Form -> Widget HTML (Array Form)
multiFormWidget = traverse formWidget
```

Here, we use the fact that `Widget` also has an `Applicative` instance (along with Functor and Monad instances) -

If instead you wanted to allow editing all the forms together, you would do -

```purescript
multiFormWidget :: Array Form -> Widget HTML (Array Form)
multiFormWidget = andd <<< map formWidget
```

Compare that with all the plumbing with Events, Actions, and State, that is needed for an equivalent *Elm* program. It would be even worse with something like *Reflex* because you would have to keep track of where you were in the list in the editing process, and also, what the values of the previously submitted forms are, and you would have to do that by composing events while manipulating the DOM (try it).
