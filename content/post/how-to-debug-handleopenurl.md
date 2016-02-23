+++
title = "How to debug handleOpenURL"
date = 2009-01-30T23:33:00Z
updated = 2009-04-18T16:51:25Z
categories = ["debugging", "xcode", "iphone"]
+++

I've seen a number of questions on the Apple iPhone developer forums asking how to debug the UIApplication handleOpenURL message. I finally had a complex scenario that I needed to debug, and came up with the following solution. Note that this example has been tested using the simulator only. Firstly, you'll need to grab the two 'DebugSupport' files from my google code repository [here](http://code.google.com/p/iphone-sdk-examples/source/browse/#svn/trunk/utility/debugging). Include them in your project and modify your handleOpenURL message as follows:

```mm
-(BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url {
 [DebugSupport waitForDebugger];
 ...
 // place breakpoint after the above line
}
```

The call to `[DebugSupport waitForDebugger]` shows a `UIAlertView`, which will wait until you confirm by clicking the OK button. You'll notice that the prompt tells you the process ID. Don't click OK yet. Return to Xcode and from the Run menu, choose Attach To Process | Process ID... Enter the PID given to you from the alert box and Xcode will attach and enable all the breakpoints. Obviously, remove this from production code. Enjoy.
