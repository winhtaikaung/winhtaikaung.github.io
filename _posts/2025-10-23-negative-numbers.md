---
title:  The Day I Learned How Computers See Negative Numbers : A 4-Bit Journey into Two's Complement
description: Understanding how the negative numbers were stored in binary system. 
author: winhtaikaung 
date: 2025-10-23 11:33:00 +0800
categories: [CS]
tags: [Binary,NumberSystem, CS]
pin: false
math: true
mermaid: true
 
---

## The Day I Learned How Computers See Negative Numbers: A 4-Bit Journey into Two's Complement

**Abstract:** It started with a simple question from a colleague: "Do you know how computers actually handle negative numbers?" I'll admit, with a sheepish grin, that I didn't have a ready answer. It was one of those foundational concepts I'd taken for granted. This simple query sent me down a fascinating rabbit hole, using a tiny 4-bit system to understand a huge concept. What I discovered was an elegant solution to a fundamental problem in computing: the representation of negative numbers in a world of ones and zeros.

### The Binary Basics in a 4-Bit World

At the heart of all modern computing lies the binary system. With a 4-bit system, we can represent 2⁴, or 16, different values (from 0 to 15). For example, the number 5 is represented as `0101`. While this is perfect for positive numbers, it begs the question: how do you show a "minus" sign when you only have ones and zeros?

### Attempt 1: The Obvious Flaw of Sign and Magnitude

My first thought was the most intuitive one: let's use the first bit, the most significant bit (MSB), to indicate the sign. We could say `0` is for positive numbers and `1` is for negative numbers. The remaining 3 bits would represent the value (the magnitude). This method is called **Sign and Magnitude**.

Using this logic for our 4-bit system:
*   **+5** would be `0101` (The `0` means positive, and `101` is 5).
*   **-5** would be `1101` (The `1` means negative, and `101` is 5).

While simple on the surface, this approach has a critical flaw: the number zero. In this system, we have:
*   `0000` for +0
*   `1000` for -0

This dual representation of zero complicates calculations and requires more complex hardware to check for both conditions. Furthermore, performing simple arithmetic is awkward and requires different logic depending on the signs of the numbers.

### Attempt 2: A Step Closer with One's Complement

The next idea in the evolution of representing negative numbers is the **One's Complement** system. Here, to represent a negative number, you take its positive binary form and simply flip all the bits (0s become 1s and 1s become 0s).

Let's try this with -5 in our 4-bit system:
*   The binary for +5 is `0101`.
*   Flipping the bits gives us `1010`. So, -5 is `1010`.

This was an improvement, making some arithmetic operations a bit easier. However, it still suffered from the same core problem as Sign and Magnitude: two representations for zero.
*   `0000` represents +0.
*   `1111` (the flip of `0000`) represents -0.

This "negative zero" still created headaches for hardware designers.

### The Elegant Solution: Two's Complement

This brings us to the system that is the standard in virtually all modern computers: **Two's Complement**. This clever method solves the dual-zero problem and makes computer arithmetic incredibly efficient.

The process is simple: to find the negative representation of a number, you first find its One's Complement (flip the bits) and then **add one**.

Let's find -5 again, this time with Two's Complement:
1.  Start with **+5** in binary: `0101`
2.  Find the **One's Complement** by flipping the bits: `1010`
3.  **Add 1** to the result: `1010 + 1 = 1011`

So, in a 4-bit Two's Complement system, **-5** is represented as `1011`.

### Why Two's Complement Is King

The genius of Two's Complement is that it turns subtraction into addition, meaning the computer's processor needs less-complex hardware. To subtract a number, you just add its two's complement.

Let's demonstrate by calculating **7 - 5**, which is the same as **7 + (-5)**.

*   **7** in 4-bit binary is `0111`.
*   We just found that **-5** in 4-bit Two's Complement is `1011`.

Now, let's add them:

```
  0111  (7)
+ 1011  (-5)
-------
 10010
```

Because we are in a 4-bit system, we simply **discard the carry-out bit** (the far-left '1'). The remaining 4 bits are `0010`, which is the binary for **2**. The answer is correct! This unified approach to addition and subtraction is a massive win for hardware efficiency.

Crucially, the Two's Complement system has only one representation for zero. If we take the two's complement of `0000`, we flip the bits to get `1111`, and adding one gives `10000`. Again, we discard the carry bit, leaving `0000`. No more ambiguity.

This journey, sparked by a simple question, revealed the elegant engineering behind a core concept of computing. By limiting our view to a simple 4-bit system, the power of Two's Complement becomes clear. It's a system that not only got rid of the confusing "negative zero" but also simplified the very logic that powers our digital world. The most efficient solutions are often the most clever ones.

So, the next time you see a negative number on your screen, you'll know the clever binary ballet happening behind the scenes.