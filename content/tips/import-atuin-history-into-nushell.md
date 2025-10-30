---
title: "Import Atuin History into Nushell"
date: 2025-10-30T21:43:18Z
updated: 2025-10-30T21:43:18Z
tags: ["nushell", "atuin"]
---

If you want Nushell's per-directory auto-suggestions backed by the commands you've already captured in Atuin's SQLite3
history, you can read the full dataset and pipe it into `history import`.

{{< notice warning >}}
You must be using the SQLite3 backend for Nushell as the text version doesn't support current working directory (cwd).
{{< /notice >}}

<!--more-->

A one-liner to import your complete atuin history into Nushell

```nu
open ~/.local/share/atuin/history.db 
  | get history 
  | each {|v| 
    {
      start_timestamp:($v.timestamp | into datetime),
      duration:(if $v.duration < 0 { 0 } else { $v.duration } | into duration),
      exit_status:$v.exit,
      hostname:$v.hostname,
      cwd:$v.cwd,
      command:$v.command
    }
  } 
  | history import
```
