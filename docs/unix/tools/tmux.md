# tmux

tmux is a terminal multiplexer.

* [:fontawesome-brands-github: tmux / tmux](https://github.com/tmux/tmux)
* [tmux - wiki](https://github.com/tmux/tmux/wiki)
* [tmux(1)](https://manpages.debian.org/tmux.1.en.html)

## Scripting with tmux

### send-keys

The command `send-keys` has the following syntax:

```console
$ tmux send-keys -t '{session}:{window}.{pane}' {command} ENTER
```

The target can be shorted as `-t ':.!'`, which means:
* last active session
* last active window
* previously selected pane

Usage with `new-window`:

```bash
tmux new-window -c "${dir}" -n "${name}"
tmux send-keys -t ':.' "${EDITOR}" ENTER
```

Usage with `new-session`:

```bash
tmux new-session -d
tmux send-keys -t ':.' "${EDITOR}" ENTER
tmux attach-session -t ":"
```

## Current key bindings

* ++ctrl+a++ ++v++ - split pane vertically (same directory)
* ++ctrl+a++ ++h++ - split pane horizontally (same directory)
