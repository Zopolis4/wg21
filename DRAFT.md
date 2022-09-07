---
title: "Create namespaces for modules without wrapping files in namespace blocks"
document: PXXXXRX
date: 2022-09-09
audience: Evolution
author:
  - name: Maximilian Downey Twiss
    email: <creatorsmithmdt@gmail.com>
toc: false
---

# Introduction

This paper proposes to allow the creation of namespaces for modules
without wrapping files in namespace-blocks through specifying a namespace-name
in a module-declaration.

# Motivation and Scope

In brief, this paper hinges on the idea that wrapping implementation files in namespace
blocks is bad, as they add unnecessary (albeit optional) indentation, decrease clarity
when not indented and add unnecessarily lines of code.

While the effects of this can partially be mitigated by not indenting the namespace blocks and using an ending block comment, it does not solve the root issue, which gets worse the larger the file is and the more interface partitions are used, as they also have to be wrapped in namespace blocks.


While headers allow one to relatively easily use namespaces without wrapping implementation
files in namespace blocks, modules have no such equivalent.

Using headers, one can wrapping the header files in namespace blocks and prepending the
namespace onto the functions in the implementation file, although this is against good
style.

For example:

old.cpp
```
#include "old.h"

void old::bar()
{
  int bar;
}

void old::foo()
{
  int foo;
}

```

old.h
```
namespace old
{
public:
  void bar();
private:
  void foo();
};
```

However, when using modules, there is currently no way, regardless of style, to avoid
wrapping implementation files in namespace blocks.

See example:
current.cpp
```
export module current;

namespace current
{
export void bar()
{
  int bar;
}

void foo()
{
  int foo;
}
} //namespace current
```

Through this change, this paper creates a simple, compact way to use namespaces with modules, as opposed to wrapping implementation files inside namespace blocks.

Like so:
new.cpp
```
export module new namespace new;

export void bar()
{
  int bar;
}

void foo()
{
  int foo;
}
```

While the current examples may seem to be relatively similar, the differences between the
current method and new method become significantly more apparent in larger files, and with
multiple interface partitions. Examples reflecting this were not included for brevity.

# Wording

Relative to N4910.

## #[module]

Change the grammar before paragraph 1:

```diff
  module-declaration:
    export-keyword(opt) module-keyword module-name module-partition(opt)
-    attribute-specifier-seq(opt);
+    namespace-name(opt) attribute-specifier-seq(opt);
  module-name:
    module-name-qualifier(opt) identifier
```

(The re-adjusting of numbers after the introduction of a new paragraph was not included
for diff conciseness, but is implied)
Insert new paragraph after 3:
```diff
+ A module-declaration with a namespace-name has behaviour equal to a module containing
+ a namespace-definition with a namespace-body enclosing all exported names.
+ Only the primary module interface unit of any given module can contain a namespace-name.
```

# Citation

[@N4762] is here just to make it compile.
