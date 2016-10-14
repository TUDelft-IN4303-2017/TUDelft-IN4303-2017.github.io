---
layout: page
title: "Lab 5: Name Analysis"
excerpt: "Lab 5: Name Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you define name bindings and corresponding constraints for MiniJava in NaBL2.

The official NaBL2 documentation is not entirely up to date. It is better to only use the lab
description for now.
{: .notice .notice-warning}

## Overview

### Objectives

Specify name analysis for MiniJava in
[NaBL2](http://metaborg.org/en/latest/source/langdev/meta/lang/nabl2/index.html), by specifying a
scope graph and resolution constraints. The specification should include:

1. Scope graph constraints for
  * class declarations
  * class references
  * field declarations
  * method declarations
  * parameter declarations
  * variable declarations
  * field, parameter, and variable references
2. Resolution constraints for
  * class references
  * method references
  * parameter references
  * field references
  * variable references
3. Constraints for
  * duplicate definitions of classes, fields, parameters, and variables
  * variable declarations which hide parameter or field declarations
4. Properties on
  * variable declarations to indicate whether they are fields, parameters, or local variables

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment5` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submissions is October 26th, 23:59.
{: .notice .notice-warning}

### Grading

The point assignment is not up to date yet.
{: .notice .notice-warning}

You can earn up to 80 points for the correctness of your name analysis.
Therefore, we run several test cases against your implementation.
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (20 points)
  * class declarations and references (6 points)
  * method declarations (2 point)
  * variable and field declarations (6 points)
  * variable and field references (6 points)
* scopes (15 points)
* constraints (35 points)
  * duplicate definitions (10 points)
  * unresolved references (10 points)
  * hiding variables (5 points)
  * main class references (10 points)
* challenge (10 points)

You can earn up to 20 points for your messages in errors and warnings.
We particular focus on
 readability,
 precision,
 and the level of detail in your messages.

### Early Feedback

We provide early feedback for your language implementation.
This feedback gives you an indication which parts of your implementation might still be wrong.
It includes a summary on how many tests you pass and how many points you earn by passing them.
You have 3 early feedback attempts.

## Detailed Instructions

### Preliminaries

#### Git Repository

You continue with your work from the previous assignment.  See the
[Git documentation](/documentation/git.html#continue-from-previous-assignment)
on how to create the `assignment5` branch from your previous work.

#### Updating from the initial project

A few changes were made to the initial project. See
[Git documentation](/documentation/git.html#pulling-in-changes-from-upstream) on how to update your
initial project to match the latest version.

### Constraint generation rules

Name binding is specified as constraint generation rules in `.nabl2` files. NaBL2 files must go in
the `trans/analysis` directory. The module name at the top of the file should match the filename relative to `trans`. For example, the file `trans/analysis/minijava.nabl2` starts as:

```
module analysis/minijava
```

The rules match on an AST constructor, and get one or more scopes as an argument. The initial
project contains an `init` rule that creates the initial global scope. The general schema for these
rules looks like this:

```
rules

    [[ <Pattern> ^ (<{Scope ","}*>) ]] :=
        <Constraint>.
```

Multiple constraints are separated by commas. The constraint `true` always succeeds.

There can only be **one** rule that matches on a certain constructor. It is possible and recommended
to split the rules over multiple files.
{: .notice .notice-warning}

There is no implicit traversal of the AST. This means the rules should be complete: there must be a
rule for every constructor you want to visit.
{: .notice .notice-warning}

### Name Binding

#### Namespaces

Different kinds of names can be kept separate by putting them in a different *namespace*. For
example, variables live in the `Var` namespace. A name with a namespace and an (implicit) AST
position is called an *occurrence*, and written as `Var{x}`, where `Var` is the namespace, and `x`
the name.

Namespaces are declared as part of the name of the name resolution parameters. Put the following in
one of your `*.nabl2` files:

```
signature

    name resolution
        namespaces Var // ... space separated list of namespaces ...
```

For this assignment it is required to use the `Var` namespace for fields, method parameters, and
local variables.
{: .notice .notice-warning}

#### Declarations and references

References and declarations are specified as occurrences with arrows going to and from scopes
respectively. Reference resolution can be thought of as finding a path in the scope graph from the
reference to a declaration with the same name. For example the rules for variable declarations and
references, both in the current scope, look like this:

```
rules

    [[ Var(_,x) ^ (s) ]] :=
        Var{x} <- s.

    [[ VarRef(x) ^ (s) ]] :=
        Var{x} -> s.
```

#### Introducing new scopes

New scopes can be created and passed down to children in the AST using `new` and recursive calls to
the constraint generation rules. The following example creates a new scope `s'`, adds an edge to the
current scope `s`, and passes it as the current scope in the recursive call for the subterm `e`.

```
rules

    [[ Method(_, _, _, _, _, e) ^ (s) ]] :=
        new s',
        s' ---> s,
        [[ e ^ (s') ]].
```

The term in the recursive call **must** be a variable from your match pattern.
{: .notice .notice-warning}

#### Importing scopes by name

TBD

### Constraints

#### Name resolution

A reference in the scope graph must resolve to a declaration. This is specified with a resolution
constraint. Usually the right hand side of the constraint is a variable that will get the value of
the declaration that the reference refers to. In the following example, we capture the declaration
in the variable `d`. Note that `d` is really a *meta-variable*, that is, it is a variable in the
rule, not a variable in MiniJava.

```
rules

    [[ VarRef(x) ^ (s) ]] :=
        Var{x} -> s,
        Var{x} |-> d.
```

#### Custom errors and warnings 

It is possible to control the errors and warnings that are generated if a constraint fails.

```
rules

    [[ VarRef(x) ^ (s) ]] :=
        Var{x} -> s,
        Var{x} |-> d | <Severity> <Message> <Location>.
```

The severity is one of `error`, `warning`, or `note`. The message is optional and can be 1) a
literal string `"variable not found"`, or 2) a formatted message `$[Cannot find variable [x]]`.
The default error location is the matched term, but it can be specified explicitly using `@t`,
where `t` is a variable from your match pattern.

