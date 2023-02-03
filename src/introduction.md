# Introduction

In spite of being a long time Linux user, I have shied away from system calls, `strace`, and the likes. I have
always been afraid of going into the depths of things that have fascinated me. Instead of exploring my fascination, 
I have stayed an arm's length away from it.

My understanding of system calls is as high level as that of a student who's new to Operating Systems. In my head,
system calls are the functions called by our programs written in high-level programming language to communicate with 
the kernel space, which in turn talks with the hardware, to help me print the contents of the file when I do 
something like:

```shell
$ cat filename
Hello, world
```

Diagrammatically, the picture in my head looks like this:

![flow](images/flow.png)

But if I were presented with the output of `strace cat filename`, I would look away from it thinking it's not 
important to me. The truth is, I'm too afraid to make sense of it. I'd dismiss it as something I don't _need_ to 
learn or understand to get my stuff done.

In retrospect (it's been 12.5 years since I gained Bachelors degree in Information Technology (another name for 
Computer Science in most (all?) universities in India at that time)), it is this attitude, behaviour and approach that 
has kept me from learning that what separates a good engineer from an average engineer.

I am an average engineer in my own eyes (partly also because of the 
[impostor syndrome](https://dharmitshah.com/2022/04/imposter-syndrome/) that I'm trying hard to get rid of.)

Through this book I'll be trying to learn more about system calls, and writing what I understood, perceived and 
grasped about them in my own words (and sometimes diagrams.)