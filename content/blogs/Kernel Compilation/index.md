---
title: "Compiling the Linux Kernel from Source on Ubuntu"
date: 2026-06-24
draft: false
tags: ["linux", "kernel", "ubuntu", "source"]
summary: "Compiled linux mainline kernel from source and replace my current kernel with the new mainline kernel"
coverImage: "cover.png"
keywords: ["linux", "kernel", "ubuntu", "source", "compilation"]
---

# Compiling the Linux Kernel from Source on Ubuntu

I recently went through the process of building the Linux kernel from source on Ubuntu, as part of a kernel development course I've been working through. Here's a simple breakdown of how it went.

## Why compile your own kernel?

The distro-provided kernel works fine for everyday use, but if you're getting into kernel development — testing patches, exploring new config options, or just understanding how the pieces fit together — building your own from source is a rite of passage. Here's how I did it.

## Step 1: Install the dependencies

Before touching the source, you need the tools that actually build the kernel:

```bash
sudo apt update
sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev bc
```

## Step 2: Clone the kernel source

I grabbed the mainline kernel directly from kernel.org:

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
```
> Note: Full clone (no --depth): ~8.7 GB  full history back to 2005, all branches and tags --depth=1: ~2 GB — just the latest snapshot, no history. so if you have slow internet or limited space sure to use --depth=1 in cloning 

## Step 3: Start from your current config

Rather than configuring a kernel from a blank slate, I copied the config of my currently running Ubuntu kernel into the source tree:

```bash
cp /boot/config-$(uname -r) .config
```

This gives the new kernel a sensible starting point instead of building everything from scratch.

## Step 4: Update the config

Since the new kernel version has options that didn't exist when the old config was written, I ran:

```bash
make olddefconfig
```

This fills in sensible defaults for anything new. If I wanted to manually enable or disable specific features, I could also open the interactive menu:

```bash
make menuconfig
```

## Step 5: Build it

This is the part that takes a while:

```bash
make -j$(nproc)
```

`-j$(nproc)` tells make to use all available CPU cores, which speeds things up considerably. Depending on your machine, this can still take anywhere from 20 minutes to over an hour.

## Step 6: Install modules and the kernel

Once the build finished successfully:

```bash
su -c "make modules_install install"
```

On Ubuntu, this single command quietly does more than it looks like — it copies the new kernel into `/boot`, installs the kernel modules, and also triggers hook scripts that regenerate the initramfs and update GRUB automatically. That's a Debian/Ubuntu-specific convenience; other distros like Arch require running those steps manually.

## Step 7: Reboot

```bash
sudo reboot
```

After rebooting, a quick check confirms the new kernel is running:

```bash
uname -r
```

## Final thoughts

The whole process is more approachable than it sounds — most of the "work" is just installing dependencies and waiting for the build to finish. The real value is in understanding *why* each step exists, not just running the commands.