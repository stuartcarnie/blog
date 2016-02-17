+++
title = "Micro-benchmarking iOS devices"
date = 2010-01-24T22:58:00Z
updated = 2012-09-23T17:37:30Z
categories = ["ARM", "performance", "iphone"]
+++

**Update:** Added iPhone 5.  
**Update:** Added iPhone 4s, iPad 3<sup>rd</sup> gen.  
**Update:** Added iPhone 4, iPad 1<sup>st</sup> gen.  

I follow the excellent [weekly posts](http://www.mikeash.com/?page=pyblog/) by Mike Ash, and [entered a brief discussion](http://www.mikeash.com/?page=pyblog/friday-qa-2010-01-22-toll-free-bridging-internals.html#comment-fe3facc8db943d179b5c670f0da3be68) in comments about toll free bridging. In particular, the difference between calling a method via Objective-C (objc_msgSend) and it's equivalent CoreFoundation C call. Mike suggested adding it to [his original](http://mikeash.com/?page=pyblog/performance-comparisons-of-common-operations-iphone-edition.html) suite of tests, which lead to the following results.

## iPhone 5 (-mno-thumb)

### Custom Apple A6 ARM Cortex A15, up to 1.2GHz

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 0.3 | 3.1 |
| C++ virtual method call | 100000000 | 0.3 | 3.3 |
| Integer division | 100000000 | 1.1 | 10.9 |
| Objective-C message send | 100000000 | 1.4 | 13.7 |
| Float division with int conversion | 10000000 | 0.2 | 24.7 |
| Floating-point division | 100000000 | 2.5 | 24.8 |
| Objective-C objectAtIndex: | 10000000 | 0.4 | 35.9 |
| CF CFArrayGetValueAtIndex | 10000000 | 0.5 | 50.9 |
| 16 byte memcpy | 10000000 | 0.7 | 65.8 |
| 16 byte malloc/free | 10000000 | 4.8 | 482.7 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 533.4 |
| NSObject alloc/init/release | 100000 | 0.1 | 1169.0 |
| NSInvocation message send | 100000 | 0.1 | 1391.8 |
| 16MB malloc/free | 1000 | 0.0 | 13331.8 |
| Zero-second delayed perform | 1000 | 0.1 | 99329.1 |
| pthread create/join | 100 | 0.0 | 120390.0 |
| 1MB memcpy | 100 | 0.0 | 421517.1 |

* * *

## iPhone 5 (thumb)

### Custom Apple A6 ARM Cortex A15, up to 1.2GHz

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 0.3 | 3.1 |
| C++ virtual method call | 100000000 | 0.4 | 4.0 |
| Integer division | 100000000 | 1.1 | 10.8 |
| Objective-C message send | 100000000 | 1.4 | 13.6 |
| Float division with int conversion | 10000000 | 0.2 | 24.9 |
| Floating-point division | 100000000 | 2.6 | 26.4 |
| Objective-C objectAtIndex: | 10000000 | 0.4 | 35.6 |
| CF CFArrayGetValueAtIndex | 10000000 | 0.5 | 51.0 |
| 16 byte memcpy | 10000000 | 0.7 | 65.8 |
| 16 byte malloc/free | 10000000 | 4.7 | 474.3 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 513.2 |
| NSObject alloc/init/release | 100000 | 0.1 | 1183.1 |
| NSInvocation message send | 100000 | 0.1 | 1241.5 |
| 16MB malloc/free | 1000 | 0.0 | 12979.7 |
| Zero-second delayed perform | 1000 | 0.1 | 83574.5 |
| pthread create/join | 100 | 0.0 | 121289.2 |
| 1MB memcpy | 100 | 0.0 | 426971.7 |

* * *

## iPad 3 (thumb)

### Apple A5x ARM Cortex A9 1000MHz

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 1.1 | 11.5 |
| C++ virtual method call | 100000000 | 1.3 | 12.7 |
| Floating-point division | 100000000 | 2.6 | 26.1 |
| 16 byte memcpy | 10000000 | 0.3 | 26.2 |
| Float division with int conversion | 10000000 | 0.3 | 26.3 |
| Integer division | 100000000 | 2.9 | 28.7 |
| Objective-C message send | 100000000 | 3.6 | 35.5 |
| Objective-C objectAtIndex: | 10000000 | 0.7 | 69.0 |
| CF CFArrayGetValueAtIndex | 10000000 | 1.3 | 131.7 |
| 16 byte malloc/free | 10000000 | 4.3 | 433.9 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 600.2 |
| NSObject alloc/init/release | 100000 | 0.1 | 1235.4 |
| NSInvocation message send | 100000 | 0.3 | 2966.6 |
| 16MB malloc/free | 1000 | 0.0 | 11633.0 |
| Zero-second delayed perform | 1000 | 0.1 | 121336.0 |
| pthread create/join | 100 | 0.0 | 130293.3 |
| 1MB memcpy | 100 | 0.2 | 1662780.4 |

* * *

## iPad 3 (-mno-thumb)

### Apple A5x ARM Cortex A9 1000MHz

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 0.9 | 9.0 |
| C++ virtual method call | 100000000 | 1.1 | 11.1 |
| Integer division | 100000000 | 2.4 | 24.1 |
| Floating-point division | 100000000 | 2.6 | 26.1 |
| Float division with int conversion | 10000000 | 0.3 | 26.1 |
| 16 byte memcpy | 10000000 | 0.3 | 27.1 |
| Objective-C message send | 100000000 | 2.7 | 27.2 |
| Objective-C objectAtIndex: | 10000000 | 0.7 | 68.4 |
| CF CFArrayGetValueAtIndex | 10000000 | 1.0 | 103.3 |
| 16 byte malloc/free | 10000000 | 4.3 | 432.5 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 570.4 |
| NSObject alloc/init/release | 100000 | 0.1 | 1209.9 |
| NSInvocation message send | 100000 | 0.2 | 1682.2 |
| 16MB malloc/free | 1000 | 0.0 | 10251.2 |
| pthread create/join | 100 | 0.0 | 118494.2 |
| Zero-second delayed perform | 1000 | 0.1 | 121578.2 |
| 1MB memcpy | 100 | 0.2 | 1635983.3 |

* * *

## iPhone 4s (thumb)

### Apple A5 ARM Cortex A9 ~800MHz

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| C++ virtual method call | 100000000 | 1.1 | 11.3 |
| IMP-cached message send | 100000000 | 1.3 | 12.6 |
| Integer division | 100000000 | 3.1 | 31.4 |
| Float division with int conversion | 10000000 | 0.3 | 32.6 |
| Floating-point division | 100000000 | 3.3 | 32.6 |
| 16 byte memcpy | 10000000 | 0.3 | 32.6 |
| Objective-C message send | 100000000 | 3.4 | 33.8 |
| Objective-C objectAtIndex: | 10000000 | 0.9 | 85.9 |
| CF CFArrayGetValueAtIndex | 10000000 | 1.6 | 165.0 |
| 16 byte malloc/free | 10000000 | 5.4 | 542.2 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 753.2 |
| NSObject alloc/init/release | 100000 | 0.2 | 1511.7 |
| NSInvocation message send | 100000 | 0.2 | 2111.9 |
| 16MB malloc/free | 1000 | 0.0 | 19033.7 |
| pthread create/join | 100 | 0.0 | 142817.5 |
| Zero-second delayed perform | 1000 | 0.1 | 146302.7 |
| 1MB memcpy | 100 | 0.2 | 1787482.1 |

* * *

## iPhone 4 (-fthumb)

### Apple A4 ARM Cortex A8 ~800MHz

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 0.9 | 9.0 |
| C++ virtual method call | 100000000 | 1.0 | 10.2 |
| 16 byte memcpy | 10000000 | 0.4 | 36.2 |
| Integer division | 100000000 | 4.1 | 40.6 |
| Objective-C message send | 100000000 | 4.1 | 40.8 |
| Floating-point division | 10000000 | 0.9 | 89.4 |
| Objective-C objectAtIndex: | 10000000 | 1.1 | 105.8 |
| Float division with int conversion | 10000000 | 1.1 | 105.8 |
| CF CFArrayGetValueAtIndex | 10000000 | 1.7 | 168.1 |
| NSInvocation message send | 100000 | 0.1 | 550.8 |
| 16 byte malloc/free | 10000000 | 6.6 | 656.3 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 979.5 |
| NSObject alloc/init/release | 100000 | 0.4 | 4277.9 |
| 16MB malloc/free | 1000 | 0.0 | 20406.7 |
| pthread create/join | 100 | 0.0 | 139971.2 |
| Zero-second delayed perform | 1000 | 0.2 | 243883.3 |
| 1MB memcpy | 100 | 0.1 | 1150657.9 |

* * *

## iPhone 3GS (-fthumb)

### ARM Cortex A8 ~600MHz / 1.66 ns per cycle

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 1.2 | 11.7 |
| C++ virtual method call | 100000000 | 1.3 | 13.5 |
| 16 byte memcpy | 10000000 | 0.5 | 46.0 |
| Objective-C message send | 100000000 | 5.4 | 53.9 |
| Integer division | 100000000 | 6.3 | 62.9 |
| Floating-point division | 10000000 | 1.2 | 117.4 |
| Float division with int conversion | 10000000 | 1.4 | 138.2 |
| Objective-C objectAtIndex: | 10000000 | 1.4 | 140.1 |
| CF CFArrayGetValueAtIndex | 10000000 | 2.2 | 220.0 |
| 16 byte malloc/free | 10000000 | 6.4 | 642.6 |
| NSInvocation message send | 100000 | 0.1 | 723.0 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.1 | 1305.9 |
| NSObject alloc/init/release | 100000 | 0.6 | 5743.7 |
| 16MB malloc/free | 1000 | 0.0 | 16104.0 |
| pthread create/join | 100 | 0.0 | 185759.2 |
| Zero-second delayed perform | 1000 | 0.4 | 353519.4 |
| 1MB memcpy | 100 | 0.2 | 2170179.2 |

* * *

## iPhone 3GS (no thumb)

### ARM Cortex A8 ~600MHz / 1.66 ns per cycle

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 1.2 | 11.8 |
| C++ virtual method call | 100000000 | 4.3 | 42.9 |
| Objective-C message send | 100000000 | 5.9 | 59.2 |
| CF CFArrayGetValueAtIndex | 10000000 | 1.0 | 97.9 |
| Integer division | 100000000 | 9.8 | 98.4 |
| 16 byte memcpy | 10000000 | 1.1 | 109.3 |
| Floating-point division | 10000000 | 1.2 | 118.5 |
| Objective-C objectAtIndex: | 10000000 | 1.3 | 129.0 |
| Float division with int conversion | 10000000 | 1.4 | 142.6 |
| 16 byte malloc/free | 10000000 | 7.5 | 748.6 |
| NSInvocation message send | 100000 | 0.1 | 806.0 |
| NSObject alloc/init/release | 100000 | 0.5 | 4793.1 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.5 | 4953.1 |
| 16MB malloc/free | 1000 | 0.0 | 17969.2 |
| Zero-second delayed perform | 1000 | 0.2 | 211840.4 |
| pthread create/join | 100 | 0.0 | 214742.5 |
| 1MB memcpy | 100 | 0.3 | 3162774.6 |

* * *

## iPhone 3G

### ARM1176 ~412MHz / 2.4ns per cycle

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 3.9 | 38.6 |
| C++ virtual method call | 100000000 | 5.0 | 49.9 |
| Floating-point division | 10000000 | 0.8 | 81.3 |
| Float division with int conversion | 10000000 | 0.8 | 81.4 |
| 16 byte memcpy | 10000000 | 1.4 | 136.0 |
| Objective-C message send | 100000000 | 14.9 | 148.6 |
| Integer division | 100000000 | 16.2 | 162.2 |
| CF CFArrayGetValueAtIndex | 10000000 | 2.0 | 201.7 |
| Objective-C objectAtIndex: | 10000000 | 4.2 | 418.3 |
| NSInvocation message send | 100000 | 0.2 | 1833.2 |
| 16 byte malloc/free | 10000000 | 27.3 | 2729.8 |
| NSObject alloc/init/release | 100000 | 1.4 | 14179.1 |
| NSAutoreleasePool alloc/init/release | 100000 | 1.9 | 18956.7 |
| 16MB malloc/free | 1000 | 0.0 | 47811.3 |
| Zero-second delayed perform | 1000 | 0.8 | 803419.3 |
| pthread create/join | 100 | 0.1 | 1085830.0 |
| 1MB memcpy | 100 | 1.0 | 9902796.7 |

* * *

## iPad (-fthumb)

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 0.7 | 7.1 |
| C++ virtual method call | 100000000 | 0.8 | 8.1 |
| 16 byte memcpy | 10000000 | 0.3 | 27.7 |
| Objective-C message send | 100000000 | 3.2 | 32.3 |
| Integer division | 100000000 | 3.4 | 33.7 |
| CF CFArrayGetValueAtIndex | 10000000 | 0.6 | 58.8 |
| Floating-point division | 10000000 | 0.7 | 70.5 |
| Objective-C objectAtIndex: | 10000000 | 0.8 | 81.6 |
| Float division with int conversion | 10000000 | 0.8 | 83.1 |
| 16 byte malloc/free | 10000000 | 3.6 | 357.8 |
| NSInvocation message send | 100000 | 0.0 | 470.8 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.3 | 2957.0 |
| NSObject alloc/init/release | 100000 | 0.3 | 3080.2 |
| 16MB malloc/free | 1000 | 0.0 | 14824.2 |
| pthread create/join | 100 | 0.0 | 127386.2 |
| Zero-second delayed perform | 1000 | 0.2 | 225271.3 |
| 1MB memcpy | 100 | 0.1 | 1064566.2 |

* * *

## iPad (-mno-thumb)

### Apple A4 ARM Cortex A8 ~1GHz / 1 ns per cycle

| Name | Iterations | Total time (sec) | Time per (ns) |
| ---- | ---------- | ---------------- | ------------- |
| IMP-cached message send | 100000000 | 0.8 | 8.1 |
| C++ virtual method call | 100000000 | 2.2 | 21.8 |
| 16 byte memcpy | 10000000 | 0.3 | 28.2 |
| Objective-C message send | 100000000 | 3.2 | 32.5 |
| Integer division | 100000000 | 3.4 | 33.9 |
| CF CFArrayGetValueAtIndex | 10000000 | 0.6 | 55.8 |
| Floating-point division | 10000000 | 0.7 | 70.9 |
| Objective-C objectAtIndex: | 10000000 | 0.8 | 81.6 |
| Float division with int conversion | 10000000 | 0.8 | 82.8 |
| 16 byte malloc/free | 10000000 | 3.6 | 358.3 |
| NSInvocation message send | 100000 | 0.0 | 473.4 |
| NSAutoreleasePool alloc/init/release | 100000 | 0.3 | 3017.6 |
| NSObject alloc/init/release | 100000 | 0.3 | 3071.8 |
| 16MB malloc/free | 1000 | 0.0 | 14623.6 |
| pthread create/join | 100 | 0.0 | 128674.6 |
| Zero-second delayed perform | 1000 | 0.3 | 255627.5 |
| 1MB memcpy | 100 | 0.1 | 1063407.5 |

Note that I did reduce the iterations from the original tests, so whilst the total times are significantly less, the iteration times are still a reflection of overall performance.  Compared to Mike's results, these show that the IMP method is indeed faster as expected, but this was only after I changed to a release build.  I also compiled these with Thumb disabled unless otherwise specified.
I've recently watched some iTunes U videos released by Apple on optimizing OpenGL ES 2.0 and a key takeaway was that the Cortext A8 architecture should always be compiled with thumb enabled. The Cortex CPU uses the newer Thumb-2 instruction set, which has native instructions for floating point. The benefit of Thumb is reduced code size and potentially better performance by utilising the I-cache.

## Observations

*   I've estimated the iPhone 4 CPU to be running at 800MHz.  Looking at the increased speed over the 3GS for a number of benchmarks, the average is 1.333x increase.  Multiplying 600MHz x 1.333 yields roughly 800MHz as the clock speed.
*   The IMP-cached message send is significantly faster on the newer Cortex CPU.  I have read of improvements in the branch prediction logic, which is particularly important due to the greater penalty of a misprediction in the longer A8 pipeline.  The code for executing the call is
      `blx r8`
    r8 contains the target address of the function, and remains so for the duration of the test.
*   For the 3GS, the Objective-C message send is very close to the C++ virtual method call.  I ran this test several times, and the behaviour didn't change.  The virtual method call is an indirect load of the pc register
      `ldr pc, [r3]`
    Without being able to access the [PMC registers](http://infocentre.arm.com/help/topic/com.arm.doc.ddi0344j/Bgbighbf.html), I can't be sure of mispredictions; however, I know that 9 instructions are executed every iteration in the C++ test.  That suggests around 15ns / iteration; but, we're at 42.9\.  Adding an additional 13 cycles every iteration (21.58ns) for a mispredition would get us to 37ns / iteration - much closer.  Stepping in to the objc_msgSend function finds the cached method on the first pass, totaling 28 instructions per iteration.  Given there are significantly more instructions for the Objective-C call, we're probably seeing the benefits of the dual—issue architecture.
*   Memory performance of the 3GS is significantly higher.  I've done some other micro-benchmarks, showing 2<sup>nd</sup> gen around 200 MB/s and 3<sup>rd</sup> gen around 800MB/s.  With some very well placed cache-preloads, I've actually pushed the ARM1176 to almost 300MB/s.
*   Calling the objectAtIndex: using CoreFoundation API is 2x faster on older devices; however, the gap is less significant with the newer hardware.  We've seen significant improvements to the objc_msgSend performance on the 3GS, which undoubtedly is making up much of the gap.
*   Floating point performance for scalar operations is slightly slower on the newer device.

Source code for this test is available [here](http://gist.github.com/285663).
