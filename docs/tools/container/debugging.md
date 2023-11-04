# Debugging Slim Containers

In order to debug a slim or distroless container the
[Nixery](baseimages/nixery.md) can be used.

Start a simple slim container:

```console
$ docker run --rm -p 8080:80 --name slim-container traefik/whoami
```

Attach a debug container based on Nixery (with the packages `ps`, `tree`,
and `vim`) to the slim container namespaces `pid` and `network`.

```console
$ docker run \
           -it --rm \
           --name debugger \
           --pid container:slim-container \
           --network container:slim-container \
           nixery.dev/shell/ps/tree/vim \
           bash
```

Now the processes of the slim container are visible.

```console
# ps -e
UID          PID    PPID  C STIME TTY          TIME CMD
0              1       0  0 18:28 ?        00:00:00 /whoami
0             18       0  0 18:29 pts/0    00:00:00 bash
0             24      18  0 18:30 pts/0    00:00:00 ps -ef
#
```

The filesystem of the slim container is visible under `/proc/1/root`.

```console
# ls /proc/1/root
dev  etc  proc  sys  usr  whoami
#
```

To work directly on the filesystem of the slim container you need to `chroot`
into `/proc/1/root`. In order to use the tools from the Nixery image the
directories `/bin` and `/nix` must be accessible from the filesystem of the slim
container. This can only be achieved via the `/proc` filesystem.

```console
# ln -s /proc/$$/root/bin /proc/1/root/nix-bin
# ln -s /proc/$$/root/nix /proc/1/root/nix
# export PATH=${PATH}:/nix-bin
# chroot /proc/1/root /nix-bin/bash
```

Now you can debug a running slim container from within and use every tool you
like.

!!! note
    [:fontawesome-brands-github: iximiuz/cdbug](
     https://github.com/iximiuz/cdebug){:target="_blank"}

    A tool for container debugging. Based on the ideas descripted in the blog
    post "[Docker: How To Debug Distroless And Slim Containers](
    https://iximiuz.com/en/posts/docker-debug-slim-containers/){:target="_blank"}"
    from Ivan Velichko

!!! note
    At DockerCon 2023 `docker debug` was announced.
