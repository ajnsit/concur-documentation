# Introduction

**Concur** is a UI development framework for functional languages like Haskell and Purescript. It is primarily used for client side web development, but there are efforts underway to port it to other platforms such as those for native desktop and mobile applications.

1. For learners - Concur focuses on **ease of development without sacrificing power**. Simplicity is the number one goal. Concur provides a consistent set of tools which can be combined in predictable ways to accomplish any level of functionality ranging from the simplest hello world example to the most complex frontend for a web app. Due to its extremely gentle learning curve, Concur is well suited for learners of functional programming (replacing console applications for learners).

2. For experienced folks - Assuming you are already familiar with functional programming, Concur will provide a satisfying development experience. Concur does not artificially constrain you in any form. You are encouraged to use your FP bag of tricks in predictable ways, and that is the standard way of doing things so you are never going against the grain. It's a *library* in spirit, rather than a framework.

3. For professional development - Concur scales linearly with program complexity. Simple things are easy, complex things are just as complex as the problem itself, no more. Reusing existing widgets, and refactoring existing code is extremely easy. Concur is a great choice to build your next enterprise application on.

4. Multiplatform development - Concur works with Haskell and Purescript. It provides a [React][react] backend, and a virtual-dom backend. With Haskell it also supports server-side virtual-dom with [Replica][replica]. There are other native backends in the works. It provides great support for integrating existing React widgets. Note that Concur code in all those platforms looks (or will look) exactly the same!

## Note about the code samples -

Throughout this tutorial, please keep in mind that Concur does not hide any functionality from you.
What you see in the code *is* exactly what is happening. There is never any *magical* plumbing that
takes place in the background which you don't directly control. So if a piece of code is not
immediately and independently understandable, then please point it out and I will try to make
it clearer.

> This book is written to be backend and language neutral. These concepts will also apply whether you are
> using the Haskell version or the Purescript version, and to some extent even JS and python versions.
> However to simplify things, all the code examples will be in Purescript, using the React backend.


[replica]: https://github.com/concurhaskell/concur-replica
[react]: https://reactjs.org/
