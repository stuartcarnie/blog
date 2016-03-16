+++
title       = "pattern matching: expressions"
date        = "2016-03-01T15:52:58Z"
categories  = ["swift-compiler"]
draft       = true
+++

First post in a series where I will explore the Swift compiler and how code is translated to machine code.

<!--more-->

[Swift patterns][patterns] are a powerful feature of Swift, if you want to learn more about them, check out [Pattern Matching in Swift][2] by Andy Bargh. I will link to the various sections within Andy's post as I explore each of the Swift patterns.

{{< callout note >}}
Swift 2.2 was used for this post. The assembler output may vary from version to version.
{{< /callout >}}

[Expression Pattern](expression)
----

The expression pattern is arguably the most powerful of the patterns. As of Swift 2.2, these patterns can be used as a conditional expression in a number of statements such as `switch`, `if case`, `while case` and `for-in case`. 

Lets take a look at a simple expression, evaluating whether `a` falls within a specific range and compare that to a typical `if` statement:

~~~swift
func test_if_case(a: Int) -> Int {
    if case 2...5 = a {
        return a
    }
    return -1
}
~~~

Translated to x86_64 assembly resulted in the following output, which was identical to `if a >= 2 && a <= 5`

```x86asm
__TF4main12test_if_caseFSiSi:
0100000f50  push       rbp
0100000f51  mov        rbp, rsp
0100000f54  lea        rax, qword [ds:rdi-2]
0100000f58  cmp        rax, 0x4
0100000f5c  mov        rax, -0x1
0100000f63  cmovb      rax, rdi
0100000f67  pop        rbp
0100000f68  ret        
```

Ignore the first two lines, which is preamble. `rdi` is the value of the first argument, corresponding to the variable `a`.

[patterns]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Patterns.html

[2]: http://andybargh.com/pattern-matching-in-swift/

[identifier]: http://andybargh.com/pattern-matching-in-swift/#The_Identifier_Pattern

[expression]: http://andybargh.com/pattern-matching-in-swift/#The_Expression_Pattern