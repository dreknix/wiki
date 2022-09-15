# Markdown

## Emphasis

is*bold*is
is_bold_is
is**bold**is
is__bold__is
is***bold***is
is___bold___is

* Bold
  * is **Bold**
  * is __Bold__
* Italic
  * is *Italic*
  * is _Italic_
* Bold & Italic
  * is ***Bold & Italic***
  * is ___Bold & Italic___

## Table

|  A  |  B  |  C  |
|:----|:---:|----:|
| A   | B   | C   |
| AA  | BB  | CC  |
| AAA | BBB | CCC |


## Nested Lists

### Setup

Using [Mdx Truly Sane Lists](https://github.com/radude/mdx_truly_sane_lists).

Needs to be added into `requirements.txt`: `mdx_truly_sane_lists`

```yaml
markdown_extensions:
  - mdx_truly_sane_lists:
      nested_indent: 2
      truly_sane: true
```

### Example

!!! example "GitHub indention"

    === "Layout"

        * Level 1 (GitHub)
          * Level 2
            * Level 3
          * Level 2

    === "Markdown"

        ```markdown
        * Level 1 (GitHub)
          * Level 2
            * Level 3
          * Level 2
        ```

!!! example "Standard indention"

    === "Layout"

        Standanrd indention is not valid any more.

        * Level 1 (Standard)
            * Level 2
                * Level 3
            * Level 2

    === "Markdown"

        ```markdown
        * Level 1 (Standard)
            * Level 2
                * Level 3
            * Level 2
        ```


