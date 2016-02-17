+++
title = "Renew Apple developer certificates with OpenSSL"
date = 2011-02-02T16:30:00Z
updated = 2011-02-02T19:12:14Z
categories = ["apple", "iOS"]
+++

I like to reuse the same private keys when generating a signing request to renew my Apple developer certificates.  Unfortunately you can't do this with Keychain Access, as it won't save the signing request file after you step through the wizard.  OpenSSL is your friend.

Open Keychain Access, RMB on the key your wish to use and click _Export "[Key Name]"_.  Save it as a .p12 file with a strong password.  In my case it was StuartCarnie.p12.

Open a terminal session and convert the .p12 to a PEM with the following command

```bash
openssl pkcs12 -in StuartCarnie.p12 -out StuartCarnie.pem
```

You will be prompted  for your .p12 password, and also a password to encrypt your .pem.

Now generate the signing request with the following command.

```bash
openssl req -new -key StuartCarnie.pem -out StuartCarnie.csr
```

You'll be prompted for a few questions to place in the signing request, such as the country, etc.  At the very least, enter the Common Name (your name) and Email Address.  One you've completed this step, the .csr file can be submitted to Apple.
