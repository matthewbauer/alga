# Algebraic graphs

[![Hackage version](https://img.shields.io/hackage/v/algebraic-graphs.svg?label=Hackage)](https://hackage.haskell.org/package/algebraic-graphs) [![Linux & OS X status](https://img.shields.io/travis/snowleopard/alga/master.svg?label=Linux%20%26%20OS%20X)](https://travis-ci.org/snowleopard/alga) [![Windows status](https://img.shields.io/appveyor/ci/snowleopard/alga/master.svg?label=Windows)](https://ci.appveyor.com/project/snowleopard/alga)

**Alga** is a library for algebraic construction and manipulation of graphs in Haskell. See
[this paper](https://github.com/snowleopard/alga-paper) for the motivation behind the library, the underlying
theory and implementation details. There is also a
[Haskell eXchange talk](https://skillsmatter.com/skillscasts/10635-algebraic-graphs).

## Main idea

Consider the following data type, which is defined in the top-level module
[Algebra.Graph](http://hackage.haskell.org/package/algebraic-graphs/docs/Algebra-Graph.html)
of the library:

```haskell
data Graph a = Empty | Vertex a | Overlay (Graph a) (Graph a) | Connect (Graph a) (Graph a)
```

We can give the following semantics to the constructors in terms of the pair **(V, E)** of graph *vertices* and *edges*:

* `Empty` constructs the empty graph **(∅, ∅)**.
* `Vertex x` constructs a graph containing a single vertex, i.e. **({x}, ∅)**.
* `Overlay x y` overlays graphs **(Vx, Ex)** and **(Vy, Ey)** constructing **(Vx ∪ Vy, Ex ∪ Ey)**.
* `Connect x y` connects graphs **(Vx, Ex)** and **(Vy, Ey)** constructing **(Vx ∪ Vy, Ex ∪ Ey ∪ Vx × Vy)**.

Alternatively, we can give an algebraic semantics to the above graph construction primitives by defining the following
type class and specifying a set of laws for its instances (see module [Algebra.Graph.Class](http://hackage.haskell.org/package/algebraic-graphs/docs/Algebra-Graph-Class.html)):

```haskell
class Graph g where
    type Vertex g
    empty   :: g
    vertex  :: Vertex g -> g
    overlay :: g -> g -> g
    connect :: g -> g -> g
```

The laws of the type class are remarkably similar to those of a [semiring](https://en.wikipedia.org/wiki/Semiring),
so we use `+` and `*` as convenient shortcuts for `overlay` and `connect`, respectively:

* (`+`, `empty`) is an idempotent commutative monoid.
* (`*`, `empty`) is a monoid.
* `*` distributes over `+`, that is: `x * (y + z) == x * y + x * z` and `(x + y) * z == x * z + y * z`.
* `*` can be decomposed: `x * y * z == x * y + x * z + y * z`.

This algebraic structure corresponds to *unlabelled directed graphs*: every expression represents a graph, and every
graph can be represented by an expression. Other types of graphs (e.g. undirected) can be obtained by modifying the
above set of laws. Algebraic graphs provide a convenient, safe and powerful interface for working with graphs in Haskell,
and allow the application of equational reasoning for proving the correctness of graph algorithms.

To represent *non-empty graphs*, we can drop the `Empty` constructor -- see module
[Algebra.Graph.NonEmpty](http://hackage.haskell.org/package/algebraic-graphs/docs/Algebra-Graph-NonEmpty.html).

## How fast is the library?

Alga can handle graphs comprising millions of vertices and billions of edges in a matter of seconds, which is fast
enough for many applications. We believe there is a lot of potential for improving the performance of the library, and
this is one of our top priorities. If you come across a performance issue when using the library, please let us know.

Some preliminary benchmarks can be found [here](https://github.com/haskell-perf/graphs).

## Blog posts

The development of the library has been documented in the series of blog posts:
* Introduction: https://blogs.ncl.ac.uk/andreymokhov/an-algebra-of-graphs/
* A few different flavours of the algebra: https://blogs.ncl.ac.uk/andreymokhov/graphs-a-la-carte/
* Graphs in disguise or How to plan you holiday using Haskell: https://blogs.ncl.ac.uk/andreymokhov/graphs-in-disguise/
* Old graphs from new types: https://blogs.ncl.ac.uk/andreymokhov/old-graphs-from-new-types/
