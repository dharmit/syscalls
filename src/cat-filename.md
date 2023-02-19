# Run `strace` on `cat` command

Create a file and `strace` the `cat` command that shows its contents:

```shell
$ echo "Hello, world" > filename
$ cat filename
Hello, world
```

Now the `strace` output.
<details><summary><code>strace cat filename</code>:</summary>

```shell
$ strace cat filename
execve("/usr/bin/cat", ["cat", "filename"], 0x7ffe36b26958 /* 62 vars */) = 0
brk(NULL)                               = 0x5651e8f08000
arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd8d760bf0) = -1 EINVAL (Invalid argument)
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c25a000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=73663, ...}, AT_EMPTY_PATH) = 0
mmap(NULL, 73663, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f390c248000
close(3)                                = 0
openat(AT_FDCWD, "/lib64/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320v\2\0\0\0\0\0"..., 832) = 832
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
newfstatat(3, "", {st_mode=S_IFREG|0755, st_size=2224288, ...}, AT_EMPTY_PATH) = 0
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
mmap(NULL, 1953104, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f390c06b000
mmap(0x7f390c091000, 1400832, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x26000) = 0x7f390c091000
mmap(0x7f390c1e7000, 339968, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x17c000) = 0x7f390c1e7000
mmap(0x7f390c23a000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1ce000) = 0x7f390c23a000
mmap(0x7f390c240000, 32080, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f390c240000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c068000
arch_prctl(ARCH_SET_FS, 0x7f390c068740) = 0
set_tid_address(0x7f390c068a10)         = 23150
set_robust_list(0x7f390c068a20, 24)     = 0
rseq(0x7f390c069060, 0x20, 0, 0x53053053) = 0
mprotect(0x7f390c23a000, 16384, PROT_READ) = 0
mprotect(0x5651e7e56000, 4096, PROT_READ) = 0
mprotect(0x7f390c28f000, 8192, PROT_READ) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
munmap(0x7f390c248000, 73663)           = 0
getrandom("\x2a\xcf\xe3\x91\x15\x16\xe5\x39", 8, GRND_NONBLOCK) = 8
brk(NULL)                               = 0x5651e8f08000
brk(0x5651e8f29000)                     = 0x5651e8f29000
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=224104272, ...}, AT_EMPTY_PATH) = 0
mmap(NULL, 224104272, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f38fea00000
close(3)                                = 0
newfstatat(1, "", {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}, AT_EMPTY_PATH) = 0
openat(AT_FDCWD, "filename", O_RDONLY)  = 3
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=13, ...}, AT_EMPTY_PATH) = 0
fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0
mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c046000
read(3, "hello, world\n", 131072)       = 13
write(1, "hello, world\n", 13hello, world
)          = 13
read(3, "", 131072)                     = 0
munmap(0x7f390c046000, 139264)          = 0
close(3)                                = 0
close(1)                                = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++
```

</details>

Let's start from the first line in the `strace` output and go all the way to the last one.

### `execve()` 

`execve("/usr/bin/cat", ["cat", "filename"], 0x7ffe36b26958 /* 62 vars */) = 0`

