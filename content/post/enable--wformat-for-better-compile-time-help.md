+++
title = "Enable -Wformat for better compile time help"
date = 2009-04-18T16:16:00Z
updated = 2009-04-18T17:53:35Z
categories = ["osx", "xcode", "tips", "iphone"]
+++

Let the compiler do all the hard work, and be sure to enable the following warning:

![Picture 1.png](http://lh6.ggpht.com/_WTgxY9AxbJk/SepdBUhSs7I/AAAAAAAAAh8/oO-mV51nQew/Picture%201.png?imgmax=800)

It does more than just validate printf/scanf formatting calls, which is helpful in itself. It also validates that a sentinel is present in variadic functions. A sentinel is typically NULL or nil for the last parameter. A common place you would benefit from this is using the arrayWithObjects method of NSArray, that requires a nil for the last value.

```mm
NSArray *items = [NSArray arrayWithObjects:@"one", @"two", nil];
```

If the nil is absent, you're receive the following:

![Picture 3.png](http://lh4.ggpht.com/_WTgxY9AxbJk/Sep1_lFLQ3I/AAAAAAAAAiA/RpVKEbbVEaM/Picture%203.png?imgmax=800)