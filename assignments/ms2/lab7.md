---
layout: page
title: "Lab 7: Type Analysis"
excerpt: "Lab 7: Type Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

The description of this lab is currently being revised. **You should
not work on this lab until revision is complete**, because important aspects
will change.
{: .notice .notice-warning}

{% include _toc.html %}

In this lab, you define typing rules and constraints for MiniJava.
From this definition, you generate an Eclipse editor, that provides type information in hover help and type constraint checking.

## Overview

### Objectives

Specify type analysis for MiniJava in NaBL, TS, and Stratego, and generate an Eclipse editor from it.
The specification should include:

1. Name binding rules for
  * `this` expressions,
  * method declarations and
  * method calls.
2. Typing rules for
  * integer and boolean literals,
  * unary and binary expressions,
  * variable and field references,
  * object creation,
  * `this` expressions,
  * and method calls.
4. Custom constraint rules for type errors in
  * expressions,
  * statements and
  * method declarations.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment7` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submission is November 28th, 17:00.
{: .notice .notice-warning}

### Grading

You can earn up to 75 points for the correctness of your type analysis.
Therefore, we run several test cases against your implementation.
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* name binding (10 points)
  * method declarations
  * method calls
* typing rules (30 points)
  * literals (3 points)
  * unary expressions (5 points)
  * binary expressions (10 points)
  * variable and field references (1 points)
  * object creation (1 point)
  * `this` expression (5 points)
  * method call (5 points)
* constraints (35 points)
  * expressions (25 points)
  * statements (10 points)

You can earn up to 20 points for your messages in errors and warnings.
We particular focus on
 readability,
 precision,
 and the level of detail in your messages.
Finally, you can earn up to 5 points with a challenge.

### Early Feedback

We provide early feedback for your language implementation.  This feedback gives you an indication
which parts of your name and type analysis might still be wrong.  It includes a summary on how many
tests you pass and how many points you earn by passing them. You have 3 early feedback attempts.

## Detailed Instructions

### Git Repository

You continue with your work from the previous assignment.  See the
[Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the
`assignment7` branch from your previous work.

### TS

You have already used TS in a previous lab to specify constraints.
In this lab, you specify typing rules for expressions and additional constraints in TS.

#### Debugging

Your test cases from the previous lab should enable a principled way of testing type analysis.
In addition, hover functionality in the MiniJava editor also offers a quick way to check if type analysis works as expected. When you move your mouse over an expression or definition in a MiniJava editor, you should see its type in a hover text.

When you get unexpected results, you can inspect index entries and
  collected tasks with the *Show analysis*, *Show tasks* and *Show index* builders.
Probably the most useful builders for you are those which show them only for the current file (also called a partition) or for a selection.

Consider the following task entry:

```
task 1 [2, 3] =
  produce &2
  ->
  [Int()]
