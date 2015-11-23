---
layout: page
title: "Lab 9: Inheritance and Subtyping"
excerpt: "Lab 9: Inheritance and Subtyping"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you extend name binding and typing rules for MiniJava in order to support inheritance and subtyping.

## Overview

### Objectives

Specify inheritance and subtyping in MiniJava with name binding and type rules in NaBL, TS and Stratego and generate an Eclipse editor from it. The specification should include:

* Name binding rules for inheritance.
* Typing rules for
    * `extends` parts of class declarations,
    * class declarations and
    * a subtype relation.
* Custom constraint rules for
    * variables hiding inherited fields,
    * fields hiding inherited fields,
    * method overloading,
    * method overriding,
    * cyclic inheritance and
    * valid subtyping in assignments, method calls and return expressions.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment9` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submission is November 29th, 23:59.
{: .notice .notice-warning}

### Early Feedback

We provide early feedback for your language implementation.
This feedback gives you an indication which parts of your name and type analysis might still be wrong.
It includes a summary on how many tests you pass and how many points you earn by passing them.

If you scored 50 or more points during lab 7, you can also get more feedback for your tests until the deadline for this assignment.

### Grading

You can earn up to 90 points for the correctness of your name and type analysis.
Therefor, we run several test cases against your implementation.
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (6 points)
* constraints (84 points)
    * cyclic inheritance (12 points)
    * hiding variables and fields (12 points)
    * method overloading and method overriding (24 points)
    * subtyping in assignments, return expressions, method calls (36 points)

You can earn up to 10 points for your messages in errors and warnings.
We particular focus on
 readability,
 precision,
 and the level of detail in your messages.

## Detailed Instructions

### Git Repository

You continue with your work from the previous assignment.
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment9` branch from your previous work.

### Inheritance

Your current implementation does not consider inheritance.
Field names are resolved to field declarations in the surrounding class, but not to declarations in ancestor classes.
Similarly, method names are resolved to method declarations in the callee class, but not to declarations in its ancestors.

You can fix this by importing fields and methods from parent classes into their child classes with an `imports` clause.
A clause

```
imports NS1, NS2 from NS name
```

works like

```
refers to NS name
```

but additionally imports declarations of `NS1` and `NS2` from the referred scope into the current scope.
It only considers declarations which are declared in the referred scope, but not those which are imported into the referred scope.
`imported` is a keyword for one specific label, not a clause such as `imports`.
The following clause also imports declarations of `NS1` which are imported into the referred scope:

```
imports NS1, imported NS1, NS2 from NS name
```

### Subtyping

Your current typing constraints check for type equivalence.
However, subtyping is allowed in the following places:

1. In an assignment, the type of the assigned expression needs to be a subtype of the type of the variable.
2. In a method declaration, the type of the return expression needs to be a subtype of the declared method type.
3. In a method call, the types of the actual parameters need to be subtypes of the types of the formal parameters.
4. In method overriding, the return type of the overriding method needs to be a subtype of the return type of the overridden method.

You can achieve subtyping in three steps:

1. define and instantiate subtyping relations in TS.
2. make existing constraints work with subtyping where needed.
3. add new constraints to handle subtyping errors.

#### Defining the Subtype Relation

Relations in TS are used to model subtyping. A relation in TS is much like the regular definition of a logical relation, it is a set of binary term tuples that are in a certain relation with each other. For example, `class B extends A` indicates that `B` has a subtype relation with `A`. Relations allow matching such as: is class `B` in a subtyping relation with `A`?, which will be used to implement constraints later.

First, a relation and its properties must be defined

```
relations

  define property1, property2, ... <name:
```

The available properties for relations are:

* reflexive: If elements in the relation relate to them self.
* transitive: If `A` relates to `B`, and `B` relates to `C`, then `A` also relates `C`.
* symmetric: If `A` relates to `B`, then `B` relates to `A`.

A relation must be named so it can be used in other places. Note that name can also be empty, so `<:` can be used as relation name. You should define a subtyping relation and think about which properties it should have.

Now you can store new instances of subtype relations using regular type rules.

```
type rules

  pattern :-
  where store c <name: sc
```

This stores a new instance of a relation between `c` and `sc`.
TS has a limitation where references are not allowed on the left hand side of a relation, these must be definitions.
If you use a reference on the left hand side, the analysis will crash.
Now add corresponding rule(s) to store instances of subtype relations.

