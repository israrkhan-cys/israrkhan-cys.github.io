---
title: "My First Three Linux Kernel Patches: One Accepted, One Duplicate, One Real Bug"
date: 2026-10-25
draft: false
summary: "What I learned from sending three patches to the Linux staging tree: a duplicate fix, a review that exposed a real concurrency bug, and a first patch accepted into staging-next."
tags: ["Linux", "Kernel", "Open Source", "Staging", "Beginner"]
coverImage: "cover.png"
---



After finishing the Linux Foundation's *A Beginner's Guide to Linux Kernel Development* (LFD103) course, I wanted to send real patches instead of only reading about the process. I've now sent three to the staging tree. They went three different ways, and each taught me something different.

## Why staging drivers

The `drivers/staging` tree holds drivers that aren't up to kernel quality yet. It's full of small, well-defined cleanup tasks, which makes it a good place to learn the contribution workflow without needing to understand a whole subsystem. The patches here all touch the `rtl8723bs` Wi-Fi driver and a staging video driver.

## The workflow in short

Every patch followed the same loop:

1. Find a problem, either a `checkpatch.pl` warning or something I noticed while reading the code.
2. Make a small change and build it.
3. Run `scripts/checkpatch.pl` on the patch.
4. Find the right maintainers with `scripts/get_maintainer.pl`.
5. Send it with `git send-email` and wait for replies.

<!-- Add one or two sentences about how you set up git send-email and what gave you trouble, if anything. -->

## Patch 1: a duplicate

I noticed `msleep()` being called with values under 20 ms in the `rtl8723bs` power-control code. For short delays `msleep()` can sleep much longer than requested, so the kernel docs recommend `usleep_range()` instead. I changed it and sent it to the list, addressed to Greg Kroah-Hartman.

The reply was that someone had already submitted the same fix back in June. My patch was dropped as a duplicate.

**What I learned:** check that the fix doesn't already exist before you send it. Search the mailing list archive on lore.kernel.org and look at the recent history of the file in the `staging-next` branch. It stung a little, but it confirmed I could spot a real issue on my own. Someone else just got there first.

## Patch 2: a review that was better than an acceptance

My second patch added comments to mutex and spinlock definitions in `staging: most: video`, because `checkpatch` warns about locks without a comment explaining what they protect.

Dan Carpenter replied, and his answer wasn't "applied". He pointed out that the comment I wrote for `list_lock` would hide a real concurrency bug in `comp_vdev_read()`, and he'd rather keep the `checkpatch` warning in place until the bug itself is fixed.

**What I learned:** `checkpatch` warnings are hints, not the goal. Silencing a warning with a comment that papers over a bug makes the code worse than leaving the warning. The honest fix is to understand why the lock is there and whether it's used correctly. I'm now reading `comp_vdev_read()` to understand the problem he described.
<!-- Update this paragraph once you know more about the bug, or if you have sent a fix. -->

## Patch 3: accepted

My third patch was the least exciting one, and it's the one that went through. I ran `checkpatch.pl` on `rtw_mlme_ext.c` in the `rtl8723bs` driver and saw two kinds of `CHECK` messages: braces should be used on all arms of this statement, and unbalanced braces around an else statement. The kernel coding style says that if one branch of an `if`/`else` needs braces, all branches should use them.

I fixed five places in the file. Each looked like this:

```c
-       } else
+       } else {
                channel_set[chanset_size].scan_type = SCAN_ACTIVE;
+       }
```

The patch changes 10 lines added and 8 removed, with no change in behaviour, which I said in the commit message. I sent it on 14 September 2026. On 2 October I got an email from Greg Kroah-Hartman saying it had been added to the `staging-next` branch of his staging tree. It will appear in `linux-next` and be merged in the next merge window.

You can read the accepted patch [on the mailing list](https://patch.msgid.link/20260914135639.28269-1-7israr.work@gmail.com).

**What I learned:**
- One small, mechanical, clearly scoped change is easier to review and accept than anything clever.
- Say what the patch does and that it doesn't change functionality, so the reviewer doesn't have to guess.
- After the patch is accepted, the maintainer adds tags such as `Link:` and their own `Signed-off-by:`. That's the point where it becomes part of the project's history.

## What I'd do differently

- Search for existing fixes first, every time.
- Treat reviewer comments as the real lesson, even when the answer is "not like this".
- Write commit messages in a more polished, consistent style. My first accepted one was informal, and that's worth tightening.

## What's next

I'm going to keep sending small patches while I get more comfortable reading kernel code. I also want to understand the `comp_vdev_read()` issue well enough to propose a proper fix. If you're starting out too, the LFD103 course and the `staging` tree are a good place to begin.
