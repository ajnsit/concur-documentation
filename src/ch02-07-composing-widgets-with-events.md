# Composing Widgets with Events

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
listCounters :: Array Int -> Widget HTML Int
listCounters = orr <<< map counter
```

Now if we want to distinguish which counter was updated, we can use the `Functor` instance of Widgets to tag the return type. Widgets are also `Applicative` (and as we have already seen, `Monad`). These instances, allow a natural handling of return values using `<$>`, `<*>`, `<$`, monadic do-notation, etc. So we can now return the updated count tagged with the index of the Counter which was clicked -

```purescript
-- Compose a list of counters in parallel
-- The return value is the value which was clicked together with its index.
listCounters :: Array Int -> Widget HTML {count: Int, index: Int}
listCounters initialCounts = orr (mapWithIndex mkCount initialCounts)
  where mkCount index = map (\count -> {index, count}) <<< counter
```

See how we map over the widgets to modify the return value to a record with index.

Or even better, you can simply return the modified counts together as a list, so the initial list of counts and the final list of counts have the same type.

```purescript
-- Compose a list of counters in parallel
listCounters :: Array Int -> Widget HTML (Array Int)
listCounters initialCounts = orr (mapWithIndex (mkCount initialCounts) initialCounts)
  where mkCount initialCountArray index initCount = map (\count -> fromMaybe initialCountArray (updateAt index count initialCountArray)) (counter initCount)
```

It's also important to note, that the new list widget is still short and encapsulated within a single function (**As it really should be**).

Of course, Concur also provides a better way to show multiple widgets in parallel and collect the output from each of them. It's called `andd` and it waits until all the reponses have been received (compare with `orr` which returns the first successful response). With `andd` you can simply write -

```purescript
-- Compose a list of counters in parallel
listCounters :: Array Int -> Widget HTML (Array Int)
listCounters initialCounts = andd (map counter initialCounts)
```
