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

With title:

``` python title="Python example"
def example(param):
    print(f'param = {param}')
```

With line numbers and line highlight:

``` python linenums="1",hl_lines="2"
def example(param):
    print(f'param = {param}')
    return param
```

Highlighting inline code is done with the `#!` characters, e.g.,
`#!python print(f'param = {param}')`.

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

## Available Languages/Lexer

See:
[https://pygments.org/docs/lexers/](https://pygments.org/docs/lexers/){target="_blank"}

### bash / sh

``` bash
#!/usr/bin/env bash

if [ $? != 0 ]
then
  echo "${USER:-}"
else
  echo "${HOSTNAME:-}"
fi
```

### console / shell-session

``` console
$ whoami
dreknix
```

### python

``` python
def example(param):
    print(f'param = {param}')
```

### pycon

``` pycon
>>> name = 'dreknix'
>>> print(name)
dreknix
>>> div = 1 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```

### pwsh / powershell

``` pwsh
$content = [IO.File]::ReadAllText('c:\samplefile.txt')
Get-Item -Path C:\Temp
```

### pwsh-session

``` pwsh-session
PS> Get-Item -Path C:\Temp
dreknix
PS C:\> $content = [IO.File]::ReadAllText('c:\samplefile.txt')
```

### batch

``` batch
REM off
ECHO ms-dos
```

### doscon

``` doscon
C:\> ECHO ms-dos
ms-dos
```

### text

``` text
Formatted text without highlighting
```

### todotxt

https://github.com/todotxt/todo.txt

``` todotxt
(A) 2024-01-01 example todo +writing @mkdocs due:2024-01-31
(B) 2024-01-01 example todo +writing @mkdocs due:2024-01-31 label:free
x (A) 2024-02-01 2024-01-01 example todo +writing @mkdocs due:2024-01-31
```
