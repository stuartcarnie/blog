+++
title = "Eval Expression service for OS X"
date = 2011-04-09T21:44:00Z
updated = 2011-04-10T01:31:03Z
categories = ["osx", "utility"]
blogimport = true 
+++

**Eval Expression** is a Mac OS X service to evaluate the selected text of any text field as a Ruby expression.  My instinct was to choose Perl, however Ruby offers binary in addition to decimal, hex and octal numerical literals.  The service becomes infinitely more useful if you assign it a global shortcut in Keyboard preferences.  In my case I assigned a combination that seemed obvious, ⌘=

![Keyboard preferences](http://lh5.ggpht.com/_WTgxY9AxbJk/TaE1i0ngklI/AAAAAAAAAnU/yDUu3uOqs9Y/Keyboard%20preferences.png?imgmax=800)

It came about as I was working on some layout in Xcode 4 / Interface Builder, and needed to adjust the Y position of a view by a specific number of units.  I'm lazy, and figured the computer should do the work, so I typed 768-35 and tabbed out.  Unlike Acorn and Opacity, which evaluates the expression in numerical entries automatically, Xcode simply complained.  Not happy, I created this service and it works like a charm.  Some examples:

*   768-35 ⌘= 733
    *   calculator
*   0b11010 ⌘= 26
    *   conversion to decimal
*   0x1f.to_s(2) ⌘= 11111
    *   hex to binary
*   2**3 ⌘= 8
    *   exponent
*   (0x8000 | 0xf1).to_s(16) ⌘= 80f1
*   "-+" * 5 ⌘= -+-+-+-+-+
    *   expansion
*   Array(1..15).join ',' ⌘= 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15
    *   lists

Download the Automator zip file from [github](https://github.com/downloads/scarnie/sgc_general/Eval%20Expression.zip), unzip and install to your ~/Library/Services folder.

A quick video of the service in action (note that I'm using my ⌘= shortcut to evaluate within Mars Edit):

<object type="application/futuresplash" width="425" height="344"><param name="movie" value="http://www.youtube.com/v/K-gP9ToAPTU?hl=en&amp;fs=1"><param name="allowFullScreen" value="true"><param name="allowscriptaccess" value="always"><embed type="application/futuresplash" width="425" height="344" src="http://www.youtube.com/v/K-gP9ToAPTU?hl=en&amp;fs=1" allowscriptaccess="always" allowfullscreen="true"></object>