```

This entry consists of

* an identifier `1`,
* a list `[2, 3]` of dependencies on other tasks,
* an instruction `produce &2` which refers the result of task `2`,
* and a list of results `[Int()]`.

You can navigate the dependencies of tasks to find the reasons for failing tasks.
The following instructions exist:

* `resolve ns n in s* wrt props []` resolves a name `n` of namespace `ns` in scopes `s*`.
* `choose x <+ y` chooses from alternatives `x` and `y` deterministically.
  If `x` is a term or a task with a result, `x` is chosen.
  Otherwise, `y` is chosen.
* `concat t1 + t2` combines the results of tasks `t1` and `t2`.
* `Message(tsk, [], result, trm, msg)` shows an error message `msg` on term `trm` based on the `result` (either succeeding, failing, or producing multiple results) of task `tsk`.
  There are similar variants for warnings and other conditions such as the success of a task.
* `produce x` produces result `x`.
* `CollectDefs(x)` retrieves the definition of a reference `x`.
* `lookup Type() props on d` looks up the type of a definition `d`.
* `check Type() prop t1 against t2 wrt Eq()` checks if types `t1` and `t2` are equal.
* `rewrite t wrt s` rewrites term `t` using strategy `s`.

### Typing Rules

Typing rules specify the types of expressions.

#### Literals

In the simplest case, the type of an expression is directly known.
A typical example for such expressions are literals.
For example, integer literals are of type `int`:

```
IntValue(_): Int()
```

Specify typing rules for the other literals.

#### Unary and Binary Expressions

Types for unary and binary expressions are also directly known.
For example, the `not` expression is of type `boolean`:

```
UnExp(NotOp(), _): Boolean()
```

Note that these expressions have been desugared.
See the `reference/desugar-signatures.str` file for their signatures, or run the `Syntax -> Show desugared syntax` builder on a MiniJava file to see the AST.

Specify typing rules for all kinds of unary and binary expressions.

#### References

For references, you need to lookup the type of the corresponding definition.
Types of definitions are specified in NaBL rules.
You should have specified these types in the name analysis assignment:

```
...: defines Field f of type t
```

In TS, you can lookup the type of definitions.
You already did this for the constraint rules in a previous lab, but this time the type will actually be used.
Lookup the type of a definition as follows:

```
e: ty
where definition of r: ty
```

Here, `r` should be a reference in the expression `e`.

Specify typing rules for variable and field references, and object creation.

Do not modify your existing constraint rules to achieve this. Instead, add additional type rules that assign types to references.
TS supports overlapping rules, as long as typing rules of the form `e : ty` do not overlap with each other.
Constraint rules of the form `e :-` can overlap with typing rules and other constraint rules.
{: .notice .notice-info}

### Name Binding Revisited

#### `this` Expressions

To type `this` expressions, you first need to specify a name binding rule for `this`.
Since `this` is nowhere specified explicitly,
  you need to add an implicit definition clause to one of your existing name binding rules in NaBL.
An implicit definition has the following form:

```
...: implicitly defines Namespace name of type t
```

It adds an additional definition for `name` in namespace `Namespace` to a name binding rule that already defines some other name.

Think about the scoping of `this` and what type it should have, then add an implicit definition for `this` to one of your existing name binding rules.
You might want to reuse an existing namespace or define a new one for `this`.
If you use a new namespace, you need to scope this namespace properly.
As a name, use the constructor for `this`: `This()`.
Implicit definitions can also have properties, which allows you to specify the type of `this`.

Next, you should specify a name binding rule in NaBL which resolves a `this` expression to the implicit definition you just added.
Again, you should use the constructor for `this` as the name in this rule.

Finally, define a typing rule which looks up the type of `this`.
You need to make sure that `definition of` is applied to the term matched by the rule, not to a new term `This()`.
You can achieve this by binding a variable `v` to the term matching `this`:

```
v@This(): ...
where definition of v ...
```

#### Method Calls

Method calls need to be resolved with respect to the type of the callee expression.
This expression needs to be of a class type and the method call should resolve to a method in the corresponding class.
Such contextual references are specified in NaBL as follows:

```
...:
  refers to Namespace1 name1 in Namespace2 name2
  where e has type ty
```

The `where` clause requires an expression `e` to be of type `ty`.
`name1` is then resolved in the scope of `name2`.

You need to instantiate this pattern for method calls which resolve to methods inside classes.
You should use a pattern for `ty`, which binds `name2`.
Add a name binding rule for method calls in NaBL.

To type a method call, you need to define the type of a method name definition:

```
...: defines Method m of type t
```

Modify your name binding rule for method declarations to include its type.
Next, specify a type rule in TS for method calls, which looks up the type of the method declaration.

### Constraints

Now that all expressions have a type, it is time to specify typing constraints.
All constraints are specified in TS.

#### Unary and Binary Expressions

Subexpression in unary and binary expressions need to be checked.
In TS, equality of types can be checked against an expected type with `==`:

```
BinExp(Plus(), e1, _) :-
where e1 : ty
  and ty == expected-ty
 else error "Useful message" on e1

BinExp(Plus(), _, e2) :-
where e2 : ty
  and ty == expected-ty
 else error "Another useful message" on e2
```

You can add names and types to your error messages in TS using the following syntax:

```
 else error $[Useful message with type [ty]] on e1
```

Do not add expressions to your messages, this can cause your analysis to fail.
{: .notice .notice-warning}

Specify constraints for all kinds of unary and binary expressions.
To prevent cascading errors when a constraint fails, you should add new constraint rules instead of extending your existing rules.
Also separate the left and right-hand subexpression checks for binary expressions, like in the example above.
This is to prevent error messages on the right-hand expression from showing when the constraint on the left-hand expression fails.

#### Statements

Statements typically do not have a type, but they might expect a certain type of an expression in the statement.
For example, an `if` statement requires a boolean expression.

Specify constraints for `if` statements, `while` statements, print statements, and assignments.
Again, keep constraints separate to prevent cascading.

#### Method Declarations

Specify a constraint which checks the type of a return expression against the declared return type of the method declaration.

#### Method Calls

Finally, you need to check the arguments of method calls, which can have missing arguments, too many arguments, or wrong argument types.

To check this, you need to modify your name binding rule for method declarations again, this time to associate each method declaration with a more sophisticated type.
This type should include the expected types for the parameters and the return type of the method.
You can construct such a type as a tuple `(pty*, ty)` of parameter types `pty*` and return type `ty`.

Similar to the rule for method calls, you can collect the parameter types with a `where x has type tys` clause.
When this clause is used on a list `x`, it will automatically retrieve the types of elements in `x`, and produce a list of types `tys`.
To make this work, you also need to create a type rule for parameters in TS:

```
Param(t, _): ...
```

Before you continue, you should check if the type associated with the method name carries all the information you need.
Hover over a method name, the tooltip should include the type which should look like: `([p1ty, p2ty, ..., pnty], rty)`.
Do not continue, until this is working.
{: .notice .notice-warning}

Since the type of method definitions has changed, you should modify your type rule for method calls to extract the return type from the tuple `(pty*, ty)`.

Next, add a constraint in TS which checks the argument types.
The type lookup will now yield the more sophisticated type.
You need to extract the parameter types and the return type from the tuple.
You can use a pattern for `sophisticated-type` which matches a tuple with `parameter-types` and `return-type`:

```
... :-
where definition of m: sophisticated-type // lookup sophisticated type and match parameter-types and return-type
  and ...                                 // get the actual argument types from the method
  and ...                                 // check actual argument types w.r.t. parameter types
 else ...                                 // show error if types are not equal
