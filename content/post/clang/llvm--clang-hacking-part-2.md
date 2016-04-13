+++
title = "llvm / Clang hacking: Part 2"
date = 2012-06-06T00:07:00Z
updated = 2012-08-02T22:24:55Z
categories = ["clang"]
+++

Part 2 in my N-part series on my exploration of hacking on [llvm](http://llvm.org) and [Clang](http://clang.llvm.org) (c-language) tool chain.

## Prerequisites

This post assumes you've successfully completed [Part 1](/post/clang/llvm--clang-hacking-part-1/) of the series.  

## Debugging

By default, Clang presents a [gcc-compatible](http://clang.llvm.org/docs/DriverInternals.html#gcccompat) command-line interface.  In most circumstances, this allows Clang to be a drop-in replacement for gcc for rapid testing and easier adoption.  When using the gcc interface, Clang spawns a new job to handle the compilation, which prevents debugging the various stages of the compilation process.  You can see this by running the following command:

```sh
$ clang -### test.m -o test
```

With that in mind, you should invoke Clang with the -cc1 option as the first argument, which directly executes the Clang cc1 tool.  TIP: running the following command outputs considerably useful command line documentation:

```sh
$ clang -cc1 --help
```

Also noteworthy is the following command

```sh
$ clang -cc1as --help
```

which documents many of the arguments available to the Clang Integrated Assembler.

If your using Xcode, you'll find main() in Clang executables » clang » Source Files » driver.cpp.  Notice in this folder you'll also find the cc1_main.cpp, which is the entry point for the clang compiler. 

![Clang executable source files](http://lh4.ggpht.com/-0Sq4-kkOw_c/T879Zwl628I/AAAAAAAAA6Q/GOrL03TypjA/clang_executable_source_files.png?imgmax=800 "clang_executable_source_files.png")

To set these command-line options, select Product | Edit Scheme:

![Edit Scheme](http://lh6.ggpht.com/-2I4WW8JChMM/T88BxYWtJkI/AAAAAAAAA6c/uAjsTKWSKyI/edit_scheme_to_set_arguments.png?imgmax=800 "edit_scheme_to_set_arguments.png")

Note again that the first argument must be -cc1\.  Next is the input file to compile, which you will substitute for your own source file.  Finally you must specific the include path for your current clang headers as this development build will not find them automatically.

With these set, you should be able to successfully run and debug the entire Clang tool chain.

## Recommended Reading

Now that you have an environment and presumably can debug the compiler, I'd recommend you read the following articles for clarification on the design and internals of Clang:

*   [Clang CFE Internals Manual](http://clang.llvm.org/docs/InternalsManual.html)
*   [Library Based Architecture](http://clang.llvm.org/features.html#libraryarch)
*   Identifies the major libraries and their function, which correspond to many of the top-level folders within the Xcode project tree.

## Next Up

In [Part 3](/post/llvm--clang-hacking-part-3), I'll walk through creating a language extension to Objective-C, supporting `NSURL` literals, following (in principal) the introduction of [Objective-C Literals](http://clang.llvm.org/docs/ObjectiveCLiterals.html) featuring in the next release of Clang.

## Twitter

Follow me on twitter, [@stuartcarnie](http://twitter.com/stuartcarnie).
