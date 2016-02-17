+++
title = "llvm / Clang hacking: Part 1"
date = 2012-06-02T14:21:00Z
updated = 2012-08-02T22:25:08Z
categories = ["clang"]
blogimport = true 
+++

Part 1 in my N-part series on my exploration of hacking on [llvm](http://llvm.org) and [Clang](http://clang.llvm.org) (c-language) tool chain.  I am running OS X 10.7, however I will try to highlight the steps where you should consider substituting for your platform.

## Getting Started

Follow [these steps](http://clang.llvm.org/get_started.html), with the following exceptions if you prefer git (it is a _lot_ faster); I am using the [official llvm mirror](http://llvm.org/docs/GettingStarted.html#git_mirror) on llvm.org.

*   Step 2, substitute the svn command for  

    `git clone http://llvm.org/git/llvm.git`

*   Step 3, substitute the svn command for  

    `git clone http://llvm.org/git/clang.git`

*   Step 4, substitute the svn command for  

    `git clone http://llvm.org/git/compiler-rt.git`

*   Step 5, I am using CMake, so instead of ../llvm/configure  

    ```bash
    cmake -G "Unix Makefiles" ../llvm
    make
    ```

## Creating Xcode project for Clang

You could run

*   `mkdir llvm`
*   `cd llvm`
*   `cmake -G Xcode ../llvm`

to create an Xcode project for the entire llvm/Clang toolchain, however it ends up being thousands of source files and 223 targets! As I'm only interested in hacking on Clang, lets generate a project file for Clang and related projects only. Starting from the folder which contains build and llvm:

*   `mkdir clang`
*   `cd clang`
*   ```bash
    cmake -DCLANG_PATH_TO_LLVM_SOURCE=../llvm -DCLANG_PATH_TO_LLVM_BUILD=../build -DCMAKE_BUILD_TYPE=Debug ../llvm/tools/clang
    ```

Assuming all went well, you'll now have an Xcode project called Clang.xcodeproj with about 400 source files and 60 targets. Open it up and let Xcode index everything, which may take a few minutes depending on your hardware.  Once completed, switch to the clang target and build!

**Note:** On Windows, it is likely cmake will auto-detect your Visual Studio environment and the above commands will Just Work™

![Xcode clang target](http://lh6.ggpht.com/-uMbB6KPBBvY/T8qD8KbfK8I/AAAAAAAAA58/QZTXhPnGPKk/clang_target.png?imgmax=800 "clang_target.png")

Assuming all goes well, a few minutes later you should see:

![Clang build Succeeded](http://lh4.ggpht.com/-o50LhcUc71I/T8qD8iMHhqI/AAAAAAAAA6E/4j7CIEAcilU/xcode_clang_successful_build.png?imgmax=800 "xcode_clang_successful_build.png")

Back in Terminal, you can run the following command, which creates a Hello World program and tests your build of Clang.

From the clang folder

* `cd bin/Debug`
* 

    ```cpp
    echo "#include \nint main(int argc,char**argv) { printf(\"hello world\\\n\"); return 0; }" &gt; hello.c &amp;&amp; clang hello.c -o hello &amp;&amp; ./hello
    ```

If you see _hello world_ after running the 2<sup>nd</sup> command, pat yourself on the back, as you've successfully setup a working llvm / Clang development environment to start your hacking.

## Next Up

[Part 2](/post/llvm--clang-hacking-part-2) and debugging Clang.

## Twitter

Follow me on twitter, [@stuartcarnie](http://twitter.com/stuartcarnie).