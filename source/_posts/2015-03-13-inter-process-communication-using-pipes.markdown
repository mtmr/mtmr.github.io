---
layout: post
title: "Inter-Process Communication using Pipes"
date: 2015-03-13 09:13:54 +0530
comments: true
categories: ["OS", "linux"]
---


In the last post, we saw forking and executing another program in the child process after forking. Now let us look at basic interprocess communication using pipes.
Pipes are a means to redirect input and output in such a way that the output of one process becomes the input of another process. We have already seen this in use of sed and grep tools where we'd pipe the `echo's` output to the tools and use it. This is the UNIX implementation of pipes for the shell. Let us now see piping in C programs as a means of Inter-process communication in Linux.

pipe(), close() and read(), write()
-------------------------------------
