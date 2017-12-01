---
layout: page
title: "Lab 7: Type Analysis"
excerpt: "Lab 7: Type Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

This lab is under construction. Proceed at own risk.
{: .notice .notice-warning}

In this lab, you define type constraints for MiniJava in NaBL2. The concepts you are going to use in
NaBL2 are described in the following papers:

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

Specify type analysis for MiniJava in NaBL2. The specification should include:

1. Name binding constraints for
  * method calls.
2. Typing constraints for
  * integer and boolean literals,
  * unary and binary expressions,
  * variable and field references,
  * object creation,
  * `this` expressions,
  * method calls,
  * and the subtyping relation on classes.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment7` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submission is December 6th 2017, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 100 points for the correctness of your type analysis. Therefore, we run several
test cases against your implementation. You earn points, when your implementation passes test
cases. The total number of points depends on how many test cases you pass in each of the following
groups:

* name binding (20 points)
  * method declarations
  * method references
* typing rules (35 points)
  * literals (4 points)
  * unary expressions (5 points)
  * binary expressions (8 points)
  * variable and field references (4 points)
  * object creation (4 point)
  * `this` expression (5 points)
  * method call (5 points)
* constraints (45 points)
  * expressions (10 points)
  * statements (10 points)
  * subtyping in assignments, return expressions, method calls (10 points)
  * cyclic inheritance (2 points)
  * method overloading and method overriding (10 points)
  * main class usage (3 points)

### Early Feedback

We provide early feedback for your language implementation. This feedback gives you an indication
which parts of your name and type analysis might still be wrong. It includes a summary on how many
tests you pass and how many points you earn by passing them. You have 3 early feedback attempts.

## Detailed Instructions

### Git Repository

You continue with your work from the previous assignment.  See the
[Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the
`assignment7` branch from your previous work.

### Declaring Types

The first thing you need to do, is declare the types that are used in MiniJava. Most type
constructors were already given in the previous lab. Types are declared in the signature section of
an NaBL2 file. As an example, we show the type declaration for class types:

    signature
      constructors
        ClassType : occurrence -> Type
        // ... more constructors ...

We identify classes by the *declaration* from the class definition. For example, assume a `class A
{}` with corresponding AST `Class("A",_)`. Therefore, the class type constructor takes an occurrence
as argument. Instances of this class would have the type `ClassType(Class{"A"})`.

You should define the constructors for integers, arrays, and booleans similarly. You also need to
add one more type constructor for methods. This type should have two arguments, the return type, and
a list of argument types of the method. A type argument is indicated by `type`. Lists of types are
themselves considered types, so you can use `type` for the argument list as well.

### Constraint Generation Rules (revisited)

The constraint generation rules of lab 5 need to be extended with type constraints. Some rules will
only need extra constraints, some will need to assign a type to the AST node that it matches on. The
latter is the case for expressions, which have a type assigned to them. For example, a literal
`True()` will have type `Bool()`, and `This()` will have the type of the surrounding class. For AST
nodes that have a type assigned, the rules will now look like this:

    [[ <Pattern> ^ (<{Scope ","}*>) : <Type> ]] :=
      <Constraint>.

Similarly, whenever doing a recursive call, the type must now be added as well. Schematically, this
looks like this:

    [[ ... ]] :=
      ...,
      [[ <Var> ^ ({Scope ","}*) : <Type> ]],
      ...

Only the rules for sorts that have a type, like expressions, should be changed this way.
{: .notice .notice-warning}

If you used `Map1` to iterate over a list of expressions, you now need to use `Map1T`, which
includes a type as well. Consider the following recursive call over a list of expressions `es`:

    Map1T [[ es ^ (s) : tys ]]

This will apply rules of the form `[[ <Expr> ^ (<Scope>) : <Type> ]] := ...` to every element of the
list. The variable `tys` is instantiated to the list of types of the expressions. So, if `es` is
`[True(),False(),IntValue(_)]`, then `tys` will be `[Bool(),Bool(),Int()]` -- assuming you wrote the
correct rules for expressions of course.

The same general restrictions for constraint generation rules that applied in lab 5 still apply: 1)
Only one rule per constructor, and 2) no implicit traversal of the AST.
{: .notice .notice-warning}

Types can be concrete terms, like `Bool()`, or variables, like `ty`. You can use concrete types or
variables in both the rule match, and the recursive calls. The type that you pass in the recursive
call, and the type or variable you match on in the rule that is then applied generate an equality
constraint together. These are explained in the next section.

### Equality and Inference on Types

Types can be compared for equality using equality constraints. For example, the constraint

    Bool() == Bool()

would be satisfied. However, the constraint

    Bool() == Int()

would result in an error.

Sometimes the concrete type is not known, in which case you can use a variable for the unknown
type. For example, the type of a reference depends on the type of the declaration. We use the
variable `ty` to stand for the (unknown) type:

    [[ VarRef(x) ^ (s) : ty ]] :=
        // ... constraints ...

When variables are used in equality constraints, the constraint solver will try to find values for
them, such that the constraint is satisfied. Take for example the following three constraints:

    ty1 == Bool(),    // (1)
    ty2 == ty1,       // (2)
    ty2 == Int().     // (3)

The constraint solver will solve constraint (1) by assigning `ty1` the value `Bool()`. Now
constraint (2) can be satisfied by assigning `ty2` the value of `Bool()` as well. Such an assignment
of values is called a *unifier*. The unifier is built in such a way that, if possible, substituting
all variables for their assigned values will result in equal concrete terms on both sides of the
equal sign. In this example, there is no valid unifier, because the third constraint will try to
equate `Int()` with `Bool()`.

The order in which constraints are solved is not fixed -- it is not the same order as they are
generated by the constraint generation function. This makes it possible to use equality constraints
for some forms of non-local type inference.

You should introduce equality constraints in your rules wherever you expect types to be equal.

### Types for Declarations

It is also possible to assign types to declarations. This is done using constraints of the following
form:

    <Occurrence> : <Type>

At references, where you have a resolution constraint `<Occurrence> |-> <Var>`, you use the same
constraint, but with the variable, instead of a concrete occurrence, like this: 

    <Var> : <Type>

For example, the rule for references uses this pattern:

    [[ VarRef(x) ^ (s) : ty ]] :=
        // ...
        Var{x} |-> d,
        d : ty,
        // ...

Note that `d` will be instantiated to the declaring occurrence, making the constraints the same,
except for the types. Consider the case where the types do not match. For example, the declaration
is an integer, `Var{"x"} : Int()`, but the use site expects a boolean, `Var{"x"} : Bool()`. The
constraint solver treats all constraint equally, and will report an error on one of them. But in a
language like MiniJava, with type annotations, you want the error to appear at the use site, not the
definition site. This can be achieved by adding `!` to the constraint at declaration site, for
example, `Var{x} : ty !`. This indicates it is more important, and in case of a conflict with a
constraint without the exclamation mark, the error will always be reported on the less important
constraint.

Add constraints for the types of variables and references.

Be careful not to set a type on a reference! It will not automatically be unified with the type of
the declaration it resolves to.
{: .notice .notice-warning}

### Type-dependent Name Resolution

If you have done everything described above, your expressions all have types. Now we can implement
name resolution of method names. In lab 5 you wrote rules to create a scope graph which probably
look like this:

    [[ Class(x,_,_,_) ^ (s) ]] :=
        // ...
        Class{x} <- s,
        new cs,
        Class{x} ===> cs,
        // ...
 
Now, using the class declaration of a class type, we can get the class scope by using `<Occurrence>
?===> cs`. Note that `cs` will be a variable that is going to be instantiated during constraint
solving. Create the method reference in a new scope `ms` (any name will do). We connect this scope
to the variable scope using an edge, like `ms ---> cs`. After this, write constraints for resolving
the method reference, getting its type, etc.

Remember that you can check if method resolution works, by *Ctrl/Cmd + click* on a method call in an
example program.
{: .notice .notice-info}

### Subtyping

Classes in MiniJava support subtyping. Building the subtype relation is done using the following
constraint:

    <Type> <! <Type>

The relation must be a tree, which is automatically enforced by the constraint solver. Checking if
one type is a subtype of the other, can be done using the following constraint:

    <Type> <? <Type>

Note that these constraints will not be used to find values for variables that appear in the
types. They need to get values from somewhere else. If variables in these types remain free, the
constraint solver will report an error.

Build the subtype relation from class definitions, and check in the right places if values are
subtypes of the expected type.

### Overriding

Finally, write constraints to check if overriding is done correctly. Use the reference introduced
for method declarations, that refers to the super method, or itself. Check that the argument types
are the same. Also check that the return type is correct. Be careful, it does not have to be the
same, but it needs to be co-variant! Overloading or incorrect overrides should result in an error.
