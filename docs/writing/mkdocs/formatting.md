# Formatting

## Critic

* https://squidfunk.github.io/mkdocs-material/reference/formatting/#highlighting-changes
* https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown-extensions/#critic
* https://facelessuser.github.io/pymdown-extensions/extensions/critic/
* https://github.com/CriticMarkup/CriticMarkup-toolkit

### Setup

```yaml
markdown_extensions:
  - pymdownx.critic:
      mode: view
```

### Examples

* Addition {++add++}
* Deletion {--del--}
* Substitution {~~old~>new ~~}
* Comment {>>comment<<}
* Highlight {==highlight==}

{==

Highlight a complete paragraph

==}

## Caret, Mark & Tilde

* https://squidfunk.github.io/mkdocs-material/reference/formatting/#highlighting-text
* https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown-extensions/#caret-mark-tilde
* https://facelessuser.github.io/pymdown-extensions/extensions/caret/
* https://facelessuser.github.io/pymdown-extensions/extensions/mark/
* https://facelessuser.github.io/pymdown-extensions/extensions/tilde/

### Setup

```yaml
markdown_extensions:
  - pymdownx.caret
  - pymdownx.mark
  - pymdownx.tilde
```

### Example

- ==This was marked==
- ^^This was inserted^^
- ~~This was deleted~~

## Keyboard

* https://squidfunk.github.io/mkdocs-material/reference/formatting/#adding-keyboard-keys
* https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown-extensions/#keys
* https://facelessuser.github.io/pymdown-extensions/extensions/keys/
* https://facelessuser.github.io/pymdown-extensions/extensions/keys/#key-map-index

### Setup

```yaml
markdown_extensions:
  - pymdownx.keys
```

### Examples

* `++space++`: ++space++
* `++return++`: ++return++
* `++ctrl+c++`: ++ctrl+c++
* `++alt+a++`: ++alt+a++
* `++shift+up++`: ++shift+up++
* `++windows+page-up++`: ++windows+page-up++
