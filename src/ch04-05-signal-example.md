# Signal Example

Here's an example of using Signals to implement a counter widget where the count is also automatically incremented every second -

```purescript
mainWidget :: forall a. Widget HTML a
mainWidget = do
  dyn $ loopS 0 \n -> do
    display $ D.text (show n)
    n' <- incrementTicker n
    counterSignal n'

-- Counter
counterSignal :: Int -> Signal HTML Int
counterSignal init = loopW init $ \n -> D.div'
  [ n+1 <$ D.button [P.onClick] [D.text "+"]
  , D.div' [D.text (show n)]
  , n-1 <$ D.button [P.onClick] [D.text "-"]
  ]

-- Timer
incrementTicker :: Int -> Signal HTML Int
incrementTicker init = loopW init $ \n -> do
  liftAff $ delay $ Milliseconds 1000.0
  pure (n+1)
```
