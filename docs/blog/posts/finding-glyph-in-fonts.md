---
date: 2023-11-20
categories:
  - LaTeX
---

# Finding Glyph in Fonts

Sometimes it is necessary to find fonts that contain a specific glyph. The tool
[albatross](https://ctan.org/pkg/albatross) can be used for that task.

<!-- more -->

Finding a font by providing the glyph [Snowman Emoji](
https://symbl.cc/en/2603/){target="_blank"}:

``` console
albatross ☃
```

Finding a font by providing the code of the glyph in multiset union notation:

``` console
albatross U+2603
```

A detailed description of all options can be found in the CTAN package
documentation or [albatross(1)](
https://manpages.debian.org/albatross.1.en.html){target="_blank"}.

Possible Unicode characters and their code point can be searched on
[symbl.cc](https://symbl.cc){target="_blank"}.

!!! note
    On Debian or Ubuntu the tool is part of the package `texlive-font-utils`.
    The command is not available. Instead the following command can be used:

    ``` console
    java -jar /usr/share/texlive/texmf-dist/scripts/albatross/albatross.jar 0x2603
    ```
