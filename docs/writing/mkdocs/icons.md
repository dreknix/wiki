# Icons & Emojis

* https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/
* https://facelessuser.github.io/pymdown-extensions/extensions/emoji/

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
https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/.

Available Icons/Emojis:

* https://materialdesignicons.com/
  * `:material-account-circle:` - :material-account-circle:
* https://fontawesome.com/icons?d=gallery&p=2&m=free
  * `:fontawesome-regular-face-laugh-wink:` - :fontawesome-regular-face-laugh-wink:
* https://primer.style/octicons/
  * `:octicons-verified-24:` - :octicons-verified-24:
* https://simpleicons.org/
  * `:simple-ubuntu:` - :simple-ubuntu:
