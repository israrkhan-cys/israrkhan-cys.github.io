---
title: "Simfs- Simulated Linux filesystem"
date: 2025-05-28
draft: false
tags: ["linux, C++ , Terminal"]
summary: "Used different Data structure(e.g n-ary tree) concepts to simulate Linux filesystem. you get terminal to interact with it ."
coverImage: "cover.png"
---

## Overview

# simfs (v1.0)

##  What is simfs?

**simfs** is a Linux file system simulator built from scratch in C++. It started from a simple observation: Linux's hierarchical file system is essentially a tree data structure.

When you navigate `/home/user/docs/file.txt`, you're traversing a tree:

This project implements that tree structure practically, with real file operations and some API integrations as bonus features.
It gives a Terminal and you can use different basic linux cammands to interact with the system.

##  Features

### Core File System
- **Hierarchical storage** using n-ary trees
- **Complete path resolution** with `.` and `..` support
- **File operations**: create, read, update, delete, move, copy
- **Recursive search** with wildcard pattern matching (`*.cpp`, `project*`)
- **Memory management** with RAII cleanup


###  Technical Highlights
- Modular C++ architecture
- libcurl for HTTP requests
- Custom JSON parsing
- Build automation
- Terminal UI with ANSI colors
- 
### APIs Integrations  dont mind this it was not the main part of project  
- **Weather API** - Real-time weather 
- **Crypto API** - Live cryptocurrency prices
- **HTTP client** implementation using libcurl
- **Response caching** to reduce API calls

### Try it Yourself 
### You can compile it using the bash script with it it is much simpler to use.  
```bash
# Clone and build
git clone https://github.com/israrkhan-cys/simfs.git
cd simfs
./build.sh

# Run
./simfs
```
### If you have any suggestion or feedbacks for this project hit me up on linkden.

