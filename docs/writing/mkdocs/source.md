# Source

* https://squidfunk.github.io/mkdocs-material/reference/code-blocks/
* https://facelessuser.github.io/pymdown-extensions/extensions/highlight/

## Setup

```yaml
theme:
  features:
    - content.code.annotate # (1)!
    
markdown_extensions:
  - pymdownx.highlight:
      anchor_linenums: true
      use_pygments: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - pymdownx.superfences
```

1.  Important for code annotations

## Examples

Source code with highlighting:

```c
#include<stdio.h>
#include<stdlib.h>

/*
 * Hello World
 */
int main(int argc, char *argv[])
{
  int a = 5;
  int b = 4.3;

  printf("Hello World!\n");

  exit EXIT_SUCCESS;
}
```

!!! example "Using annotations:"

    === "Layout"

        ```c
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

        ````markdown
        ```c
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

