---
title: "ZephShell- Unix shell built in C"
date: 2026-04-15
draft: false
tags: ["shell, C, system"]
summary: "A Unix shell built from scratch in C to understand how shells actually 
work under the hood, process creation, command parsing, and execution 
from first principles.
"
coverImage: "cover.png"
---
## Intro

After studying Operating systems course and learning about system calls and low level stuff i got interested in How shell actually works in linx so i start building it in C.

## Resources i used 
- [Write a Shell in C — Stephen Brennan](https://brennan.io/2015/01/16/write-a-shell-in-c/)
- Book: The Linux Programming Interface by Michael Kerrisk



## How It Works

1. Reads user input with `getline`
2. Parses command and arguments using `strtok`
3. Forks a child process with `fork()`
4. Child executes the command via `execvp()`
5. Parent waits for child to finish with `wait()

## Feaures
For it deos most of the basic work i am planning to add more features like pipes , redirection, and many other cool features.

## Tech Stack

- Language: C
- Platform: Linux
- Tools: GCC, GDB, Make


## Links

- [GitHub](https://github.com/israrkhan-cys/zephshell)