#### Sets of names

The following constraints can be used to restrict the names that can appear in scopes:

* `distinct <Nameset>`
* `<Nameset> subseteq <Nameset>`

The constraint `<Nameset> seteq <Nameset>` is desugared to two `subseteq` constraints.

The following sets of names are available (for a given scope `s`):

* all declarations `D(<Scope>)/<Namespace>`,
* all references `R(<Scope>)/<Namespace>`,
* all (transitively) visible declarations `V(<Scope>)/<Namespace>`,
* and all reachable (including shadowed) declarations `W(<Scope>)/<Namespace>`.

For example, `D(s)/Var` would give all variable declarations in `s`.

You can write expressions to get the set of names you are interested in, using the following operators:

* union `<Nameset> union <Nameset>`,
* intersection `<Nameset> isect <Nameset>`,
* and set different `<Nameset> diff <Nameset>`.

Note that sets of names behave like multisets. For example, `X diff Y` where `X` contains two `x`s
and `Y` contains one `x` will result in a set with one `x`.

Error messages on `subseteq` and `distinct` constraints can use two special keywords.  The position
can be `@NAMES`, in which case the error will appear on the relevant names. In a formatted message
`NAME` can be used to insert the name in the message.

Note that if you use set expressions in your constraint, the order in which you do the operations
can be important to get the error on the right name.
{: .notice .notice-warning}

#### Origins on variable declarations

Properties can be set on declarations with a constraint `<Namespace>{<Term>}.<NAME> := <Term>`. In
this lab you must set a property `origin` on `Var` declarations.

* Fields must get an origin `Field()`
* Parameters must get an origin `Param()`
* Local variables must get an origin `Local()`

It is important for grading to use these exact property and constructor names.
{: .notice .notice-warning}

You will need to define these constructors by adding a `signature` section of a Stratego file,
e.g. `trans/analysis.str`.

### Challenge

TBD

### Debugging

The result of analysis, including the scope graph that was built, the resolved references, and more
can be shown by selecting the menu `Spoofax > Analysis > Show File Analysis` for a MiniJava program.

At the moment it is not checked if the constructors that you match on actually exist. You can check
this by opening the generated Stratego file. This is done by selecting `Spoofax > Analysis >
Generate Stratego` for an NaBL2 file. Make sure that you import your MiniJava signatures in the
NaBL2 file, or you will also get errors for constructors that do exist.

