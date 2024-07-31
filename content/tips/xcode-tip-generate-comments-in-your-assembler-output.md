---
title: "Xcode Tip: Generate comments in your assembler output"
date: 2009-03-07T21:56:00Z
updated: 2009-03-07T21:57:40Z
tags: ["xcode"]
aliases:
  - /xcode-tip-generate-comments-in-your-assembler-output/
---

To make it easier to find the assembly generated when you 'Show Assembly Code', embed comments using:  

```asm
asm("# your comment")
```
