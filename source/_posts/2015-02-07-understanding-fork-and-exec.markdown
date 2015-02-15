---
layout: post
title: "Understanding fork() and exec()"
date: 2015-02-07 20:46:59 +0530
comments: true
categories: ["linux", "OS"]
---
<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js?autoload=true&amp;skin=doxy&amp;lang=css" defer="defer"></script>

Hello. This is the first of a series of articles intended to accompany the  Operating systems course and helps to get the basic of System Programming.
So without further ado, jump in...

<!-- more -->

Creating a process
-------------------

A process is *a program in the midst of execution*. Apart from the text section, it also has memory space, open resources, internal information and whole lot of scary details (which the OS will abstract for you).  So far so good. So how do you create a process? Since the process is a program in execution you can just create a process by running a program right? Yes and No. There is no universal createprocess() or spawn() method. You cannot create a process like that. So how does it happen then?

In unix all process start off as copies of their parent process. i.e., They start of as clones of their parents. Then they assume their own ID on the decision of their parents ( whoever decided this must be Indian ). So how are the clones created then? Simple. By means of the clone() method. But mostly we won't use that method directly. We will call clone indirectly using a function called fork(). (Which is software jargon for copying). fork() is defined in the header `unistd.h`. Here is the syntax:

						fork(void)


It returns the PID of the process if the fork is successful. And it takes no parameters. So let's see a simple program that forks a process.

``` C++

#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
int main(void) {
    printf("%ld  %ld", (long)getpid(), (long)getppid());
    pid_t pid = fork();
    if(pid == 0)
    {
    	printf("Hello World %ld", (long)getpid());
    	execl("/bin/ls", "-a",(char *)0);
    }
    else
    	if(pid == -1)
    		printf("Failed");
    else
    	printf("HEllo Small world %ld", (long)getpid());
    return 0;
}

```
What happens is, when fork() is executed, the cloning takes place. And until the cloning is finished, the parent is in blocked or waiting state. When it is finished, the processes resume execution at a point right after the fork call. Now remember how the `fork()` call returns a value (that I told you was PID). Well here is where it gets a little tricky.
The `fork()` call returns twice. Once for the parent process and once for the child. And in the case of a parent process, it returns the actual PID. In case of child though, it returns 0. Which is not the PID. Because only the scheduler has the PID of 0. Even the `init` process which is the first process to be executed doesn't get 0. It gets a 1 for PID. So, the fact to be taken into account here is that in case of child process the `fork()` doesn't actually return the PID. Instead it returns 0 so that we can identify it is a child. Note that this is done because the actual PID will be some positive integer which cannot be predicted and hence cannot be used to identify the child.
Everything is fine so far. Now, what exactly is the use of forking a method? For simple programs like the ones we demoed above, where a single program has both parent and child code, this may be fine. But for complex programs where we want to execute a separate program this approach may not be suitable. Which is where exec() comes into picture.

There is no function called exec()
----------------------------------

Yes. `exec()` is the name of a family of functions that execute another program. The one we will see here is `execl()`. Let's see a program and then an explanation.

``` C++

#include<stdio.h>
#include<unistd.h>
#include<sys/types.h>
int main(void)
{
  pid_t pid; //Holds the return value of fork()
  pid = fork();
  if(pid == 0) //fork is successful and we are in child process
  {
    execl("/bin/ls", "", *char(0)); //program name, commandline argument to the program, null pointer for terminating the list of parameters
  }
  else if (pid == -1) //fork has failed
  {
  printf("Forking failed :( )"); //Print an error message to stdin and exit
  }
else //fork done and parent version runs
  {
  printf("This is parent process and I was waiting till child was finished.");
  }
return 0;
}

```

The function call and the comment following it pretty much sums it up. When `exec()` is called, it _overwrites_ the memory space of the process. i.e., none of that parent process's memory space is now accessible. Other than that, everything is fine.
Remember though that you have to give exact path name of the program.

Next post
--------------

Probably in 2 days. We will see InterProcess Communication using named pipes (FIFOs) and using semaphores. Watch this space. Suggestions and comments are welcome.

Many Thanks to Faizaan Mohammed for pointing out typos and grammar mistakes. 
