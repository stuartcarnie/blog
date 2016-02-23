+++
title = "llvm / Clang hacking: Part 3"
date = "2012-06-06T02:33:00Z"
updated = "2012-08-02T22:24:44Z"
categories = ["clang"]
+++

Part 3 in my N-part series on my exploration of hacking on [llvm](http://llvm.org) and [Clang](http://clang.llvm.org) (c-language) tool chain.

## Prerequisites

This post assumes you've successfully completed [Part 1](/post/clang/llvm--clang-hacking-part-1/) and [Part 2](/post/clang/llvm--clang-hacking-part-2/) of the series.  I'm also going to assume if you're interested in hacking on Clang, you have an understanding of compilation and are familiar with terms such as lexing, parsing, syntactic analysis, semantic analysis and code generation.  If not, then you need to purchase a copy of [Compilers: Principals, Techniques and Tools](http://www.amazon.com/Compilers-Principles-Techniques-Tools-Edition/dp/0321486811/ref=pd_sim_b_1), also known as the [Dragon Book](http://en.wikipedia.org/wiki/Compilers:_Principles,_Techniques,_and_Tools) and read through it.  There are also plenty of resources on Google.

## Objective-C Extension: NSURL Literals

[Objective-C literals](http://clang.llvm.org/docs/ObjectiveCLiterals.html) are an exciting syntactic feature coming to the next release of Clang.  This will be available in [Xcode 4.4](http://stackoverflow.com/questions/9347722/apple-llvm-4-0-new-features-on-xcode-4-4-literals) and presumably the next iOS update.  I was indirectly presented with the challenge on Twitter from [@casademora](https://twitter.com/casademora/status/208596677551071232) when querying what an NSURL literal might look like.  Truthfully, I've wanted an excuse to hack on Clang and this seemed small enough in scope to achieve in a day.  I threw out the idea of NSURL literals being represented by a @@ prefix, so the following line would compile:

```objc
NSURL *url = @@"http://apple.com"
```

**NOTE:** I'm not suggesting `NSURL` literals should become a new language feature of Objective-C – this merely serves a reasonable feature for academic exploration.

## Parsing: libparse

Armed with the knowledge that these new literals were available, I started exploring the libparse code in Clang.  ParseObjc.cpp seemed like a good place to start, which turned out to be correct and lead me to the rather aptly named [Parser::ParseObjCAtExpression](https://github.com/llvm-mirror/clang/blob/4d3db4eb6caa49a7cdbfe1798728ce4b23cd0b53/lib/Parse/ParseObjc.cpp#L2019) method.  The implementation of this method is obvious, determining the next token and delegating parsing to various methods depending of the type of expression encountered.  Our syntax requires a second @ token, so I added the following code to the switch statement:

```objc
case tok::at:
    // Objective-C NSURL expression
    ConsumeToken(); // Consume the additional @ token.
    if (!Tok.is(tok::string_literal)) {
      return ExprError(Diag(AtLoc, diag::err_unexpected_at));
    }
    return ParsePostfixExpressionSuffix(ParseObjCURLLiteral(AtLoc));
```

In english, if we find another @ token, we'll assume an NSURL literal and attempt to parse, by delegating to our new ParseObjCURLLiteral method. The implementation of [ParseObjCURLLiteral](https://github.com/scarnie/clang/blob/NSURL-literal/lib/Parse/ParseObjc.cpp#L2561) is again quite simple:

```objc
ExprResult Parser::ParseObjCURLLiteral(clang::SourceLocation AtLoc) {
    ExprResult Res(ParseStringLiteralExpression());
    if (Res.isInvalid()) return move(Res);

    return Owned(Actions.BuildObjCURLLiteral(AtLoc, Res.take()));
}
```

The first thing it does is attempt to parse a C-string literal using the existing ParseStringLiteralExpression method.  If the result of this is invalid, fail; otherwise, call our new [Actions.BuildObjCURLLiteral](https://github.com/scarnie/clang/blob/NSURL-literal/lib/Sema/SemaExprObjC.cpp#L143) method.  Actions is an instance of the Semantic Analysis class _Sema_ in libsema, responsible for generating the Abstract Syntax Tree (AST), which is consumed by the code generator in libcodegen.

## Semantic Analysis: libsema

This library is responsible for converting parsed code into an AST.  I looked at the [BuildObjCStringLiteral](https://github.com/scarnie/clang/blob/NSURL-literal/lib/Sema/SemaExprObjC.cpp#L306) and [BuildObjCNumericLiteral](https://github.com/scarnie/clang/blob/NSURL-literal/lib/Sema/SemaExprObjC.cpp#L306) methods to gain a better understanding of the responsibility of libsema.  These methods return an ExprResult which will ultimately be used by the code generator to write llvm IR. 

First this is to determine is how to generate an NSURL instance.  The [NSURL URLWithString:](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURL_Class/Reference/Reference.html#//apple_ref/doc/uid/20000301-BAJBBDIB) class method is the best candidate, following the lead of the other Obj-C literals.  This class method requires an NSString as it's first and only argument, so the first thing we need is to generate this expression.  As can be seen in the first few lines of our BuildObjCURLLiteral method

```objc
ExprResult Sema::BuildObjCURLLiteral(SourceLocation AtLoc, Expr *String) {
  StringLiteral *S = reinterpret_cast(String);
  if (CheckObjCString(S))
      return true;

  ExprResult ObjCString = BuildObjCStringLiteral(AtLoc, S);
```

We take our string expression and build and NSString. Next we need to confirm the existence of and cache the NSURL class declaration

```objc
if (!NSURLDecl) {
  NamedDecl *IF = LookupSingleName(TUScope,
                                   NSAPIObj->getNSClassId(NSAPI::ClassId_NSURL),
                                   AtLoc,
                                   LookupOrdinaryName);
  NSURLDecl = dyn_cast_or_null(IF);
  if (!NSURLDecl && getLangOpts().DebuggerObjCLiteral)
    NSURLDecl =  ObjCInterfaceDecl::Create (Context,
                                              Context.getTranslationUnitDecl(),
                                              SourceLocation(),
                                              NSAPIObj->getNSClassId(NSAPI::ClassId_NSURL),
                                              0, SourceLocation());

  if (!NSURLDecl) {
    Diag(AtLoc, diag::err_undeclared_nsurl);
    return ExprError();
  }

  // generate the pointer to NSURL type.
  QualType NSURLObject = CX.getObjCInterfaceType(NSURLDecl);
  NSURLPointer = CX.getObjCObjectPointerType(NSURLObject);
}
```

and finally the URLWithString: selector

```objc
if (!URLWithStringMethod) {
  Selector Sel = NSAPIObj->getNSURLLiteralSelector(NSAPI::NSURLWithString);
  URLWithStringMethod = NSURLDecl->lookupClassMethod(Sel);
}
```

**NOTE:** I implemented the `NSURL` features on the `NSAPI` class, which were easily determined by examining the other APIs exposed, such as NSArray and NSDictionary.

The remaining requirement of this method is to return an expression that will ultimately result in a call to objc_msgSend with the arguments: NSURL class, URLWithString: selector and NSString constant.  Conveniently, the ObjCBoxedExpr provides just what we need, resulting in this final call

```objc
SourceRange SR(S->getSourceRange());

// Use the effective source range of the literal, including the leading '@'.
return MaybeBindToTemporary(
                            new (Context) ObjCBoxedExpr(ObjCString.take(), NSURLPointer, URLWithStringMethod,
                                                        SourceRange(AtLoc, SR.getEnd())));

```

The SR (SourceRange) argument is used to associate this AST node with the source code it is generated from.  The ObjCBoxedExpr class contains all the pieces needed to execute the `objc_msgSend`, which will be later consumed by the code generator.  By reusing this class, we've avoided the need to write our own code generation.

## Code Generation

To conclude this article and clarify what happens with our AST node (ObjCBoxedExpr), lets take a look at libcodegen to see how our NSURL call is converted to executable code.  The CGObjC.cpp file contains a rather curiously named method, [EmitObjCBoxedExpr](https://github.com/scarnie/clang/blob/NSURL-literal/lib/CodeGen/CGObjC.cpp#L59), taking a single parameter ObjCBoxedExpr.  It is quite succinct, making it very easy to understand (code removed for clarity). Worth noting is this function requires the boxing selector be a class method.

```objc
  // Grab the NSString expression that will be the argument to URLWithString:
  const Expr *SubExpr = E->getSubExpr();

  // Grab the URLWithString: selector
  const ObjCMethodDecl *BoxingMethod = E->getBoxingMethod();
  Selector Sel = BoxingMethod->getSelector();

  // Generate a reference to the class pointer, which will be the receiver.
  // Assumes that the method was introduced in the class that should be
  // messaged (avoids pulling it out of the result type).
  CGObjCRuntime &Runtime = CGM.getObjCRuntime();
  const ObjCInterfaceDecl *ClassDecl = BoxingMethod->getClassInterface();
  llvm::Value *Receiver = Runtime.GetClass(Builder, ClassDecl);

  // adds the NSString to the call arguments list
  const ParmVarDecl *argDecl = *BoxingMethod->param_begin();
  QualType ArgQT = argDecl->getType().getUnqualifiedType();
  RValue RV = EmitAnyExpr(SubExpr);
  CallArgList Args;
  Args.add(RV, ArgQT);

  // Generates the llvm IR code to execute the objc_msgSend function
  RValue result = Runtime.GenerateMessageSend(*this, ReturnValueSlot(),
                                              BoxingMethod->getResultType(), Sel, Receiver, Args,
                                              ClassDecl, BoxingMethod);
  return Builder.CreateBitCast(result.getScalarVal(),
                               ConvertType(E->getType()));
```

## Limitations and Improvements

This implementation is fairly rigid; only allowing a single line NSString, whereas a more robust, production-quality implementation should support multi-line NSString declarations.  

As per the [overview for NSURL](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURL_Class/Reference/Reference.html#//apple_ref/doc/uid/20000301-4355), URLs understood are described in RFCs 1808, 1738 and 2732\.  Adding code to interpret the contents of the string and validate per these RFCs, reporting a warning to the engineer would add considerable value to this feature, much like the warnings provided when a format string and its arguments are potentially invalid.

## Source Code

Complete source to this series is available on github in my [clang repository](https://github.com/scarnie/clang) fork.

## Next Up

I may explore compiling a release build and using the compiler within Xcode as an alternate.  Suggestions are welcome; message me on twitter, [@stuartcarnie](http://twitter.com/stuartcarnie)

## Twitter

Follow me on twitter, [@stuartcarnie](http://twitter.com/stuartcarnie).