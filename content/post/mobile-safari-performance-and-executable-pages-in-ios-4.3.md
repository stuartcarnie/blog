+++
title = "Mobile Safari performance and executable pages in iOS 4.3"
date = 2011-03-17T15:12:00Z
updated = 2011-03-17T16:24:45Z
categories = ["iOS"]
+++

DaringFireball recently posted [his thoughts](http://daringfireball.net/2011/03/nitro_ios_43) as to why the Javascript performance of Mobile Safari is faster than those launched from SpringBoard or within existing applications which use a UIWebView.  I don't think it's quite that complicated.

My thoughts are it is one of two things:

* Apple is concerned about security (which would support John's theory)
    * Mobile Safari is binding to a newer and/or private version of Webkit, and Springboard was an oversight
* Apple is concerned about breaking existing applications
    * I would argue this makes the most sense, and was probably a very deliberate action by Apple, given enabling Nitro is a significant enough change to a core framework that could break existing applications.
    * Perhaps a future update will allow developers to _opt-in_ to the faster Javascript engine either via a plist setting or more likely, a new API on the UIWebView class.
    * Would also explain the oversight in Springboard, if it was not opted in.

There are other implications for allowing 3rd parties to use Nitro, most importantly _battery life._ If Javascript executes up to 3x faster, the CPU is running 3x less to to execute equivalent Javascript, allowing the CPU to go to low power mode faster.

Gruber also makes the following statement:

> Apple, as of iOS 4.3, trusts Mobile Safari enough to allow this. The upside is that Mobile Safari is now significantly faster. The downside is that any security exploits against Mobile Safari now potentially allow worse things to happen than before.

I would disagree, as many of the previous Jailbreak exploits in iOS such as [this example](http://daringfireball.net/linked/2010/08/02/jailbreakme) were via Mobile Safari, without a JIT engine, simply exploiting the stack and other typical attacks to _elevate privileges_.  As Apple continues to get a handle on these _security_ issues, running dynamic code shouldn't be a problem.

## What is my basis for this assumption?

A few weeks ago, when I heard Apple's announcement that Nitro was coming to 4.3, I immediately had to retest some scenarios that were not previously supported on iOS.  Prior to iOS 4.3, calling the mprotect function and attempting to set the PROT_EXEC flag, marking the page as executable would _fail_ when run on the device_._ Quite simply, the API would return an error code.  This suggested that likely something in the kernel was locked down or this API had specific code to disallow pages to be marked as executable.  Knowing that the Nitro engine must execute in user space or it would be a serious hack in iOS, Apple had to enable support for executable pages.  I went ahead and [created an example](https://gist.github.com/855607), ran it on my iPhone 4 and viola!  Essentially this example creates an 'increment' function in 32-bit ARM on the fly that adds one to the parameter and returns the result.




I [responded](https://twitter.com/stuartcarnie/status/43762981863034880) to Miguel de Icaza's [tweet](https://twitter.com/migueldeicaza/status/43127184440836096), who also wondered it might now be possible.  Further discussions with Miguel also [identified](https://twitter.com/migueldeicaza/status/48517553395466240) that Apple has only partially opened up executable page support, whereby pages can either be read/write or read/execute.  Unfortunately the majority of existing JIT engines expect RWX in order to patch executable pages without the need to change the state from rx » rw, patch code and then rw » rx.  Critical in multi-threaded scenarios, as code could conceivably jump to a page that was being patched and fault due to the absence of the execute flag.


