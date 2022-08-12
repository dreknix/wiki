# Markdown

## Table

|  A  |  B  |  C  |
|:----|:---:|----:|
| A   | B   | C   |
| AA  | BB  | CC  |
| AAA | BBB | CCC |


## Nested Lists

* Level 1 (GitHub)
  * Level 2
    * Level 3

* Level 1 (Standard)
    * Level 2
        * Level 3

## Mdx Truly Sane Lists

* [:fontawesome-brands-github: radude/mdx_truly_sane_lists](
   https://github.com/radude/mdx_truly_sane_lists)

Needs to be added into `requirements.txt`: `mdx_truly_sane_lists`

```yaml
markdown_extensions:
  - mdx_truly_sane_lists:
      nested_indent: 2
      truly_sane: true
```
