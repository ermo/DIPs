# Compiler-defined Attribute Consistency

| Field           | Value                                                           |
|-----------------|-----------------------------------------------------------------|
| DIP:            | (number/id -- assigned by DIP Manager)                          |
| Review Count:   | 0 (edited by DIP Manager)                                       |
| Author:         | Rune Morling <ermo@serpentos.com>                               |
| Implementation: | (links to implementation PR if any)                             |
| Status:         | Will be set by the DIP manager (e.g. "Approved" or "Rejected")  |


## Abstract

There is some inconsistency between the syntax of various compiler-defined attributes because of historical reasons.

For example, "pure" and "@nogc" are inconsistent because one of them uses the @identifier syntax and the other does not, yet they are both compiler-defined attributes.

This DIP proposes to fix this issue.

## Contents
* [Rationale](#rationale)
* [Prior Work](#prior-work)
* [Description](#description)
* [Breaking Changes and Deprecations](#breaking-changes-and-deprecations)
* [Copyright & License](#copyright--license)
* [Reviews](#reviews)

## Rationale

The newly gained compiler-defined attribute consistency is intended to:

- Make it simpler for human readers to identify and use compiler-defined attributes in the code (ergonomics)
- Make a better impression on users new to Dlang (aesthetics)
- Get rid of a couple of historical warts (consistency)

Example:

Consistent @-attributes as proposed

```D
int add(const int x, const int y) @nogc @nothrow @pure @safe
{
    return x + y;
}
```

Current inconsistent use of @-attributes vs function keywords:

```D
int add(const int x, const int y) @nogc nothrow pure @safe
{
    return x + y;
}
```

## Prior Work

This DIP is based on [DIP64](https://wiki.dlang.org/DIP64) (some text is copied verbatim) but omits the "The definition of attribute sets, which allow users to combine multiple annotations into a single annotation" aspect.

## Description

The compiler front-ends will need to be modified to recognize the new @-versions of the attributes as equivalent to their old non-@-versions (e.g. "@pure" is equivalent to "pure"). This could be achieved via suitable aliases.

Keywords that are only attributes (i.e. they are not also storage classes or type constructors) will be need to be deprecated.

To aid in this transition a tool could be constructed on top of the lexer contained in the [D-Scanner project](https://github.com/dlang-community/D-Scanner). Because this is only a keyword substitution, the chances of this tool failing are incredibly low. All whitespace, comments, and other formatting will be preserved by this tool. The construction of this tool is considered optional.

Various documentation (including the Dlang tour) will need to be updated to reflect this change.

### Grammar

Section 3.5 [Attributes](https://dlang.org/spec/grammar.html#attributes) changes:

```
Attribute:
     LinkageAttribute
     AlignAttribute
     DeprecatedAttribute
     VisibilityAttribute
     Pragma
     static
     extern
     abstract
     final
     override
     synchronized
     auto
     scope
     const
     immutable
     inout
     shared
     __gshared
     AtAttribute
-    FunctionAttributeKwd
     ref
     return

-FunctionAttributeKwd:
-    nothrow
-    pure
-
 AtAttribute:
     @ disable
     @ nogc
+    @ nothrow
     @ live
     Property
+    @ pure
     @ safe
     @ system
     @ trusted
```

## Breaking Changes and Deprecations

At the end of the deprecation period, code using the old keyword attributes will no longer compile.

Hence a fairly long deprecation period might be necessary to allow stakeholders to update existing code bases as appropriate.

The proposed tool could help mitigate the cost this update imposes on existing code bases.

## Copyright & License

Copyright (c) 2021 by the D Language Foundation

Licensed under [Creative Commons Zero 1.0](https://creativecommons.org/publicdomain/zero/1.0/legalcode.txt)

## Reviews
