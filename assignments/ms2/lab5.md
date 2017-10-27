---
layout: page
title: "Lab 5: Name Analysis"
excerpt: "Lab 5: Name Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you define name bindings and corresponding constraints for MiniJava in NaBL2. The
concepts you are going to use in NaBL2 are described in the following papers:

1. P. Neron, A. Tolmach, E. Visser, G. Wachsmuth: [A Theory of Name Resolution](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2015-001.pdf), ESOP 2015
2. H. van Antwerpen, P. Neron, A. Tolmach, E. Visser, G. Wachsmuth: [A Constraint Language for Static Semantic Analysis based on Scope Graphs](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2015-012.pdf), PEPM 2016

Documentation for NaBL2 can be found online at
[NaBL2 Documentation](https://spoofax.readthedocs.io/en/latest/source/langdev/meta/lang/nabl2/index.html). For
a complete overview of the language, use the
[NaBL2 Language Reference](https://spoofax.readthedocs.io/en/latest/source/langdev/meta/lang/nabl2/reference.html). The
[NaBL2 Configuration](https://spoofax.readthedocs.io/en/latest/source/langdev/meta/lang/nabl2/configuration.html) documentation
describes how to configure logging and inspect the results of analysis.
{: .notice .notice-info}

## Overview

### Objectives

Specify name analysis for MiniJava in
[NaBL2](http://metaborg.org/en/latest/source/langdev/meta/lang/nabl2/index.html), by specifying a
scope graph and resolution constraints. The specification should include:

1. Scope graph constraints for
  * class declarations
  * class references
  * field declarations
  * parameter declarations
  * variable declarations
  * field, parameter, and variable references
2. Resolution constraints for
  * class references
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

The deadline for submissions is October 27th 2017, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 100 points for the correctness of your name analysis.
Therefore, we run several test cases against your implementation.
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (45 points)
  * class declarations and references (6 points)
  * field declarations (6 points)
  * parameter declarations (6 points)
  * variable declarations and references (6 points)
  * scopes of program, classes, and methods (12 points)
  * variable origins (3 points)
* constraints (45 points)
  * unresolved references (16 points)
  * duplicate definitions (20 points)
  * hiding variables and fields (9 points)
* challenge (10 points)

### Early Feedback

We provide early feedback for your language implementation.
This feedback gives you an indication which parts of your implementation might still be wrong.
It includes a summary on how many tests you pass and how many points you earn by passing them.
You have 3 early feedback attempts.

## Detailed Instructions

### Preliminaries

#### GitHub Repository

You continue with your work from the previous assignment. See the
[Git documentation](/documentation/git.html#continue-from-previous-assignment) on
how to create the `assignment5` branch from your previous work.

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

Multiple constraints are separated by commas. The constraint `true` always succeeds. Inspect the
signature files in `reference/src-gen/signatures/` for the available constructors.

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

Note that specifying declarations and references like this does not imply that references must
resolve. To ensure this, you must add resolution constraints, described below.

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

There are some default rules to traverse a list of terms. For example to traverse all class
definition in a program, you can write:

```
rules

    [[ Program(_,cs) ^ (s) ]] :=
        // ...
        Map1 [[ cs ^ (s) ]].
```

The rule `Map1` takes one scope parameter. There is also a rule `Map2` that takes two
parameters. These rules are implemented in NaBL2, so it is possible to write such named rules
yourself as well. See
[stdlib/map.nabl2](https://github.com/metaborg/nabl/blob/master/org.metaborg.meta.nabl2.runtime/trans/nabl2/runtime/stdlib/map.nabl2)
for the implementation of these rules.

#### Importing scopes by name

Scopes can also be imported by name, instead of using parent edges. Imports always require a pair of
constraints, one that associates a name with a scope, and one the imports a reference into a
scope. The constraints are written as:

* `<Occurrence> ===> <Scope>` associates the scope with the given declaration.
* `<Occurrence> <=== <Scope>` imports a reference into the scope. The declarations in the associated scope
  of the resolved reference are now visible in the importing scope.

The references and declarations used in the previous two constraints still need to be introduced
explicitly in the graph.
{: .notice .notice-warning}

### Constraints

#### Name resolution

A reference in the scope graph must resolve to a declaration. This is specified with a resolution
constraint. Usually the right hand side of the constraint is a variable that will get the value of
the declaration that the reference refers to. In the following example, we capture the declaration
in the variable `d`. Note that `d` here is a *meta-variable*, that is, it is a variable in the rule,
not a variable in MiniJava.

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

MiniJava requires errors and warnings in a few cases where hiding
occurs. Fields are not allowed to hide fields in super classes. Local
variables and parameters are allowed to hide, but get a warning if they do.

The following constraints can be used to restrict the names that can appear in scopes:

* `distinct <Nameset>` and `distinct/name <Nameset>`
* `<Nameset> subseteq <Nameset>` and `<Nameset> subseteq/name <Nameset>`

The constraint `<Nameset> seteq <Nameset>` is desugared to two `subseteq` constraints.

The following sets of names are available (for a given scope `s`):

* all declarations `D(<Scope>)/<Namespace>`,
* all references `R(<Scope>)/<Namespace>`,
* all (transitively) visible declarations `V(<Scope>)/<Namespace>`,
* and all reachable (including shadowed) declarations `W(<Scope>)/<Namespace>`.

For example, `D(s)/Var` would give all variable declarations in `s`.

You can write expressions to get the set of names you are interested in, using the following operators:

* union `(<Nameset> union <Nameset>)`,
* intersection `(<Nameset> isect <Nameset>)`,
* and set different `(<Nameset> minus <Nameset>)`.

Note that sets of names behave like multisets. For example, `X diff Y` where `X` contains two `x`s
and `Y` contains one `x` will result in a set with one `x`.

Error messages on `subseteq` and `distinct` constraints can use two special keywords.  The position
can be `@NAMES`, in which case the error will appear on the relevant names. In a formatted message
`NAME` can be used to insert the name in the message.

See the reference documentation for a more complete description of
these constraints.
{: .notice .notice-info}

Note that if you use set expressions in your constraint, the order in which you do the operations
can be important to get the error on the right name.
{: .notice .notice-warning}

#### Origin properties on variable declarations

Properties can be set on declarations with a constraint `<Namespace>{<Term>}.<NAME> := <Term>`. In
this lab you must set a property `origin` on `Var` declarations.

* Fields must get an origin `Field()`
* Parameters must get an origin `Param()`
* Local variables must get an origin `Local()`

It is important for grading to use these exact property and constructor names.
{: .notice .notice-warning}

You will need to define these constructors by adding a `signature` section of a Stratego file,
e.g. `trans/analysis.str`. Stratego signatures define new constructors that you can use, and are
written as:

```
module analysis

// ...

signature
  constructors
    <ConstructorName> : <SortName>

// ...
```

The sort name is not important, but if you do not know what to use, use `Origin`.

### Challenge

In lab 7 we need to test that an overriding method has the same type as the overridden method in the
parent class. For every method declaration, we want to introduce a reference, that resolves to its
overridden method. For example, consider the following situation:

```
class Foo {
    public int m() {
        return 1;
    }
}
class Bar extends Foo {
    public int m() {
        return 1;
    }
}
```

The declaration of `Bar.m()` should introduce a reference to `Foo.m()`. Of course just introducing a
reference in the parent scope is not enough, because this will result in an error for `Foo.m()`
which does not override any method. The challenge is to create a reference that resolves to the
overridden method if it exists, and to its own method declaration otherwise.

It is possible to encode this logic in the scope graph by modifying edge labels, the specificity
order of labels, and the well-formedness regular expression on paths. By default the parameters for
name binding are the following:

```
signature

  name resolution

    labels
      P I

    order
      D < I,
      D < P,
      I < P

    well-formedness
      P* . I*
```

The label `P` is the default label for edges between scope. This can be written explicitly with the
constraint `s' -P-> s`. The label `I` is the default label for imports, and can also be written
explicitly as `d =I=> s` and `s =I=> r`. *Note that scopes can have multiple outgoing edges, which
can have different labels.*

The well-formedness is a regular expression on path labels, that rules out paths that do not match
it. In the default case, it means that after an import, you cannot resolve in a parent scope
anymore. The regular expressions on labels have the following syntax:

* `e` is the empty string
* `0` is the empty language (i.e., it matches nothing)
* `<Label>` is a literal label
* `~<Regexp>` is the complement (e.g., `~0` matches anything)
* `<Regexp>*` is the closure (i.e., match zero or more)
* `<Regexp> . <Regexp>` is concatenation (e.g., `P . I` matches one parent edge followed by an
  import)
* `<Regexp> | <Regexp>` is a logical or (e.g., `P | I` matches a parent or an import edge)
* `<Regexp> & <Regexp>` is a logical and (i.e., both expressions must match)
* `(<Regexp>)` brackets can be used for grouping
