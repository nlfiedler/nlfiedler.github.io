---
layout: post
title: Building Git on Mac linker error
---

# Compiling Git on Mac

```
...
    LINK git-daemon
Undefined symbols for architecture x86_64:
  "_locale_charset", referenced from:
      _git_setup_gettext in libgit.a(gettext.o)
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [git-daemon] Error 1
```