#### Using the Subtype Relation

You can use the subtype relation in TS in place of equality checks. Checks in the form of

```
ty1 == ty2
```

can be replaced by subtyping checks

```
ty1 <name: ty2
```

Like the equivalence check, relation checks also handle lists of types.
When the lists have different sizes, or when the relation does not hold for a pair in the lists, the relation check will fail.

Now you can update any constraints with subtyping checks where needed, and create new constraints to handle subtyping errors.

### Hiding Variables and Fields

You can now extend your constraints for hidden fields.
For this purpose, you can use `nabl-lookup-local-import(|ctx)` and `nabl-lookup-lexical-import(|ctx)`.
These strategies work similar to `nabl-lookup-local(|ctx)` and `nabl-lookup-lexical(|ctx)`.
They create a task to lookup a name in the current context,
but only consider declarations, which are imported into the current scope.
They do not consider declarations from surrounding scopes.
You should add checks for variable and field declarations, which hide fields from ancestor classes.
You should give warnings on variable declarations and report errors on field declarations.

### Method Overloading and Overriding

Next, you can specify constraints for overloaded and overridden method declarations.
You should report errors on overloading methods and give notes on overriding methods.

You can use the following template as a starting point:

````
nabl-constraint(|ctx) =
  ?Method(retty, mname, _, _, _, _)
  ; local    := <...(|ctx)> mname                                // lookup method declarations in the current class
  ; ...                                                          // report an error if there is more than one method declaration of that name
  ; imported := <...(|ctx)> mname                                // lookup method declarations in ancestor classes
  ; mty      := <type-lookup(|ctx)> mname                        // get the type of the current method
  ; paramty  := <new-task(|ctx)> Rewrite("parameter-types", mty) // get the parameter types of the current method
  ; ity      := <type-lookup(|ctx)> imported                     // get the type of the inherited method
  ; iretty   := <new-task(|ctx)> Rewrite("return-type", ity)     // get the return type of the inherited method
  ; iparamty := <new-task(|ctx)> Rewrite("parameter-types", ity) // get the parameter types of the inherited method
  ; matchp   := ...                                              // compare parameter types
  ; matchret := ...                                              // compare return types
  ; ...                                                          // report error if current method overloads inherited method
  ; ...                                                          // report error if current methods overrides inherited method with incompatible return type
  ; ...                                                          // report note if current method overrides inherited method correctly
  ; fail

task-rewrite: ("return-type", (_, rt))      -> rt
task-rewrite: ("parameter-types", (pt*, _)) -> pt*
````

The following strategies might be useful:

* `nabl-lookup-local(|ctx)` creates a resolution task that searches for a name, of the same namespace, in the same (local) scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical(|ctx)` creates a resolution task that searches for a name, of the same namespace, in the local and surrounding (lexical) scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-local-import(|ctx)` creates a resolution task that searches for a name, of the same namespace, that is imported into the local scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical-import(|ctx)` creates a resolution task that searches for a name, of the same namespace, that is imported into the current or parent scope.
  This strategy needs to be applied to a binding instance of a name.
* `<type-match(|ctx, ty1)> ty2` creates a task that checks if `ty1` matches type `ty2`.
* `<relation-create-match(|ctx)> ("<name:", ty1, ty2)` creates a task that checks for a tuple `("<name:", ty1, ty2)` if `ty1 <name: ty2` holds.
* `<task-create-error-on-triggers(|ctx, triggers, "Useful message")> term` creates an error message on a term when any triggers in the list succeed.
* `<task-create-warning-on-triggers(|ctx, triggers, "Useful message")> term` creates a warning message on a term when any triggers in the list succeed.
* `<task-create-note-on-triggers(|ctx, triggers, "Useful message")> term` creates a note on a term when any triggers in the list succeed.

You can use the following triggers:

* `Success(task)` triggers if `task` succeeds.
* `Failure(task)` triggers if `task` fails.

Triggers are combined into a list as follows: `[Success(task), Failure(task)]`.
Even if you only use a single trigger, it must be enclosed in a list: `[Success(task)]`.

### Cyclic inheritance

Finally, you should report an error message for cyclic inheritance on class definitions, using TS.
You can use:

```
pattern :-
where not(c1 <name: c2)
 else error "Useful message" on term
```

where `c1` and `c2` are names from the pattern, to show an error message when a relation succeeds.
