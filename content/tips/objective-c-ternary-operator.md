---
title: "Objective-C: ternary operator"
date: "2012-03-15T23:01:00Z"
updated: "2012-03-16T11:23:25Z"
tags: ["objective-c"]
aliases:
  - /objective-c-ternary-operator/
---

The ternary operator, also known as a conditional expression is a C construct.  What follows is an example used for conditional assignment:

```mm
result = condition_expression ? true_expression : false_expression;
```

If `condition_expression` evaluates to true, result will be assigned the `true_expression`; otherwise, result will be assigned the `false_expression`.  A GNU extension to the ternary operator, also available in Objective-C, is the ability to omit the `true_expression` as follows:

```mm
result = first_expression ?: second_expression;
```

result will be assigned the value of `first_expression` if it evaluates to true or `second_expression` if `first_expression` evaluates to false.

`nil` in Objective-C evaluates to `false`, so the GNU extension becomes particularly useful for ensuring default values during assignment, which is a pattern used widely in JavaScript.

A contrived example might be an init method that takes an `NSDictionary` with options for configuring the instance, as follows:

```mm
- (void)initWithOptions:(NSDictionary *)options {
	self = [super init];

	_options = options ?: kDefaultOptions;

	return self;
}
```

The assignment ensures `_options` is not null. options will be assigned to the `_options` ivar if non-nil; otherwise `_options` will be assigned the `kDefaultOptions` dictionary.

Another use case is for caching values, rather than initialize them all in the init method.

```mm
@implementation MyObj {
	NSString *_someValue;
}

@property (nonatomic, strong, readonly) NSString *someValue;

@synthesize someValue=_someValue;

- (id)init {
	…
}

- (NSString *)someValue {
	return _someValue ?: (_someValue = [some expensive operation]);
}
```

When a consumer accesses the `someValue` property of `MyObj`, if `_someValue` is `nil`, the right size of the ternary expression is returned, which retrieves the value via some expensive operation and stores it in `_someValue`.