```

The type equivalence operator `==` in TS also works on lists of types.
It also checks if both lists are of the same size, and fails if they are not.

Think about where the error message should appear if the parameter and argument types do not match.
Even when there are no arguments (the argument list is empty), the error should appear somewhere.

### Challenge

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation or higher programming skills.
{: .notice .notice-success}

Currently, the rules for unary and binary expressions do not benefit from desugaring. This can be improved, by adding typing rules for unary and binary operators. Similar to the types of method names, the type of an operator should encode the type of its arguments and its result.

Provide typing rules for all unary and binary operators. Next, provide a single typing rule for unary expressions, which inspects the type of its unary operator. In a similar fashion, provide a single constraint rule for unary expressions. Finally, provide a single typing rule for binary expressions, which inspects the type of its binary operator. Provide two constraint rules for checking type errors in the left and right subexpression of a binary expression.

## Overview

### Objectives

Specify inheritance and subtyping in MiniJava with name binding and type rules in NaBL, TS and Stratego and generate an Eclipse editor from it. The specification should include:

* Name binding rules for inheritance.
* Typing rules for
    * `extends` parts of class declarations,
    * class declarations and
    * a subtype relation.
* Custom constraint rules for
    * warnings on variables hiding inherited fields,
    * errors on fields hiding inherited fields,
    * errors for method overloading,
    * notes for method overriding,
    * errors for cyclic inheritance and
    * errors for invalid subtyping in assignments, method calls and return expressions.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment9` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submission is November 29th, 23:59.
{: .notice .notice-warning}

### Early Feedback

We provide early feedback for your language implementation. This feedback gives you an indication
which parts of your name and type analysis might still be wrong. It includes a summary on how many
tests you pass and how many points you earn by passing them. You have 3 early feedback attempts.

If you scored 50 or more points during lab 7, you can also get more feedback for your tests until the deadline for this assignment.

### Grading

You can earn up to 90 points for the correctness of your name and type analysis.
Therefore, we run several test cases against your implementation.
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
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment7` branch from your previous work.

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
* `nabl-lookup-local-import(|ctx, ns)` does the same as above, but considers only names in namespace `ns`.
  You need to pass a term for the namespace of interest here. Constructor for such terms are generated from your
name binding specification, for example `NablNsClass()`.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical-import(|ctx)` creates a resolution task that searches for a name, of the same namespace, that is imported into the current or parent scope.
  This strategy needs to be applied to a binding instance of a name.
* `nabl-lookup-lexical-import(|ctx, ns)` does the same as above, but considers only names in namespace `ns`.
  You need to pass a term for the namespace of interest here. Constructor for such terms are generated from your
name binding specification, for example `NablNsClass()`.
* `<type-match(|ctx, ty1)> ty2` creates a task that checks if `ty1` matches type `ty2`.
* `<relation-create-match(|ctx)> ("<name:", ty1, ty2)` creates a task that checks for a tuple `("<name:", ty1, ty2)` if `ty1 <name: ty2` holds.
* `<task-create-error-on-triggers(|ctx, triggers, msg)> term` creates an error message on a term when all triggers in the list succeed.
* `<task-create-warning-on-triggers(|ctx, triggers, msg)> term` creates a warning message on a term when all triggers in the list succeed.
* `<task-create-note-on-triggers(|ctx, triggers, msg)> term` creates a note on a term when all triggers in the list succeed.

You can use the following triggers:

* `Success(task)` triggers if `task` succeeds.
* `Failure(task)` triggers if `task` fails.

Triggers are combined into a list as follows: `[Success(task), Failure(task)]`.
Even if you only use a single trigger, it must be enclosed in a list: `[Success(task)]`.

As messages, you can use a simple string like `"Useful message"`, or a list of strings and variables if you'd like to include variables in your message, like `["Fancy message with variable ", v, " and other variable ", t]`.

### Cyclic inheritance

Finally, you should report an error message for cyclic inheritance on class definitions, using TS.
You can use:

```
pattern :-
where not(c1 <name: c2)
 else error "Useful message" on term
```

where `c1` and `c2` are names from the pattern, to show an error message when a relation succeeds.
