# Some Notes on Signal Usage

1. A `Signal` is basically a `Widget` + `looping`. So `loopW`, which provides stateful looping, is the easiest way to generate individual signals.

2. Signals are "continuous", i.e. they always have a current value. So the notion of "merging" signals does not arise except as a zipping operation, where you provide a continuous function which combines individual values from the constituent signals. This "zipping" is possible using monadic bind like so -

```purescript
do
  a <- signal1
  b <- signal2
  c <- signal3
  pure (someFunction a b c)
```

3. If a signal depends on the value of another **upstream** signal, you can just express that dependency with parameter passing. For example, if `signal2` depends on the output from `signal1`, and `signal3` depends on the output of both `signal1` and `signal2` then -

```purescript
do
  a <- signal1
  b <- signal2 a
  c <- signal3 a b
  pure (someFunction a b c)
```

4. A slight complication occurs when a signal depends on the value of a **downstream** signal, we need to use the `loopS` combinator which "loops" the value of the last signal back up to the top so that the upstream signals can use it. Note that you would need the last signal to carry **all** the values that are needed by the upstream signals.

Usually you would have some sort of a state that is accessible to all the constituent signals, and loop over that state. In the counter+timer example, the timer and the counter both depended on the current count. So we need to loop the current count over.

5. Note that sometimes this changes the final return value of the signal away from what you want. So you may need to `map` the final composition function on top of the signal value.

To take a pedantic example - if `signal1` also depended on the values of `signal2` and `signal3`, we can have `signal3` return those values and make them available to `signal1` with `loopS`. Let's assume that the initial values for `signal2` is `initialB` and for `signal3` is `initialC`.

```purescript
loopS {b:initialB, c:initialC} \{b, c} -> do
  a <- signal1 b c
  b' <- signal2 a
  c' <- signal3 a b'
  pure {b:b', c:c'}
```

But now the final value of the signal is `Tuple b c`. So we need to fix that. We need to add the value of `signal1` to the output since that's needed in the final output, and then map over with `someFunction` -

```purescript
s = g <$> innerSignal
  where
    g {a,b,c} = someFunction a b c
    innerSignal = loopS {a:initialA, b:initialB, c:initialC} \{b, c} -> do
      a' <- signal1 b c
      b' <- signal2 a'
      c' <- signal3 a' b'
      pure {a:a', b:b', c:c'}
```

Not pretty, but usually you won't have such complicated state and dependencies.
