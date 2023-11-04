# Nixery

[Nixery](https://nixery.dev){:target="_blank"} is acontainer image registry that
provides small base images with only the packages specified in the URL,
separated by slashes.

When the first element is `shell` than the `bash` and `coreutils` are part of
the image. After that packages from the
[Nix package manager](https://nixos.org/nix){:target="_blank"} can be added. Any
package available at
[https://search.nixos.org/packages](https://search.nixos.org/packages) can be
installed.

The following command starts a shell in a container based on a Nixery image that
contains the `coreutils` with `bash` and the packages `git` and `python312`.

```console
$ docker run -it --rm nixery.dev/shell/git/python312 bash
```

Nixery can be used to [debug slim containers](../debugging.md).
