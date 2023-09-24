# Icons & Emojis

* [https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/](
   https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/){:target="_blank"}
* [https://facelessuser.github.io/pymdown-extensions/extensions/emoji/](
   https://facelessuser.github.io/pymdown-extensions/extensions/emoji/){:target="_blank"}

## Setup

```yaml
markdown_extensions:
  - attr_list
  - pymdownx.emoji:
      emoji_index: !!python/name:materialx.emoji.twemoji
      emoji_generator: !!python/name:materialx.emoji.to_svg
```

## Examples

There is a search in all available icons under
[https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/](
 https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/){:target="_blank"}.

Available Icons/Emojis:

* [:material-material-design: https://materialdesignicons.com/](
   https://materialdesignicons.com/){:target="_blank"}
  * `:material-account-circle:` - :material-account-circle:
* [:fontawesome-brands-font-awesome: https://fontawesome.com/](
   https://fontawesome.com/icons?d=gallery&p=2&m=free){:target="_blank"}
  * `:fontawesome-regular-face-laugh-wink:` - :fontawesome-regular-face-laugh-wink:
* [:octicons-mark-github-16: https://primer.style/octicons/](
   https://primer.style/octicons/){:target="_blank"}
  * `:octicons-verified-24:` - :octicons-verified-24:
* [:simple-simpleicons: https://simpleicons.org/](
   https://simpleicons.org/){:target="_blank"}
  * `:simple-ubuntu:` - :simple-ubuntu:
