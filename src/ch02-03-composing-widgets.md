# Composing Widgets

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
