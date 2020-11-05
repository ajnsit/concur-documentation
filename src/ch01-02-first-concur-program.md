# Your First Concur Program

Now that you’ve setup Concur, let’s write your first Concur program!

## Hello Sailor

It's traditional in the Purescript world, when learning a new framework, to write a little
program that shows the text `Hello, sailor!` to the screen, so we’ll do the same here!

Here's a simple widget (named hello) that displays the text "Hello Sailor!" -

```purescript
hello = text "Hello Sailor!"
```

Concur provides a very simple DSL for constructing HTML DOM elements. `text` is a function that takes a `string` and constructs a widget with that text inside it.

I hope that there is nothing mysterious about this program. Concur has a mantra - "Make easy things easy, and complex things only as complex as the problem itself". For a simple UI like this, the program is suitably simple.

## Counter Example

It's also traditional, when learning a UI framework, to demonstrate a program that can count up.
Here, we built such a counter program in Concur.

```purescript
counter count = do
  button [onClick] [text (show count)]
  counter (count + 1)
```

Here we define a function `counter` that takes an integer `count` as a parameter. This is the number we will start counting up from. We display a `button` which can be clicked (`onClick`) and with a label that shows the count that was passed in. When the button has been clicked, we restart the counter with an incremented count.

Don't worry if you don't understand the specifics, we'll cover everything later in the book.
The important thing to note here is that the program is still *very* simple, and it has scaled only linearly with UI complexity.
