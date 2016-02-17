+++
title = "lldb: VI mode and tab-completion"
date = 2012-06-23T23:32:00Z
updated = 2012-06-23T23:33:35Z
categories = ["osx"]
blogimport = true 
+++

lldb uses libedit, which is a BSD licensed alternative to GNU readline. A feature of libedit is the ability to configure per-application settings for command-line bindings via ~/.editrc. Detailed documentation for this configuration file can be found using `man editrc` or via online documentation, such as [developer.apple.com](https://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man5/editrc.5.html#//apple_ref/doc/man/5/editrc).

As I prefer VI bindings, I initially configured my .editrc as follows, to replace the default emacs bindings:

```
lldb:bind -v
```

Unfortunately, when I re-ran lldb, tab-completion was not functioning.  I added the following line, which is exported in libedit, and what I assumed would be the default completer function:

```
lldb:bind ^I rl_complete
```

Although tab-completion started working again, I was seeing frequent segfaults in lldb, along with an error that it could not bind the rl_complete command.  How was I going to find this elusive tab-completion command?  I could dig through lldb source, but figured there must be a quicker way.  I reviewed all the commands in the editrc docs and found 

```
ed-command
    Editline extended command.
```

I cleared my .editrc and added:

```
lldb:bind ^P ed-command
```

tab-completion was working again, and pressing ^P presented me a :, puting me in Editline's extended command mode. Typing _bind_ listed all the current bindings; I was looking for ^I, which showed up as

```
"^I"           ->  lldb_complete
```

_lldb_complete_ was the elusive command I needed to fix tab-completion in VI mode, so I settled on the following for my .editrc

```
lldb:bind -v
lldb:bind ^I lldb_complete
lldb:bind ^P ed-command
```

I suspect this technique will come in handy for other programs that use libedit and a non-standard completion function, hence this long post.
