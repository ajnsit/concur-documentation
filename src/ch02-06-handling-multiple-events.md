# Handling Multiple Events

You can have mutliple event handlers on the same widget easily.

Here's an example of an input element with both change and focus handlers.

```purescript
data Action = Changed String | Focused

inputWidget :: Widget HTML Action
inputWidget = input [(Changed <<< unsafeTargetValue) <$> onChange, Focused <$ onFocus]
```
(The `unsafeTargetValue` is equivalent to `event.target.value` in Javascript. It's literally defined as `(unsafeCoerce e).target.value`. It's "unsafe" because it can't statically guarantee that the onChange handler has been called on a target with a value. We are considering implementing a better more-typesafe API for getting the target value.)

Here `inputWidget` will return an `Action`, whenever the text is changed, or when it receives focus.

You don't need an action data type if you decide to process the action in line. For example, if you maintain a state -

```purescript
type State = {focusCount:: Int, currentText :: String}

inputWidget :: State -> Widget HTML State
inputWidget st = input [st {focusCount = st.focusCount+1} <$ onFocus
                       , ((\s -> st {currentText = s}) <<< unsafeTargetValue) <$> onChange]
```

Now `inputWidget` will return the new application state whenever the text is changed or whenever it receives focus.