From the man page (`man 2 execve`), `execve` stands for "execute program", and here's the function's signature (from 
its man page's `SYNOPSIS` section):
```c
int execve(const char *pathname, char *const argv[],
          char *const envp[]);
```

Comparing the `strace` earlier output above with the function signature above, we can deduce that:

* `/usr/bin/cat` corresponds to `const char *pathname`,
* `["cat", "filename"]` to `char *const argv[]`, and
* `0x7ffe36b26958 /* 62 vars */` to `char *const envp[]`.

`execve` executes the program referred to by `pathname`; in this case `/usr/bin/cat`. The program that calls 
`execve` (in this case the shell), is replaced by the new program being executed (in this case `/usr/bin/cat`.) The 
`pathname` should either be an executable binary or a script starting with a line that looks like `#!<interpreter>`, 
e.g., a shell script that starts with `#!/bin/bash`.

`argv` is an array of arguments passed to the new program. By convention, the first string (`argv[0]`) contains the 
filename associated with the program being executed; so it's `cat` in this case. Since it's a convention (not a 
mandate) I wonder if this could be a different name than the name of the program. I tried below, but aliasing 
doesn't seem to work on aliases:

```shell
$ alias somecat=cat
$ strace somecat filename
strace: Can't stat 'somecat': No such file or directory

$ strace ll   # aliased to `ls -lh`
strace: Can't stat 'll': No such file or directory
```

Finally, `envp` is also an array of pointers to strings. In my case, it's value is `0x7ffe36b26958 /* 62 vars */`. I 
did a quick check for number of environment variables defined in my shell and the number matches:

```shell
$ env | wc -l
62
```

### `brk()`

`brk(NULL)                               = 0x5651e8f08000`

This one took some time to wrap my head around. It was
[this answer on stackexchange](https://unix.stackexchange.com/a/464985/4335) that helped me understand it. Few 
comments and answers on stackexchange network sites suggest that `brk` and `sbrk` are obsolete these days.

`brk(NULL)`, as it is being called here, returns the address of the memory where the 
[heap memory](https://dharmitshah.com/2022/09/stack-heap-go/) (the portion of the memory corresponding to dynamic 
memory allocations) ends. In this case the value `0x5651e8f08000` we see at the end of that line is the memory 
address where the heap memory ends.

This system call is usually called right before `malloc()` or another call that makes use of `malloc()` internally.

### `arch_prctl()`

`arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd8d760bf0) = -1 EINVAL (Invalid argument)`

This is an erroneous call which can be deduced from the return value of `-1`. The `EINVAL` error indicates that the 
value for the argument `code` is the one that is invalid.

### `mmap()`

`mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c25a000`

From its man page:
```shell
mmap, munmap - map or unmap files or devices into memory
```
Function signature:

```c
void *mmap(void *addr, size_t length, int prot, int flags,
          int fd, off_t offset);
```
I thought it copies the file contents to the memory. But that's not the case.

In this particular call, `mmap` function creates a new mapping of the size 8192 
[bytes](https://www.geeksforgeeks.org/size_t-data-type-c-language/) in the virtual address space of the calling 
process. The calling process here is `cat`.

Using `mmap` doesn't actually read the file, but creates a mapping of the size of the file in the memory. However, 
in this particular case, the value for `fd` (file descriptor) is -1, which indicates that there's no specific file 
for which mapping is to be created. And the flag `MAP_ANONYMOUS` indicates to create an anonymous mapping that's not 
connected to any file. So this particular call is meant to 
[grow the heap memory](https://stackoverflow.com/a/39903701/395670) by 8192 bytes. `NULL` argument lets the kernel 
choose the location in memory where it wants to create the mapping.

The return value `0x7f390c25a000` is the pointer in the virtual memory to the area where this heap memory was created.

Some useful links for mmap - 
[1](https://sasha-f.medium.com/why-mmap-is-faster-than-system-calls-24718e75ab37),
[2](https://stackoverflow.com/questions/1739296/malloc-vs-mmap-in-c),
[3](https://unix.stackexchange.com/questions/389124/understanding-mmap).

### `access()`

`access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)`

Yet another erroneous call. `access()` checks if the calling process can access the filename with the specified mode.
The first argument is the filename and second one is the mode. Here the file `/etc/ld.so.preload` doesn't exist on 
my system. `R_OK` indicates read permissions.

My curiosity led me to find more about `/etc/ld.so.preload`. First I did, `dnf provides` but it exited saying "No 
packages found". Next, I went to the search engine which yielded Q&A across stackechange sites. Attempting 
to open `/etc/ld.so.preload` is a behaviour all programs exhibit; it's something that's baked into glibc. Beyond 
that the topic of "preloading" seemed like a rabbit hole that I didn't want to dive into yet.

### `openat()`

<!-- Why does `cat` need to read `/etc/ld.so.cache` file> -->
`openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3`

Function signature: 
```c
int openat(int dirfd, const char *pathname, int flags)
```

Here, `/etc/ld.so.cache` file is being opened with the bitwise OR result of the flags (access modes) `O_RDONLY` 
(read-only) and `O_CLOEXEC` (enable close-on-exec for the file descriptor). The `AT_FDCWD` parameter is ignored here 
because the filename is provided as an absolute path, not relative path. The returned value `3` is a file descriptor.
This file descriptor is a small, non-negative integer that is an index to an entry in the process's table of open 
file descriptors.

You might notice that `/etc/ld.so.cache` is a binary file. To read its contents, use `ldconfig -p`. Its contents are 
list of directories in which the dynamic linker would search for shared objects.

### `newfstat()`

`newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=73663, ...}, AT_EMPTY_PATH) = 0`

`man newfstatat` opens the man page for `stat()`, which serves as the man page for `stat`, `fstat`, `lstat`, and 
`fstatat`. All of these functions return information about a file. They return this in a buffer pointed to by `statbuf`.

Function signature:
```c
int fstatat(int dirfd, const char *restrict pathname,
        struct stat *restrict statbuf, int flags);
```

Comparing the call we see in `strace` output with the signature above, we can deduce that:

* `3` corresponds to `int dirfd`; it is using the value `3` returned by `openat()` call before.,
* `"""` to `const char *restrict pathname`,
* `{st_mode=S_IFREG|0644, st_size=73663, ...}` to `struct stat *restrict statbuf`, and
* `AT_EMPTY_PATH` to `int flags`.

The `pathname`, which is an empty string `""`, and the flag `AT_EMPTY_PATH` instruct `fstatat` to operate on the 
file pointed to by `dirfd`, which in this case is the file descriptor `3` returned by `openat()` call above. So the 
`newfstatat` system call here is going to fetch and return the information about the file `/etc/ld.so.cache`.

The struct `statbuf` passed as the argument is where the file information fetched by `fstatat` is being put (or 
returned.) The `st_mode` field of the struct is a bitwise OR Of `S_IFREG` (from `man 7 inode`, is it a regular file?)
and `0644` (which in this case is the permission on the `/etc/ld.so.cache` file), while `st_size` is the size in 
bytes of the file:

```shell
$ ls -l /etc/ld.so.cache
-rw-r--r--. 1 root root 73663 Feb  5 11:18 /etc/ld.so.cache
```

Finally, the integer return value 0 at the end indicates that the call was successful.

### `mmap()`

`mmap(NULL, 73663, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f390c248000`

`NULL` indicates that the kernel should choose the address in the memory where it creates the mapping. It is the most 
portable way of creating a new memory mapping.

The value `73663` is the length of the mapping to be created. Here, it's 73663 bytes which is the size of the file 
`/etc/ld.so.cache`, as the mapping is being created for this file.

`PROT_READ` is the desired memory protection of the mapping. `PROT_READ` indicates that the pages may be read.

`MAP_PRIVATE` flag indicates that the changes made to the file are not copied back to the file on the disk. Those 
changes stay in the mapped memory only. Other processes mapping the same file can't see the changes either.

`3` once again points to the `/etc/ld.so.cache` file.

`0` is the value for the offset. It's the starting position in the file referred to by the file descriptor.

The return value `0x7f390c248000` is the pointer to the mapped area where the file `/etc/ld.so.cache` was mapped by 
this call.

### `close()`

`close(3)                                = 0`

It closes the file descriptor. Here, the file `/etc/ld.so.cache` has now been closed. A return value of `0` 
indicates success in closing the file descriptor.

### `openat()`

`openat(AT_FDCWD, "/lib64/libc.so.6", O_RDONLY|O_CLOEXEC) = 3`

Exactly similar call as the previous `openat()` call except for a different file.

### `read()`

`read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320v\2\0\0\0\0\0"..., 832) = 832`

Function signature:
```c
ssize_t read(int fd, void *buf, size_t count);
```
Comparing the call from `strace` output with the signature:
* `3` is the file descriptor supposed to be read. We got this from the `openat` call above.
* `"\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320v\2\0\0\0\0\0"...` is the interesting and confusing part. It's 
the buffer into which `read()` attempts to read up to count bytes.
* `832` is the count of bytes we want to read and the `832` we see being returned the bytes read by the call. That is, 
it was able to read all the requested bytes.

`"\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320v\2\0\0\0\0\0"...` is the content of the file being read. Trying 
to open `lib64/libc.so.6` will seem a lot of gibberish because it's a binary file. A better way to read its 
contents is:
```shell
$ od -bc /lib64/libc.so.6 | head
```
To dig more into this, looks this [stackoverflow answer](https://stackoverflow.com/a/58049206/395670).

### `pread()`

`pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784`

Function signature:
```c
ssize_t pread(int fd, void *buf, size_t count, off_t offset);
```

The signature is quite similar to that of `read()` call above. There's one extra parameter here - `off_t offset`. 
`pread` reads up to `784` bytes from the file descriptor `3` at offset `64` from the start of the file into the 
buffer. Return value `784` is the number of bytes read. 

### `newfstatat()`

`newfstatat(3, "", {st_mode=S_IFREG|0755, st_size=2224288, ...}, AT_EMPTY_PATH) = 0`

Similar call as earlier, but for a different file. As can be seen from below output, permissions on the file
translate to `0755` and its size is 2224288 bytes:

```shell
$ ls -l /lib64/libc.so.6
-rwxr-xr-x. 1 root root 2224288 Jan 11 18:44 /lib64/libc.so.6
```

### `pread()`

<!-- TODO: find out why exact same `pread64` call happened twice -->
`pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784`

Looks like exactly same `pread` call as earlier. I'm not sure why the same call for the same file is repeating.

### Bunch of `mmap()`s

Next up, there are five `mmap()` calls.

1. `mmap(NULL, 1953104, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f390c06b000`

   This call creates a memory map of size 1953104 bytes for the file descriptor `3` (which points to `/lib64/libc.
   so.6`.) The `MAP_DENYWRITE` flag, according to the man page, is ignored. `0x7f390c06b000` is the pointer to the 
   mapped area created for the file.
    
   <!-- TODO: find answers to below questions -->
   What I don't understand here is the requested size of the map. The size of the file is 2224288 but that of the map 
   is 1953104. The value of offset (position to start from) in the call is 0, which is the beginning of the file.
   Why is the requested size smaller than file size? How does it know that it only needs a mapping for the first 
   1953104 bytes?

1. `mmap(0x7f390c091000, 1400832, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x26000) = 
   0x7f390c091000`

    This call asks the kernel to create a new memory map of size 1400832 bytes starting at address `0x7f390c091000` for 
    the same file descriptor as earlier, but starting at the offset value of `0x26000`.
    
    When an address is specified in the `mmap()` call, the kernel takes it as a hint about where to place the mapping. 
    If another mapping already exists there, kernel will place the memory map at a different address. In this particular 
    case, the return value of `mmap` is same as the address provided in the first argument; so, the kernel successfully 
    created a memory map at the address passed as hint. But this was mostly because of the `MAP_FIXED` flag which 
    indicates that the starting address (in this call `0x7f390c091000` should not be considered as a hint, but the 
    memory mapping should be placed at that exact address. More like an order rather than a request, if you will.
    
    `PROT_EXEC` indicates that the pages maybe executed.
    [GNU documentation for libc](https://www.gnu.org/software/libc/manual/html_node/Memory-Protection.html) explains 
    this a bit further. This memory protection indicates that memory can be used to store instructions which can then 
    be executed.

1. `mmap(0x7f390c1e7000, 339968, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x17c000) = 0x7f390c1e7000`

    Similar call as the one earlier with different values for address, size in bytes, and offset. Similar result as the 
    earlier call.

1. `mmap(0x7f390c23a000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1ce000) = 
   0x7f390c23a000`

    One difference from the earlier two calls - it uses `PROT_WRITE`, which indicates that the mapped area could be 
    written to.

1. `mmap(0x7f390c240000, 32080, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f390c240000`

    Since this `mmap()` call appears in a sequence of calls being made with file descriptor value `3`, it's easy to 
    overlook that this call is not being made for any specific file. It's requesting an anonymous map which, from the 
    stackoverflow answer [I linked earlier](https://stackoverflow.com/a/39903701/395670), can be meant for different 
    purposes. In the earlier call it was meant to grow the heap. In this case, I'm not so confident. It could be 
    for any of the reasons mentioned in the answer.

### `close()`

`close(3)                                = 0`

Same as earlier call to `close()`.

### `mmap()`

`mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c068000`

So many calls to `mmap()`! In fact, it's the most called syscall in the output.

This call is very similar to the previous `mmap()` call except that it uses `NULL` instead of a specific address in 
the memory (and, hence, doesn't use `MAP_FIXED` either). This looks like a call to grow the heap size.

### `arch_prctl()`

`arch_prctl(ARCH_SET_FS, 0x7f390c068740) = 0`

A successful call this time, unlike the previous erroneous call.

This call sets the value of 64-bit base for FS register to `0x7f390c068740`. I'm not entirely sure what that means. 
This goes deeper than I am trying to dive into. Yet, I spent a good amount of time trying to understand the FS and 
GS registers. My takeaway, at the moment, is that they are segment registers, and that FS is used for
[Thread Local Storage](https://www.kernel.org/doc/html/latest/x86/x86_64/fsgs.html#common-fs-and-gs-usage).

So, here, it seems to be used to store an address into the FS register. Maybe it's storing the value that's stored 
on that address, I'm not sure.

### `set_tid_address()`

`set_tid_address(0x7f390c068a10)         = 23150`

This call sets pointer to thread ID. For each thread, the kernel maintains two attributes (addresses) called 
`set_child_tid` and `clear_child_tid` which are set to `NULL` by default. The `set_tid_address()` system call sets 
the `clear_child_tid` value for calling thread to the tid pointer. Now, when a thread whose `clear_child_tid` is not 
NULL terminates, then, if the thread is sharing memory with other threads, 0 is written at the address specified in 
`clear_child_tid` and the kernel performs a `futex()` call. This call wakes up a single thread that is performing 
futex wait on the memory location[^1].

The value `23150` that is being returned by the call is the caller's thread ID.

### `set_robust_list()`

`set_robust_list(0x7f390c068a20, 24)     = 0`

This system call deals with the per-thread robust futex lists. These lists are managed in the user space, the kernel 
only has the location of the head of the list. The purpose of the robust futex list is to ensure that if a thread 
accidentally fails to unlock a futex before terminating or calling `execve()`, another thread that is waiting on 
that futex is notified that the former owner of the futex has died[^1].

### `rseq()`

`rseq(0x7f390c069060, 0x20, 0, 0x53053053) = 0`

There's no man page for this on my Fedora 37 system. Looking around on the Internet, I found a lot of information 
about it. A few interesting links - [1](https://criu.org/Rseq),
[2](https://www.efficios.com/blog/2019/02/08/linux-restartable-sequences/),
[3](https://kib.kiev.ua/kib/rseq.pdf). From the third link hee, I found below signature:
```c
int rseq(struct rseq *rseq, uint32_t rseq_len, int flags, uint32_t sig);
```

`rseq` stands for "restartable sequences". It is a sequence of instructions guaranteed to be run atomically with 
respect to other threads and signal handlers on the current CPU. If its execution doesn't finish atomically, the 
kernel changes the execution flow by jumping to an abort handler defined by userspace for that sequence.

In the function signature for `rseq()` system call, `rseq` argument (`*rseq`) is a pointer to the thread-local 
`rseq` structure (`struct rseq`) to be shared between kernel and userspace. This structure contains many things 
including `start_ip` which is the instruction pointer address of the first instruction in the sequence of 
consecutive assembly instructions, and `abort_ip` which is the instruction pointer address of where to move the 
execution flow in case of abort of the sequence pointed by `start_ip`[^2]. 

### Bunch of `mprotect()`s

`mprotect(0x7f390c23a000, 16384, PROT_READ) = 0`

`mprotect()` sets a protection on a region of the memory. Function signature from the man page:
```c
int mprotect(void *addr, size_t len, int prot);
```

`mprotect()` changes the access protections for the calling process's memory pages in the address range [`addr`,
`addr`+`len`-1]. Trying to access memory in a manner that violates the protections causes the kernel to raise 
`SIGSEGV` signal for the process.

`PROT_READ` indicates that the memory for which access protections are being changed can be read.

In this case, the value for `*addr` is `0x7f390c23a000` which we saw in an earlier `mmap` call:
```c
mmap(0x7f390c23a000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1ce000) = 0x7f390c23a000
```
`mmap()` created it with `PROT_READ` and `PROT_WRITE` protection. So this particular `mprotect` call is changing the 
protection for first 16384 bytes of it to `PROT_READ`.

Next two `mprotect()` calls in the sequence do the same but for a different memory region. 0 return value for all 
three calls indicates a successful execution.

### `prlimit64()`

`prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0`

`prlimit()` can be used to get and set resource limits of an arbitrary process.

Function signature:
```c
int prlimit(pid_t pid, int resource, const struct rlimit *new_limit,
           struct rlimit *old_limit);
```

* The value `0` for `pid` indicates that request is for the calling process.
* `RLIMIT_STACK` is the maximum size of the process stack in bytes. Upon reaching this limit, a `SIGSEGV` signal is 
  generated
* Setting `NULL` for `*new_limit` looks common, but I don't understand why this limit is `NULL` while `*old_limit` 
  has a non `NULL` value.
* Setting `*old_limit` to a non `NULL` value places previous hard and soft limit for resource (here `RLIMIT_STACK`) 
  in the `rlimit` structure pointed to by `old_limit`.

### `munmap()`

`munmap(0x7f390c248000, 73663)           = 0`

`munmap()` is the opposite of `mmap()`. Here's the function signature:
```c
int munmap(void *addr, size_t length);
```

`munmap()` deletes the mappings for the specified address range. Here the address is `0x7f390c248000` and range is 
the length which is 73663 bytes.

Subsequent references to the pages which are successfully unmapped will generate `SIGSEGV`. However, it is not an 
error of the indicated range doesn't contain any mapped pages in the first place.

### `getrandom()`

`getrandom("\x2a\xcf\xe3\x91\x15\x16\xe5\x39", 8, GRND_NONBLOCK) = 8`

`getrandom()` helps obtain a series of random bytes. Function signature:
```c
ssize_t getrandom(void *buf, size_t buflen, unsigned int flags);
```

`getrandom()` generates random bytes of length `buflen` (in this call `buflen` is 8), and puts it into the buffer 
pointed by `*buf`. The `GRND_NONBLOCK` flag indicates a non-blocking operation that doesn't block if no random bytes 
are available; or, if using urandom source (same source as `/dev/random` device), it doesn't block if the entropy 
pool hasn't been initialized. The return value 8 indicates the bytes that were copied to the buffer.

### Couple of `brk()`s

`brk(NULL)                               = 0x5651e8f08000`

Same functionality as the earlier [`brk()`](#brk).

`brk(0x5651e8f29000)                     = 0x5651e8f29000`

This call changes the location of [program break](https://stackoverflow.com/a/6338195/395670) to the specified 
address - in this case `0x5651e8f29000`.

### `openat()`

`openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3`

Same as [`openat()`](#openat) call earlier but for `/usr/lib/locale/locale-archive`.

### `newfstatat()`

`newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=224104272, ...}, AT_EMPTY_PATH) = 0`

Same as [`newfstatat()`](#newfstat) call earlier.

### `mmap()`

`mmap(NULL, 224104272, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f38fea00000`

Same as the second [`mmap()`](#mmap-1) call except that for a different file (even the file descriptor value is same.)

### `close()`

`close(3)                                = 0`

Should be quite familiar by now.

### `newfstatat()`

`newfstatat(1, "", {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}, AT_EMPTY_PATH) = 0`

The `dirfd` value here is `1` which is the `stdout`. This is where we expect the output of our `cat` command to show 
up. Empty value for `PATHNAME` and the flag value `AT_EMPTY_PATH` ask `newfstatat` to operate on file referred by 
`dirfd` - here `stdout`. The `statbuf` value is different from previous calls. `S_IFCHR` is for a character device. 
And the return value of `makedev(0x88,0)` is set as the value for `st_rdev`. Values `0x88` and `0` passed to 
`makedev` are major ID and minor ID of the device respectively.

### openat(AT_FDCWD, "filename", O_RDONLY)  = 3

At last the `open()` call for the file we want to display the contents of! Same as previous `openat()` calls but 
only the `O_RDONLY` flag.

### newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=13, ...}, AT_EMPTY_PATH) = 0

Same as the [`newfstatat()`](#newfstat) call earlier.

### `fadvise()`

`fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0`

Function signature:
```c
int posix_fadvise(int fd, off_t offset, off_t len, int advice);
```

`fadvise()` is used by programs to announce an intention to access file data in specific pattern in future. This 
allows the kernel to do necessary optimizations.

Here the advice is meant for the file descriptor 3 starting at offset 0 (beginning of the file). Setting 0 for `len` 
means till end of the file. The advice is not a binding; it's only an expectation of the application. 
`POSIX_FADV_SEQUENTIAL` advice indicates that the application expects to access the data in the file sequentially.

### `mmap()`

`mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c046000`

Same as the first [`mmap()`](#mmap) call except that for a different file (even the file descriptor value is same.)

### `read()`

`read(3, "hello, world\n", 131072)       = 13`

Same as earlier `read()` calls. But I'm not sure about the significance of the number 131072. It translates to 128KB. 
I'm not sure if that means anything.

### `write()` 

`write(1, "hello, world\n", 13hello, world)          = 13`

Function signature:
```c
ssize_t write(int fd, const void *buf, size_t count);
```

Comparing the call wee see in `strace` output with the signature above:
* `1` (which represents stdout in this case) is the file descriptor where it will write,
* `"hello, world\n"` is the data in the buffer, and
* `13hello, world` is two parts:
  * `13` is the count of bytes
  * `hello, world` is the output of our `cat filename` command. If you note closely, it also causes a newline to be 
    printed and hence the closing parenthesis `)` appears on the next line, and so does the return value of the 
    `write()` system call which is `13` in this case. It represents the number of bytes written by the call.


### `read()`

`read(3, "", 131072)                     = 0`

I sure understand this call by now, but don't understand its significance at this point in the flow of our `cat` 
command.

### `munmap()`

`munmap(0x7f390c046000, 139264)          = 0`

As pointed earlier, `munmap()` deletes the mappings for specified address range. Here the mentioned address is same 
as that returned by most recent `mmap()` call which was executed for the file `filename`. So this call is deleting 
the mappings created for `filename`.

### Bunch of `close()`s
```
close(3)                                = 0
close(1)                                = 0
close(2)                                = 0
```

Closing the various file descriptors.

### `exit_group()`

`exit_group(0)                           = ?`

Function signature from the man page:
```c
noreturn void syscall(SYS_exit_group, int status);
```

This system call terminates all threads in a process. It doesn't return anything.

---

[^1]: Most of the explanation here is exactly as-is in the man page. That's because it was the best explanation I 
found.

[^2]: I have tried to simplify things instead of being accurate. To be accurate `abort_ip` and `start_ip` are the 
fields of the `rseq_cs` structure, which is one of the fields of the `struct rseq`. For accurate and detailed 
information, please refer [this](https://kib.kiev.ua/kib/rseq.pdf).