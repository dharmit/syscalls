# Run `strace` on `cat` command

Create a file and `strace` the `cat` command that shows its contents:

```shell
$ echo "Hello, world" > filename
```

Now the `strace`:
<!-- TODO: figure out how to fold strace output -->
```shell
$ cat filename
Hello, world

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

Let's start from the first line in the `strace` output and go all the way to the last one.

### `execve()` 

Call: `execve("/usr/bin/cat", ["cat", "filename"], 0x7ffe36b26958 /* 62 vars */) = 0`

From the man page (`man 2 execve`), `execve` stands for "execute program", and here's the function's signature (from 
man page's `SYNOPSIS` section):
```c
#include <unistd.h>

int execve(const char *pathname, char *const argv[],
          char *const envp[]);
```

Comparing the `strace` output above with the function signature, we can deduce that:

* `/usr/bin/cat` corresponds to `const char *pathname`,
* `["cat", "filename"]` to `char *const argv[]`, and
* `0x7ffe36b26958 /* 62 vars */` to `char *const envp[]`.

`execve` executes the program referred to by `pathname`; in this case `/usr/bin/cat`. The program that calls 
`execve` (in this case the shell), is replaced by the new program being executed (in this case `/usr/bin/cat`.) The 
`pathname` should either be an executable binary or a script starting with a line that looks like `#!<interpreter>`, 
e.g., a shell script that starts with `#!/bin/bash`.

`argv` is an array of pointers to strings passed to the new program as its arguments. By convention, the first string 
(`argv[0]`) contains the filename associated with the program being executed; so it's `cat` in this case. I wonder 
if this could be a different name, or it's something that's not allowed. I tried below, but aliasing doesn't seem to 
work on aliases:

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

Call: `brk(NULL)                               = 0x5651e8f08000`

This one took some time to wrap my head around. It was
[this answer on stackexchange](https://unix.stackexchange.com/a/464985/4335) that helped me understand it. Few 
comments and answers on stackexchange network sites suggest that `brk` and `sbrk` are obsolete these days.

`brk(NULL)`, as it is being called here, returns the address of the memory where the 
[heap memory](https://dharmitshah.com/2022/09/stack-heap-go/) (the portion of the memory corresponding to dynamic 
memory allocations) ends. In this case, I think, the value `0x5651e8f08000` we see at the end of that line is the 
memory address where the heap memory ends.

This system call is usually called right before `malloc()` or another call that makes use of `malloc()` internally.

### `arch_prctl()`

Call: `arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd8d760bf0) = -1 EINVAL (Invalid argument)`

This is an erroneous call which can be deduced from the return value of `-1`. The `EINVAL` error indicates that the 
value for the argument `code` is the one that is invalid.

### `mmap()`

Call:`mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f390c25a000`

It took me some time to understand `mmap`. From the first statement of its man page (`mmap, munmap - map or unmap 
files or devices into memory`) I thought it copies the file contents to the memory. But that's not the case.

In this particular call, `mmap` function creates a new mapping of the size 8192 
[bytes](https://www.geeksforgeeks.org/size_t-data-type-c-language/) in the virtual address space of the calling 
process. The calling process here is `cat`.

Using `mmap` doesn't actually read the file, but creates a mapping in the memory of the size of the file. However, 
in this particular case, the value for `fd` (file descriptor) is -1. The value for the flags is bitwise OR of 
`MAP_PRIVATE` and `MAP_ANONYMOUS`. It took me hours reading various answers and blogs on the Internet, but my 
understanding is that this call is meant to [increase the heap memory](https://stackoverflow.com/a/39903701/395670) 
by 8192 bytes.

Some useful links for mmap - [1](https://sasha-f.medium.com/why-mmap-is-faster-than-system-calls-24718e75ab37),
[2](https://stackoverflow.com/questions/1739296/malloc-vs-mmap-in-c),
[3](https://unix.stackexchange.com/questions/389124/understanding-mmap). Phew!