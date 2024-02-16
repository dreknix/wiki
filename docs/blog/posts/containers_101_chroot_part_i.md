---
date: 2024-02-16
categories:
  - Containers
  - UNIX
---

# Containers 101 - chroot (Part I)

In this series the concept of containerization is presented by looking at the
mechanisms behind the scenes. Before modern concepts like cgroups etc. are
described, the main idea behind bundling an execution environment is introduced
by using a chroot environment.

<!-- more -->

The [chroot(8)](https://manpages.debian.org/chroot.8.en.html){target="_blank"}
command allows to run a command or an interactive shell inside a context with a
different root directory. In order to execute this command root privileges are
necessary.

But a simple test will fail:

``` console
$ mkdir target
$ sudo chroot target
chroot: failed to run command ‘/bin/bash’: No such file or directory
```

!!! info

    If no additional parameters (the second is optional and secifies the
    command to run) are provided, `chroot` executes `${SHELL} -i`.

The problem with this example is, that the executable `/bin/bash` does not
exist under the new root directory `target`. The new root directory must provide
everything that is required to run the command. And here are we with the same
issues as in containerization.

When creating a container image often a base image is used, which is based on
Alpine, Debian or Ubuntu distribution. In this case a complete distribution is
present within the new root directory. This mechanism is often hidden when using
`docker` or `podman` commands. When creating a so-called distroless container
for an application the environment can easily be tested with the `chroot`
command.

Let's start building a simple chroot environment.

``` console
$ mkdir target/bin
$ cp /bin/bash target/bin
$ ls -l target/bin/bash
-rwxr-xr-x 1 dreknix dreknix 1396520 Feb 10 10:00 target/bin/bash
$ sudo chroot target
chroot: failed to run command ‘/bin/bash’: No such file or directory
```

Providing just the command is apparently not enough. To debug the execution the
command [strace(1)](https://manpages.debian.org/strace.1.en.html){target="_blank"}
can be used. This commands provides a trace of system calls.

``` console
$ sudo strace chroot target
...
chroot("target")                        = 0
chdir("/")                              = 0
execve("/bin/bash", ["/bin/bash", "-i"], 0x7ffc6f2e7f10 /* 20 vars */) = -1 ENOENT (No such file or directory)
...
```

!!! info

    The command `chroot` is using the C system call
    [chroot(2)](https://manpages.debian.org/chroot.2.en.html){target="_blank"}.
    In this man page more information about creating a chroot environment can be
    found.

From the trace it is apparent that executing `/bin/bash` is failing. But the
error code `ENOENT` is misleading in the case, since the file is existing and
executable.

For a better understanding of this issue, lets write a small application
`app.c` in C.

``` c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    printf("Hello Containerization World!\n");
    return EXIT_SUCCESS;
}
```

Compile the program and execute the program.

``` console
$ gcc -static app.c -o app
$ ls -l app
-rwxr-xr-x 1 dreknix dreknix 900344 Feb 10 10:00 app
$ ./app
Hello Containerization World!
```

Everything works like expected. So let's try to run this application within the
chroot environment.

``` console
$ cp app target
$ sudo chroot target /app
Hello Containerization World!
```

Surprisingly the application can be executed within the chroot environment. The
answer to this is the compiler option `-static`.

What happens, if we do not provide this parameter?

``` console
$ gcc -static app.c -o app
$ ls -l app
-rwxr-xr-x 1 dreknix dreknix 15960 Feb 10 10:00 app
$ ./app
Hello Containerization World!
$ cp app target
$ sudo chroot target /app
chroot: failed to run command ‘/app’: No such file or directory
```

Without this parameter the application can also not be executed within the
chroot environment. When omitting the option `-static` the resulting executable
is much smaller. Something is missing in the executable.

!!! info

    The function `printf` is provided by the C standard library and is using the
    underlying function `puts`.

In the static executable the function `puts` is present and can be called by
existing address:

``` console
$ nm app | grep " puts"
000000000040c180 W puts
```

When omitting the option `-static` the function `puts` is not part of the
executable:

``` console
$ nm app | grep " puts"
                 U puts@GLIBC_2.2.5
```

Since all executable programs share the same standard libraries memory can be
saved by not embedding the same code in every running process. Therefore, all
libraries are shared libraries and are loaded only once into memory. The
programs contains only references to this external shared libraries and the
references are resolved during run-time instead at compile time.

Since these shared libraries are not available inside the chroot environment
the non-static programs can not be started.

Which libraries are needed for the non-static application?

``` console
$ ldd app
        linux-vdso.so.1 (0x00007ffcb21af000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7723e23000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f7724075000)
```

The simple application needs two shared libraries to be copied:

* `ld-linux`: the dynamic linker/loader that is responsible for loading and
handling shared libraries
* `libc`: the standard C library

!!! info

    `linux-vdso` is a virtual dynamic shared object library that is
    automatically mapped by the kernel into the address space of all programs.
    For more information see [vdso(7)](
    https://manpages.debian.org/vdso.7.en.html){target="_blank"}.

``` console
$ mkdir target/lib64
$ mkdir -p target/lib/x86_64-linux-gnu
$ cp /lib64/ld-linux-x86-64.so.2 target/lib64/
$ cp /lib/x86_64-linux-gnu/libc.so.6 target/lib/x86_64-linux-gnu/
$ sudo chroot target /app
Hello Containerization World!
```

With the necessary libraries in place also the non-static application can be
executed inside the chroot environment.

Now let's try to run the shell:

``` console
$ ldd /bin/bash
        linux-vdso.so.1 (0x00007ffd187cb000)
        libtinfo.so.6 => /lib/x86_64-linux-gnu/libtinfo.so.6 (0x00007f673d9ea000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f673d7c1000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f673dba1000)
$ cp /lib/x86_64-linux-gnu/libtinfo.so.6 target/lib/x86_64-linux-gnu/
$ sudo chroot target
bash-5.1#
```

Hurray, the interactive shell is now running inside the newly created chroot
environment under the directory `target`. The complete layout of the
environment is as follows:

``` console
$ tree target/
target/
├── app
├── bin
│   └── bash
├── lib
│   └── x86_64-linux-gnu
│       ├── libc.so.6
│       └── libtinfo.so.6
└── lib64
    └── ld-linux-x86-64.so.2

4 directories, 5 files
```

The simple application is now bundled similar to a distroless container image.
The interactive shell has still limited features, due to the nature of shells:

``` console
bash-5.1# pwd
/
bash-5.1# ls
bash: ls: command not found
bash-5.1# echo *
app bin lib lib64
bash-5.1# exit
exit
$
```

The build-in commands `pwd`, `echo`, `exit`, and the file name expansion are
working, since these are all part of the shell program. Other commands are not
available since the corresponding programs are missing.
