+++
title       = "C vs Swift: Reprise"
date        = "2016-03-12T18:44:23Z"
categories  = ["swift"]
+++

I revisit a post by David Steuber, which benchmarked C and Swift generating a 2880x2880 pixel Mandelbrot with 4000 iterations per pixel. C killed Swift.

<!--more-->

In [C vs Swift 1.2][post], David concludes

> The major take away on this is that Swift is not C or C++. For high performance computing, code has to be structured differently.

David acknowledges the Swift version is quite different from the C implemetation, which allocates an array _for each pixel_. Worse still, this array grows dynamically as it iterates 4000 times, per pixel.

### Baseline

The C version will serve as our baseline. Using `-Ofast`<sup id="fnref:1">[1](#fn:1)</sup> it completed in about 20.5 seconds.

~~~sh
orbitals  20.50s user 0.03s system 99% cpu 20.541 total
~~~

### Swift

I restructured the Swift code so it was semantically equivalent to the C implementation, meaning that it used fixed arrays and a single core. Using the default compiler optimizations, it completed in just under 43 seconds.

~~~sh
orbitals-swift  42.67s user 0.12s system 100% cpu 42.768 total
~~~

Enabling `-whole-module-optimization` dropped the runtime to 37.5 seconds. Adding `-Ounchecked` reduced it a further 9 seconds to complete in 28.5 seconds.

~~~sh
orbitals-swift  28.35s user 0.11s system 100% cpu 28.446 total
~~~

{{< callout note >}}
Swift 2.2 was used for this post
{{< /callout >}}

----

<b id="fn:1">1</b> `-flto` hurt performance, hence limiting to the `-Ofast` option [↩](#fnref:1)

[post]: https://www.david-steuber.com/2015/03/c-vs-swift-1-2-and-getting-killed/