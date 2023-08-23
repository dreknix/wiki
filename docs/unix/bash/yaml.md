---
tags:
  - bash
  - YAML
---

# YAML and bash

* https://stackoverflow.com/questions/5014632/
* [:fontawesome-brands-github: mrbaseman / parse_yaml](https://github.com/mrbaseman/parse_yaml)

Example yaml file:

```yaml
---
global:
  input:
    - "main.c"
    - "main.h"
  flags: [ "-O3", "-fpic" ]
  sample_input:
    -  { property1: value1, property2: value2 }
    -  { property1: "value 3", property2: 'value 4' }
  licence: |
    this is published under
    open source license
    in the hope that it would 
    be useful
...
```

The following variables are created:

```bash
global_input_1="main.c"
global_input_2="main.h"
global_flags_1="-O3"
global_flags_2="-fpic"
global_sample_input_1_property1="value1"
global_sample_input_1_property2="value2"
global_sample_input_2_property1="value 3"
global_sample_input_2_property2="value 4"
global_licence="this is published under\nopen source license\nin the hope that it would \nbe useful\n"
__=" global"
global_=" global_input global_flags global_sample_input global_licence"
global_flags_=" global_flags_1 global_flags_2"
global_input_=" global_input_1 global_input_2"
global_sample_input_=" global_sample_input_1 global_sample_input_2"
global_sample_input_1_=" global_sample_input_1_property1 global_sample_input_1_property2"
global_sample_input_2_=" global_sample_input_2_property1 global_sample_input_2_property2"
```

A list of variables can iterated with:

```console
$ for f in $global_flags_ ; do eval echo \$f \$${f} ; done
global_flags_1 -O3
global_flags_2 -fpic
```
