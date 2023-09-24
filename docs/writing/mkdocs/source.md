# Source

* [https://squidfunk.github.io/mkdocs-material/reference/code-blocks/](
   https://squidfunk.github.io/mkdocs-material/reference/code-blocks/){:target="_blank"}
* [https://facelessuser.github.io/pymdown-extensions/extensions/highlight/](
   https://facelessuser.github.io/pymdown-extensions/extensions/highlight/){:target="_blank"}

## Setup

```yaml
theme:
  features:
    - content.code.annotate # (1)!
    - content.code.copy # (2)!

markdown_extensions:
  - pymdownx.highlight:
      anchor_linenums: true
      line_spans: __span
      pygments_lang_class: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - pymdownx.superfences
```

1.  Important for code annotations
2.  Enable code copy button

## Examples

Code with highlighting:

``` python
def example(param):
    print(f'param = {param}')
```

!!! example "Using snippets:"

    === "Layout"

        ``` c title="hello_world.c"
        --8<-- "writing/mkdocs/hello_world.c"
        ```

    === "Markdown"

        ```` markdown
        ``` c title="hello_world.c"
        ;--8<-- "writing/mkdocs/hello_world.c"
        ```
        ````

        The filename is under the directory `base_path` configured in
        `mkdocs.yaml`:

        ``` yaml
        markdown_extensions:
          - pymdownx.snippets:
              base_path: snippets/
        ```

        The directory should be outside of `docs/`.

        More examples can be found in the
        [documentation](
         https://facelessuser.github.io/pymdown-extensions/extensions/snippets/){:target="_blank"}.

!!! example "Using annotations:"

    === "Layout"

        ``` c
        // Simple example
        int main(int argc, char* argv[])
        {
          int a = 42; // The answer (1)
          return EXIT_SUCCESS; // (2)!
        }
        ```

        1.  to everything.
        2.  Annotation with stripping comments

    === "Markdown"

        ```` markdown
        ``` c
        // Simple example
        int main(int argc, char *argv[])
        {
          int a = 42;          // The answer (1)
          return EXIT_SUCCESS; // (2)!
        ```

        1.  to everthing
        2.  Annotation with stripping comments
        ````

    For each code annotation a link can be set
    ([example](http://localhost:8000/writing/mkdocs/source.html#__code_3_annotation_1)).
