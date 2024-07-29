---
title:  "C vs Swift 5.3"
date:   "2020-08-20"
tags:   ["swift"]
---

I revisit the C vs Swift post to see how things have improved.

<!--more-->

### Baseline

I'm running on newer hardware, so a new baseline is required. Like the original,
I'm using `-Ofast`, and this time, the run completed in about 17.45 seconds.

```sh
time orbitals
```

```
Computing row 2880 of 2880
Done! Return code = 0
orbitals  17.45s user 0.04s system 99% cpu 17.498 total
```

### Swift

After reviewing the original code, I noticed that I had refactored it to use
arguments to specify the number of pixels and iterations. These are not
constants, as they are in the C version, which can permit the compiler to
perform additional optimizations. I decided to test that theory. Running the
current version in Swift 5.3 yielded a resunt in about 20 seconds. Changing it
to use constants put it ahead of the C version.

```sh
time orbitals-swift
```

```
orbitals-swift  16.23s user 0.07s system 99% cpu 16.321 total
```
