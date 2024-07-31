---
title: "Associative References in Objective-C"
date: 2012-02-19T22:58:00Z
updated: 2012-02-20T00:12:36Z
tags: ["objective-c"]
aliases:
  - /associative-references-in-objective-c/
---

This is some preamble.

<!--more-->

Take the following Javascript (stick with me):

```js
var obj = {};
obj.newProperty = "Hello World";
```

For the unfamiliar, we've just declared an empty object and associated the string _Hello World_ to the key _newProperty_ with the instance _obj_.

We can access the value of _newProperty_ using dot notation:

```js
console.log(obj.newProperty);    // logs Hello World
```

or as an associative array:

```js
console.log(obj["newProperty"]); // logs Hello World
```

Finally, if we want to remove the property, we can use the _delete_ statement:

```js
delete obj.newProperty;
```

## What has this got to do with Objective-C?

Being able to add arbitrary data to an object is a powerful tool.  It turns out that as of OS X 10.6 and iOS 3.1, Apple added new Objective-C runtime APIs, categorized as [associative references](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocAssociativeReferences.html) to do just that.

Semantically, the following code is the same as the previous Javascript:

```mm
NSObject *obj = [NSObject new];
objc_setAssociatedObject(obj, @"newProperty", @"Hello World", OBJC_ASSOCIATION_RETAIN_NONATOMIC);
```

which associates the string _Hello World_ to the instance _obj_ using the key _newProperty_. The last parameter allows us to instruct the runtime how the associated instance will be transferred to _obj_. These policies map to regular Objective-C property attributes including _copy_, _retain_, _assign_ and their non-atomic counterparts.

```mm
printf("%s\n", [objc_getAssociatedObject(obj, @"newProperty") UTF8String]);
```

Retrieves the value of _newProperty_ from instance _obj_.

```mm
objc_setAssociatedObject(obj, @"newProperty", nil, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
```

Passing `nil` clears the association.

## That's great, but why not just declare the property in my class declaration?

Fair enough, however recall Objective-C categories, which provides a means for adding methods to a class. Commonly used as an alternative to subclassing, categories are unable to add additional state, and therein lies their limitation. [This example](http://www.wannabegeek.com/?p=96) served as inspiration for the following, which provides a convenience method for displaying a UIAlertView with a completion block, all neatly contained within the same instance, No Subclassing Required ™

<p>
    <script src="https://gist.github.com/stuartcarnie/1868028.js"></script>
</p>