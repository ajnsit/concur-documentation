# Installation

## Concur Haskell

It has three backends -

1. [React](https://github.com/facebook/react) based, called [concur-react](concur-react). You can use the [Concur-React Quickstart Template](https://github.com/concurhaskell/concur-react-starter) to quickly get started.

    An example of using Native React Widgets is here - [Drag Drop Sortable List Widget (React)](https://github.com/concurhaskell/concur-react-sortable-tree/blob/master/src/Main.hs) - [Demo](https://ajnsit.github.io/concur/examples/sortable-tree-example.jsexe/index.html) - Demonstrates Concur binding to [React-Sortable-Tree](https://github.com/fritz-c/react-sortable-tree).

2. [Virtual-Dom](https://github.com/Matt-Esch/virtual-dom) based, called [concur-vdom](concur-vdom). (**Bitrotten**). You can use the [Concur-Vdom Quickstart Template](https://github.com/concurhaskell/concur-vdom-starter) to quickly get started.

3. [Replica](https://github.com/pkamenarsky/replica) (i.e. remote virtual-dom) based, called [concur-replica](https://github.com/pkamenarsky/concur-replica). Created and maintained by [pkamenarsky](https://github.com/pkamenarsky). Head to its [project page](https://github.com/pkamenarsky/concur-replica) for more information.

## Concur Purescript

You can quickly get a production setup going (using Spago and Parcel) by cloning the [Purescript Concur Starter](https://github.com/purescript-concur/purescript-concur-starter).

Else, if you use Spago -

```bash
spago install concur-react
```

Or if you use Bower -

```bash
bower install purescript-concur-react
```

## Building examples from source

```bash
git clone https://github.com/purescript-concur/purescript-concur-react.git
cd purescript-concur-react
npm install
# Build library sources
npm run build
# Build examples
npm run examples
# Start a local server
npm run start
# Check examples
open localhost:1234 in the browser
```

## More Help

Look at the individual pages for [Concur (Haskell)][concurhaskell], or [Purescript-Concur](concurps) for further instructions.

[concurhaskell]: https://github.com/ajnsit/concur
[concurps]: https://github.com/ajnsit/purescript-concur
