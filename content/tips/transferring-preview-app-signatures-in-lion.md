---
title: "Transferring Preview app signatures in Lion"
date: 2011-08-03T22:45:00Z
updated: 2011-09-21T10:56:57Z
tags: ["macos"]
aliases:
  - /transferring-preview-app-signatures-in-lion/
---

Lion introduced a great [new feature](http://www.apple.com/macosx/whats-new/features.html#preview) that allows you to [capture your signature](http://www.cultofmac.com/you-can-now-sign-pdfs-using-lions-preview-app-screenshots-how-to/100237) via an attached camera and store it in an encrypted form for later use.  Therein lies the problem; you must have an attached camera.

I have a Mac Pro, and wanted to use the signatures I captured on my Macbook Pro.  Following these steps, you can transfer the encrypted signatures over.

On your machine endowed with the power of sight:

1.  Go ahead and capture the signatures in Preview.
2.  Quit Preview
3.  Browse to ~/Library/Containers/com.apple.Preview/Data/Library/Preferences
    *   In Finder, click the _Go_ menu and hold the option (⌥) key to show the Library folder, alternatively
    *   press ⌘+⇧+G whilst Finder is active and enter the path above to directly navigate
4.  Copy thecom.apple.Preview.signatures.plist
5.  Launch KeychainAccess
6.  Ensure the _login_ keychain is selected and the _Passwords_ category
    ![Copying Preview signatures password from keychain](http://lh3.ggpht.com/-z8tRB-j6kdo/Tjov5yUdYZI/AAAAAAAAAqE/OKZt52E80_Q/Keychain-Preview-Password.png?imgmax=800 "Keychain-Preview-Password.png")
7.  Right-click the _Preview Signature Privacy_ password and select Copy Password to Clipboard.  This is the password used to encrypt the signature images.
8.  Paste it into a text editor and save the file.  You'll need to transfer this to your other computer(s)

On your machine you wish to transfer the signatures to:

1.  If you haven't already, launch Preview, open preferences, and select the Signatures tab to ensure the default configuration files and folders have been created.  Exit Preview.
2.  Browse to ~/Library/Containers/com.apple.Preview/Data/Library/Preferences
3.  Copy thecom.apple.Preview.signatures.plist to the folder, overwriting any existing file
4.  Launch KeychainAccess
5.  Locate the _Preview Signature Privacy_ password in the _login_ keychain and double click to edit
    ![Replace the Preview Signature Password](http://lh3.ggpht.com/-BB6bmWMvymU/TjoxoHRQJeI/AAAAAAAAAqM/pdQcMbIt4o8/Replace%252520Preview%252520Signature%252520Privacy%252520password%252520dialog.png?imgmax=800 "Replace Preview Signature Privacy password dialog.png")
6.  Click the _Show password_ checkbox and paste the password you copied from your original machine.
7.  Click _Save Changes_ and you're done
