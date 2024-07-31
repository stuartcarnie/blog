+++
title = "Constant Confusion"
date = 2011-12-03T01:33:00Z
updated = 2011-12-04T16:23:06Z
tags = ["c"]
[author]
	name = "Stuart"
	uri = ""
+++

The `const` keyword in C can be confusing, so I wanted to put down my thoughts for my own benefit and for those looking for some clarity. In summary, I'm going to advocate you place `const` to the right, and read the declaration from right to left.

## What is `const`?

`const` is a hint to the C compiler and programmer that a specific declaration or element of a declaration is immutable.  It is more complicated in C++, which is outside the scope of this post.  Lets start with a simple example, and the most common form where `const` is written first:

```c
const int i = 5;
```

The previous statement simply instructs the compiler that `i` cannot be changed after its initial declaration, such that the following assignment would result in an error:

```c
const int i = 5;
i = 6; // error: read-only variable is not assignable
```

If spoken from right to left, the declaration of `i` would read "`i` is an integer constant", which is reasonable enough.  This syntax is still supported for historical reasons.  The alternative and _correct_ format is to write `const` after the type reference, as follows

```c
int const i = 5;
i = 6; // error: read-only variable is not assignable
```

which means the same thing; the value of `i` must remain constant within its declared scope.  If you read this declaration from right to left, it is spoken as "`i` is a constant integer"; much better.

Yet another common definition is an array of constant characters or also known in some circles as an immutable string

```c
const char * myString = "hello World";
myString[0] = 'H';        // error: read-only variable is not assignable
myString = "Hello World"; // ok
```

Spoken from right to left, "`myString` is a pointer to character constant", which doesn't read so well. Switching the `const` keyword, we get the following

```c
char const * myString = "hello World";
myString[0] = 'H';        // error: read-only variable is not assignable
myString = "Hello World"; // ok
```

read aloud, it is “_myString_ is a pointer to constant characters“; sounds better than the former.

So far `const` seems pretty straight forward, and perhaps at this point you're thinking where is the confusion?.  Lets complicate things…  As we've demonstrated, the `const` keyword can be placed before or after the type reference in a declaration, which for the previous scenarios is fine.  Let's show a few more examples with pointers:

```c
int * const i;
```

Declares `i` as constant pointer to an integer.  Again, reading from right to left makes it pretty clear what we're dealing with.  The following shows both legal and illegal usages of `i`:

```c
int j = 5, k = 6;
int * const i = &j; // points to the memory referred to by j
i = &k;             // error: read-only variable is not assignable
*i = 6;             // ok, modifies the value of j
```

Now, because the `const` keyword can be placed before or after the type reference, as shown earlier, the following declarations of _h_ and `i`:

```c
int j = 5, k = 6;
const int * const h = &j;
int const * const i = &j;
```

result in a variable that is a constant pointer to a constant integer.  Given that, the following lines will result in compiler errors:

```c
*h = 5; // error: read-only variable is not assignable
h = &k; // error: read-only variable is not assignable
```

Want to see something confusing? The following 3 lines are identical, and will compile with current versions of clang and gcc:

```c
const int const g = 5;
const int h = 6;
int const i = 7;
```

It's not horrible, but lets reintroduce pointers

```c
const int const * i;
```

What is this? If you had not just read all the above, one would be forgiven for thinking `i` is a constant pointer to a constant integer, but in fact both instances of `const` refer to the int type; this is a pointer to a constant integer. The correct definition, as we saw earlier is

```c
const int * const i; // or preferably
int const * const i;
```

Worse still is multiple levels of indirection, such as

```c
int * * i;
```

which when read from right to left declares “`i` is a pointer to a pointer of integers”. A more complicated example using the `const` keyword

```c
int const * const * const i;
```

or `i` is a constant pointer to a constant pointer of constant integers. All levels of indirection are immutable, so it can only be assigned at its declaration.

```c
int j[2][3] = { {1, 2, 3}, {4, 5, 6} };
int const * const * const i = &j; // ok
i = NULL;     // error
i[0] = &j[0]; // error
i[0][0] = 5;  // error
```

It's an extreme case, but becomes evident why writing `const` to the right is a good habit.  The following declaration of `i` may look the same to the untrained eye:

```c
int j[2][3] = { {1, 2, 3}, {4, 5, 6} };
const int const * const * i = &j; // ok
i = NULL;     // ok!!
i[0] = &j[0]; // error
i[0][0] = 5;  // error
```

however, armed with this knowledge, you (and the compiler) say otherwise.

After all this, you may be asking when might I use the declaration _type const * const_?  When possible, I prefer to be explicit, and strings used as constants across compilation units are often declared as

```c
char const * kMyConstant = "Hello World";
```

and then referred to in a separate object file as

```c
extern char const * kMyConstant;
```

Whilst modifying the contents of `kMyConstant` is prohibited, assigning the pointer `kMyConstant` to a new location is possible, as follows

```c
extern char const * kMyConstant;
void fn() {
    kMyConstant[0] = 'h'; // error
    kMyConstant = "Hi!";  // now says "Hi!" instead of "Hello World"
}
```

Clearly for the majority of situations, this is not what the developer intended.  Changing the declaration to

```c
char const * const kMyConstant = "Hello World";
```

would also prevent the pointer `kMyConstant` from being overwritten.

## In summary

Always write `const` to the right and read the declaration from right to left.
