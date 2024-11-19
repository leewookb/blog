---
title: "[TIL] Key Exchange"
author: "Wook Lee"
date: "2024-07-15"
tags: ["Today I Learned"]
---

#### What is a Key Exchange?

A key exchange is a method used in cryptography to securely exchange cryptographic keys between two parties. These keys are then used to encrypt and decrypt messages, ensuring that the communication remains private and secure. The primary challenge in key exchange is to do this securely over an insecure communication channel, such as the internet.

#### What is Diffie-Hellman Key Exchange?

The Diffie-Hellman key exchange is one of the earliest and most widely used methods for securely exchanging cryptographic keys. It allows two parties to generate a shared secret over an insecure channel without having to transmit the secret itself.

**_Simplified example_**: Alice and Bob want to talk securely. They want to establish a common key, so they can use symmetric cryptography, but they don't want to use key exchange with asymmetric cryptography. This is where Diffie Hellman Key Exchange comes in.

Alice and Bob both have secrets that they generate, let's call these `A` and `B`. They also have some common material that's public, let's call this `C`.

Alice and Bob will combine their secrets with the common material and form `AC` and `BC`. They will then send these to each other, and combine that with their secrets to form two identical keys, both `ABC`. Now they can use this key to communicate.

---

The **_Diffie-Hellman key exchange_** was explained in detail by the famous YouTube channel, `Computerphile`, in the following video:

{{< youtube NmM9HA2MQGI >}}
