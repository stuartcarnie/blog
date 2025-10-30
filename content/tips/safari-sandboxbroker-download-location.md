---
title: "Safari not respecting File download location preference?"
date: 2025-10-29T21:49:01Z
tags: ["macos", "safari", "sandbox"]
aliases:
  - /safari-sandboxbroker-download-location/
---

When Safari ignores your preferred **File download location** setting, terminating the `com.apple.Safari.SandboxBroker` process can resolve the issue.

Credit to Saagar Jha, who noted the problem and solution:

{{< x user="_saagarjha" id="1959541361503535216" >}}

----

Open a terminal and run the following command to kill the `SandboxBroker` process:

{{< tabs title="" >}}
{{% tab title="Bash" %}}
```sh
killall com.apple.Safari.SandboxBroker
```
{{% /tab %}}
{{% tab title="Nushell" %}}
```nushell
kill -9 ...(ps | find "com.apple.Safari.SandboxBroker" | $in.pid)
```
{{% /tab %}}
{{< /tabs >}}

{{< notice tip >}}
This technique only touches Safari's helper daemon; it doesn't delete preferences or reset Safari itself.
{{< /notice >}}
