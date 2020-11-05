# Widget Return Types

All Widgets in Concur have a *lifecycle*. But unlike the ad-hoc lifecycle in other frameworks, here the lifecycle is principled and reflected in the type. A widget that stops running after a while but doesn't return anything useful (e.g. a button) usually has the type `Unit` (or `()` in Haskell). A widget that displays something and never stops (e.g. a label) has the type `forall a. a` which is equivalent to `Void`, or the uninhabited type. Forever looping widgets will also usually have this type. The type itself shows clearly that nothing scheduled to be run after this widget will ever run. I like to call such widgets "Display Widgets". They are typically used to display static things like text. Because of their polymorphic return type, they can be passed to any function which requires a Widget of a particular return type, which comes in very handy.

The type of this widget - `forall a. Widget HTML a` is also automatically inferred by the compiler, so need not be specified. The compiler knows that the return type of this widget is `forall a. a` because we have not attached any event handlers to the widget, so any dom elements created are static and never end or change.

The resulting widget is self contained and can be easily populated on the page (within a div with the id "hello") -

```purescript
main = runWidgetInDom "hello" hello
```
