# Source

* https://squidfunk.github.io/mkdocs-material/reference/code-blocks/
* https://facelessuser.github.io/pymdown-extensions/extensions/highlight/

## Setup

```yaml
markdown_extensions:
  - pymdownx.highlight:
      use_pygments: true
      noclasses: true
      pygments_style: 'solarized-dark'
  - pymdownx.superfences
```

## Examples

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
