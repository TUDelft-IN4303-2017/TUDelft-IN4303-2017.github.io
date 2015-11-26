---
layout: page
title: "Lab 6: Name Analysis"
excerpt: "Lab 6: Name Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you define name bindings and corresponding constraints for MiniJava.
From this definition, you generate an Eclipse editor, that provides
  name resolution, content completion, and constraint checking.

## Overview

### Objectives

Specify name analysis for MiniJava in [NaBL](http://metaborg.org/nabl/), [TS](http://metaborg.org/ts/) and [Stratego](http://metaborg.org/stratego/), and generate an Eclipse editor from it.
The specification should include:

1. Name binding rules for
  * class declarations
  * class references
  * field declarations
  * method declarations
  * parameter declarations
  * variable declarations
  * field, parameter, and variable references
2. Scoping rules for
  * class declarations
  * method declarations
  * parameter declarations
  * field declarations
  * variable declarations
3. Typing rules for
  * main class declarations
  * field declarations
  * parameter declarations
  * variable declarations
4. Custom constraint rules in TS for
  * unresolved references to classes, fields, parameters, and variables
  * references to the main class
5. Custom constraint rules in Stratego for
  * duplicate definitions of classes, fields, parameters, and variables
  * variable declarations which hide local field declarations

### Submission

You can work on labs 5 and 6 in the same branch of your repository, but for grading and early feedback you need to submit your editor project with a pull request against branch `assignment6` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submissions is November 8th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 80 points for the correctness of your name analysis.
Therefore, we run several test cases against your implementation.
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (20 points)
  * class declarations and references (6 points)
  * method declarations (2 point)
  * variable and field declarations (6 points)
  * variable and field references (6 points)
* scopes (10 points)
* constraints (35 points)
  * duplicate definitions (10 points)
  * unresolved references (10 points)
  * hiding variables (5 points)
  * main class references (10 points)
* challenge (5 points)

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

You continue with your work from the previous assignment.
There is no need to create a new branch in your repository for this assignment, keep working in the `assignment5` branch.
When you make a pull request, be sure to make a pull request to the `assignment6` branch of the upstream repository though.

#### Initial Editor Project

The initial editor project from the last assignment contains the desugarings implementation `reference/desugar.rtree`, in addition to implementations of the previous initial project. The desugarings can be used by importing `reference/desugar`.

### Name Binding

In Spoofax, name bindings are specified in NaBL.
NaBL stands for *Name Binding Language* and the acronym is pronounced 'enable'.
Name binding is specified in terms of
  namespaces,
  binding instances (name declarations),
  bound instances (name references),
  scopes and
  imports.

To start a new name binding specification,
  you need to create a `.nab` file in directory `trans` or one of its subdirectories.

```
module module-name

imports

  reference/src-gen/signatures/-
  reference/-
```

The module name has to be the same as the file name and should include the path relative to the `trans` directory.
For example, a file `foo.nab` in directory `trans/bar` should define a module `bar/foo`.
When you save an NaBL file, a corresponding Stratego file will be generated from it.
This file contains implementation strategies for your name analysis.
You need to import this file into `trans/minijava.str`.
When you build your project, your name binding rules become effective in your MiniJava editor.

#### Variable Names

We use variable names as an example to get you started with [NaBL](http://metaborg.org/nabl/).
First, you start a `namespaces` section and define a namespace `Variable`:

```
namespaces

  Variable
```

In NaBL, a *namespace* is a collection of names and is not necessarily connected to a specific language concept.
Different concepts can contribute names to a single namespace.
For example, in MiniJava variables and parameters contribute to the same namespace.

Once you have defined a namespace,
you can define name bindings rules in a `binding rules` section:

```
binding rules

  Var(_, v): defines Variable v

  VarRef(v): refers to Variable v
```

Each binding rule is of the form `pattern : clause*`,
  where `pattern` is a term pattern (like in Stratego rewrite rules) and
  `clause*` is a list of name binding specifications about the language construct that is matched by `pattern`.
For example, the first rule specifies binding instances of class names.
Its pattern matches regular MiniJava variable declarations (but not parameter declarations).
Its `defines` clause states that these variable declarations are definition sites for variable names (i.e. they bind variable names).
Similarly, the second rule specifies bound instances of variable names.
Its pattern matches variable references and
its `refers` clause states that such references are use sites for variable names (i.e. they refer to bound variable names).
When a name can refer to different namespaces, you can combine refer clauses with `otherwise`, e.g.

```
VarRef(name) : refers to Namespace1 name otherwise refers to Namespace2 name
```

When you save the file and build your project,
  you will get reference resolution for variable names in your MiniJava editor.
You might recognise that reference resolution works across files in the same Eclipse project.
Though this is a nice feature, this is incorrect for MiniJava,
 where methods scope their variable declarations.
You can specify this scoping behaviour in a binding rule:

```
Method(_, _, _, _, _, _): scopes Variable
```

The pattern of this rule matches method declarations and its `scopes` clause specifies that this is a scope for variable names.

When using multiple clauses in your patterns, be sure to combine them in a single binding rule. If they are spread across multiple binding rules it can occur that some clauses are ignored. The following is an example of correct usage when using multiple clauses:

```
Method(_, name, _, _, _, _):
  defines Bar name
  scopes Foo, Tux
```

as compared to incorrect usage:

```
Method(_, name, _, _, _, _): defines Bar
Method(_, name, _, _, _, _): scopes Foo, Tux
```

#### More Name Binding Rules

You are now able to complete the name binding rules for MiniJava. You should come up with:

1. more name binding rules for variable names (there are more definition sites of variable names),
2. namespaces and name binding rules for class declarations, method declarations and field declarations,
3. scoping rules for these declarations and
4. name binding rules for variable and field references.

Build your editor and test your name binding specification with your SPT tests and in the editor.

#### Properties of Binding Instances

You can define certain properties of binding instances.
`type` is a built-in property and you should define the type of field and variable declarations.
You can do so in a special `defines` clause:

```
Var(t, v): defines Variable v of type t
```

This associates the type `t` with the variable name `v`.
You can see associated types by hovering over definition or use sites in the editor.

### Custom Constraints

NaBL provides generic checks for duplicate definitions and unresolved references.
In order to give specific error messages, you need to replace them with your own constraints.
First, you need to create a new Stratego file in `trans` or one of its subdirectories:

```
module module-name

imports

  reference/src-gen/signatures/MiniJava-sig
  reference/desugar
  lib/runtime/nabl/-
  lib/runtime/task/-
  lib/runtime/types/-
```

Again, the module name has to be the same as the file name and should include the path relative to the `trans` directory. You need to import this file into `trans/minijava.str`.
Now you can turn off the generic checks, add the following code:

```
strategies

  nabl-check-disable-duplicate(|uri, ns) = id
  nabl-check-disable-hiding(|uri, ns) = id
  nabl-check-disable-unresolved = id
```

You now need to specify custom constraints in TS and in Stratego.

#### Property-based Constraints in TS

[TS](http://metaborg.org/ts/) is a high-level language for the declarative specification of type analysis that is complementary to the name analysis expressed in NaBL.
In upcoming labs, we will use TS to specify MiniJava's type system.
In this lab, we only use it to specify constraints for unresolved references and main class constraints.

To start a new type system specification,
  you need to create a `.ts` file in directory `trans` or one of its subdirectories.

```
module module-name

imports

  reference/src-gen/signatures/-
  reference/-
```

Again, the module name has to be the same as the file name and should include the path relative to the `trans` directory.
For example, a file `foo.ts` in directory `trans/bar` should define a module `bar/foo`.

TS will give errors when importing Stratego files, this is a bug and you can safely ignore those errors.
{: .notice .notice-warning}

When you save a TS file, a corresponding Stratego file will be generated from it.
You may have to refresh the project (right click project, choose Refresh) to see the new file.
This file contains implementation strategies for your type analysis.
You need to import this file into `trans/minijava.str`.
When you build your project, your name binding rules become effective in your MiniJava editor.

You can define constraints in a `type rules` section:

```
type rules

VarRef(v) :-
  where definition of v : t
    else error "a fancy error message" on v
```

You can also add variables to your error messages in using the following syntax:

```
  else error $[a fancy error message with variable [t]] on v
```

Note that `:-` must be a single token, `: -` is not recognized.
{: .notice .notice-warning}

This rule reports errors on unresolved variable references.
Therefor, it first checks the type of its definition.
Since all variable declarations have a type, this can only fail if there exists no variable declaration to which the variable reference can be resolved.
Otherwise, an error is reported.

Similarly, you can report errors on referencing the main class.
Therefor, you need to assign types to class declarations.
You can use the assigned type to distinguish ordinary class declarations from main class declarations.
That is, you can define a constructor for a special type and associate the main class definition with this type.

In NaBL, set the type of main class definitions to this special type.
Then, in TS, you can use this to check if a class reference refers to the main class:

```
ClassType(c) :-
  where not(definition of c : YourSpecialTypeConstructor())
    else error "another fancy error message" on c
```

This rule reports errors on class types which refer to the main class.
It matches the type of the class declaration against your special type.
If this match is successful, an error is reported.
You should define your own special type constructor and provide a meaningful error message.
Next, you can do similar checks for main class subtyping and main class instantiation.

#### Tasks

Spoofax uses tasks for name and type analysis.
A task is a unit of computation, which might depend on facts (such as name declarations or properties) or on the results of other tasks.
Based on your NaBL and TS rules, tasks are collected in a traversal, before they are evaluated.
Results of evaluated tasks are cached.
When a file is changed, tasks are only re-collected for this file.
A task is only re-evaluated, if it is new or if one of its dependencies changed.
This might include tasks which originated from a different file than the file that changed.
Tasks provide incremental name and type analysis in Spoofax.

You can also see such associations in the index (try the *Show index > Partition* builder).
The index stores all definitions and properties of these definitions.
Search for entries of the form `prop URI -- Type() >> Result`.
The `URI` specifies the definition by naming its scopes.
The `Result` refers to a task which has the property as a result.
You can see tasks and their results with the *Show tasks > Partition* builder.
Alternatively, you can also use the *Show index > Partition (resolved)* builder, which inlines the results of tasks.

When you change name binding and typing rules, you might actually confuse the incremental analysis, which is designed to handle changing programs, but not changing rules. You can fix this by right-clicking the project containing your example programs and chosing *Spoofax -> Reload analysis data*.
{: .notice .notice-warning}

#### Constraints in Stratego

Constraints for duplicate definitions and hiding cannot be expressed in the current version of TS.
These constraints need to be defined in Stratego, add the rest of the constraints to the Stratego file that you created earlier which disables generic checks.
You can define constraints by providing rewrite rules for `nabl-constraint(|ctx)`.

```
rules

  nabl-constraint(|ctx):
    Var(t, v) -> <fail>
    where
      task := <nabl-lookup-local(|ctx)> v
    ; msg  := "Your meaningful error message should be here"
    ; <task-create-error-on-multiple(|ctx, task, msg)> v
```

This rule matches a language construct on the left-hand side (in this case a variable declaration) and fails on the right-hand side.
The failure ensures that you can report different errors on the same language construct.
The shown rule reports an error on duplicate variable names.
Errors can be reported on failing tasks, succeeding tasks, or tasks with multiple solutions.
In the current example of duplicate definitions, this needs to be a resolution task with multiple solutions.
The `where` clause of the rule creates this `task` from the variable name.
Next, an error message `msg` is created.
You should replace this with a meaningful message.
You can use string interpolation to include elements from the matched term in the error message.
Finally, a library strategy is called to create an error in case of multiple solutions.
The arguments to this strategy are

1. a context variable `ctx`,
2. the `task` which needs to have multiple solutions to trigger the error message, and
3. the error message `msg`.

The strategy is applied to the term where the error should be shown (in this case, the variable name).
You should follow this pattern to provide custom error messages for all kinds of duplicate definitions and for local variable declarations which hide field declarations.

The following library strategies might be useful:

* `nabl-lookup-local(|ctx)` creates a resolution task that searches for a name, of the same namespace, in the same (local) scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical(|ctx)` creates a resolution task that searches for a name, of the same namespace, in the local and surrounding (lexical) scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical(|ctx, ns)` does the same as above, but considers only names in namespace `ns`.
  You need to pass a term for the namespace of interest here. Constructor for such terms are generated from your name binding specification, for example `NablNsClass()`.
* `task-create-error-on-success(|ctx, task, msg)` creates an error message `msg`, if `task` has one or more solutions.
* `task-create-error-on-failure(|ctx, task, msg)` creates an error message `msg`, if `task` has no solution.
* `task-create-error-on-multiple(|ctx, task, msg)` creates an error message `msg`, if `task` has more than one solution.
* `task-create-warning-on-failure`, `task-create-warning-on-success` and `task-create-warning-on-multiple` are variants for warnings instead of errors.

### Challenges

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation or higher programming skills.
{: .notice .notice-success}

1. Enhance your error messages with information about scopes.
   For example, you might want to mention the surrounding method of a variable which hides a field.
   You can retrieve such information from the annotations of a binding instance.
   To retrieve annotations, you should use strategies from the [standard library](http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/).
   To analyse the annotated URIs, you should use strategies from `lib/runtime/nabl/uri`.

2. In a post-analysis desugaring, replace `VarRef(x)` terms which refer to fields and not to variables with a term `FieldRef(x)`. Implement this with a strategy `post-desugar-all` and extend the analysis rules to

```
rules // Analysis

  editor-analyze = analysis-default-editor

  analysis-single-default-interface =
    analysis-single-default(desugar-all, post-desugar-all, id|<language>)
  analysis-multiple-default-interface =
    analysis-multiple-default(parse-file <+ !(), desugar-all, post-desugar-all, id|<language>, <project-path>)
```
