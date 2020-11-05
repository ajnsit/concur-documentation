# Composing Never Ending Widgets

As the previous section showed, it's recommended to avoid creating widgets which never end, to allow arbitrary composition. However, at this point it must be pointed out that Concur does allow you to compose never-ending widgets in meaningful ways, *as long as you remember that a never ending-widget will only be a Consumer, and not a Producer of meaningful data*. **(PS: There are ways to get data out of a never ending widget as well. See [remoteWidget](https://pursuit.purescript.org/packages/purescript-concur-core/docs/Concur.Core.Patterns#v:remoteWidget) for an example)**.

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
