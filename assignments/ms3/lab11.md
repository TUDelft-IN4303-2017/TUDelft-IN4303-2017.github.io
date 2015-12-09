---
layout: page
title: "Lab 11: More Code Generation"
excerpt: "Lab 11: More Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

In this lab, you extend your code generator to handle expressions, statements, methods without local parameters, and classes without fields.

## Overview

### Objectives

1. Write MiniJava programs and corresponding Jasmin programs which cover the following constructs:
    * all kinds of unary and binary expressions,
    * object and array creation,
    * `this` expressions,
    * method calls without arguments,
    * block statements,
    * `if` statements,
    * `while` statements,
    * methods without parameters and local variables,
    * classes without fields.
2. Extend your code generator to handle these constructs.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment11` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submissions is December 13th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 20 points for your example MiniJava programs and their corresponding Jasmin programs.
We will focus on completeness and correctness of your examples.
Furthermore, you can earn up to 70 points for your code generator:

* transformation (55 points)
    * classes (8 points)
    * methods (7 points)
    * statements (13 points)
        * block statements (2 points)
        * `if` statements (5 points)
        * `while` loops (6 points)
    * expressions (27 points)
        * logic constants (1 point)
        * unary expressions (5 points)
        * binary expressions  (14 points)
        * object and array creation (1 point)
        * `this` expressions (1 point)
        * method calls (5 points)
* challenges (15 points)
    * overlays (2 points)
    * reusable method descriptors (3 points)
    * stack limits (10 points)

You can earn up to 10 points for the quality of your code.
We focus on readability in general, meaningful variable names and the consistent use of Stratego paradigms.
We will consider the fact that Stratego is new to you.

### Early Feedback

This assignment is graded manually. Thus, we do not provide early feedback for this submission.

## Detailed Instructions

### Git Repository

You continue with your work from the previous assignment.
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment11` branch from your previous work.

### Write More Jasmin Code

Before you extend your code generator, you should come up with small example MiniJava programs, which cover the following constructs:

* all kinds of unary and binary expressions,
* object creation,
* `this` expressions,
* method calls without arguments,
* block statements,
* `if` statements,
* `while` statements,
* methods without parameters and local variables,
* classes without fields.

Write corresponding Jasmin programs, which you expect to be the result of a MiniJava-to-Jasmin compiler.
Generate Java class files from them and run them.
Improve your programs until they run without errors.

Do not write all the MiniJava programs and the corresponding Jasmin code in one go.
Instead, you should extend your code generator once you figured out the Jasmin code for a certain construct.
This way, you can go back and forth between manually writing Jasmin code and extending the code generator.

### Generate Code for Expressions and Statements

You now need to extend `stmt-to-jbc` and `exp-to-jbc` to cover all statements except assignments.

1. Provide a rule for `stmt-to-jbc`, which translates a block statement from MiniJava into a sequence of Java bytecode instructions.
   This rule should call `stmt-to-jbc` recursively to translate the inner statements to Java bytecode sequences.

2. Provide rules for `exp-to-jbc`, which translate logic constants `true` and `false` from MiniJava into sequences of Java bytecode instructions.

3. Provide rules for `stmt-to-jbc`, which translate `if` and `while` statements from MiniJava into sequences of Java bytecode instructions.
   These rules should call `stmt-to-jbc` recursively to translate inner statements to Java bytecode sequences.
   The JVM documentation discusses an efficient translation pattern for `while` loops.

   These statements require labels.
   You can apply `newname` from Stratego's standard library to a string, to obtain a fresh name starting with the given string.

4. Provide rules for `exp-to-jbc`, which translate unary and binary expressions from MiniJava into sequences of Java bytecode instructions.
   These rules should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.
   Furthermore, they should call a strategy `op-to-jbc` to translate unary and binary operators to Java bytecode instructions.
   The only exception is the `&&` operator, which needs to be treated differently, due to its lazy evaluation semantics.

You can test each rule by selecting a code fragment in the MiniJava editor and running your code generation builder.

### Code Generation for Methods

Now you need to define a strategy `method-to-jbc` to handle methods without local variables.

1. Provide a rule for `method-to-jbc`, which translates a method without parameters and local variables from MiniJava into a Jasmin method. This rule should call `stmt-to-jbc` to translate the statements of the method to a Java bytecode sequence.

2. Provide a rule for `exp-to-jbc`, which translates `this` expressions from MiniJava into sequences of Java bytecode instructions.

3. Provide a rule for `exp-to-jbc`, which translates method calls without arguments from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.
   For this rule, you need to know the name of the class containing the method and the type of the method.
   You can extract the class name from the URI of the method name.
   The following strategies might be useful:
    * `nabl-uri` extracts the URI from an annotated name.
    * `nabl-uri-parent` rewrites an URI to the URI of the enclosing scope.
    * `nabl-uri-name` rewrites an URI to the name of the definition it identifies.

   You can query the type associated with the method name with `get-type`.
   This requires access to the index and the task engine, which needs to be setup.
   You should do this in your builder strategies by adding the following strategy calls:

        index-setup(|<language>, project-path); task-setup(|project-path)

4. Extend the rule for `class-to-jbc`, which handles empty classes, in order to include code generation for methods.

5. Provide a rule for `exp-to-jbc`, which translates object creation expressions into sequences of Java bytecode instructions.

## Challenges

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation and programming skills.
{: .notice .notice-success}

### Overlays

Code generation rules become hard to read, when you construct large ASTs.
Often, these rules inject only small parts into a skeleton AST.
For example, the only variable parts in the default constructor of a class are its name `c` and the name of its super class `p`:

    JBCMethod(
      [PUBLIC()]
    , Init()
    , JBCMethodDesc([], Void())
    , [ JBCLimitStack("1"), JBCLimitLocals("1")
      , JBCVarDecl("0", "this", Reference(CRef(c)), LabelRef("start"), LabelRef("end"))
      , JBCLabel("start")
      , ALOAD_0()
      , INVOKESPECIAL(JBCMethodRef(CRef(p), MRef(Init()), JBCMethodDesc([], Void())))
      , RETURN()
      , JBCLabel("end")
      ]
    )

Furthermore, you might use the same pattern in different rules, e.g. in code generation rules for the main class and for ordinary classes.
One way to avoid code duplication, are helper strategies.
For example, you can have a strategy `to-jbc-constructor` which rewrites a pair of class and super class names to the default constructor.
Alternatively, you can use _overlays_, which allow the specification of pattern abstractions.

Overlays are specified in their own section.
They are named, can be parametrised, and can be nested:

    overlays

      DEFAULT-CONSTRUCTOR(c, p) =
        JBCMethod(
          [PUBLIC()]
        , Init()
        , JBCMethodDesc([], Void())
        , [ JBCLimitStack("1"), JBCLimitLocals("1")
          , THIS-DECLARATION(c)
          , JBCLabel("start")
          , ALOAD_0()
          , INVOKE-CONSTRUCTOR(p)
          , RETURN()
          , JBCLabel("end")
          ]
        )

You can use overlays like terms in your rules, both for matching and building:

    rules

      class-to-jbc:
       Class(c, e, fs, ms) -> JBCFile(header, jbc-fs, [ DEFAULT-CONSTRUCTOR(c, p) | jbc-ms ])
       where ...

Identify AST patterns in your code generation rules and come up with overlays to improve the readability of these rules.

### Reusable Method Descriptors

In Java bytecode, method declarations and method calls include a method descriptor.
You can construct such an descriptor from the type associated with a method name.
In the current setup, you do this construction once for a method declaration and once for each method call.
However, the descriptor for a call is the same as for the declaration the call refers to.
You can avoid the reconstruction by storing a custom property on the method name.
There are two variants to achieve this.
In the first variant, you store method descriptors (`MethodDescriptor`).
In the second variant, you store method references (`MethodRef`).

#### Variant 1: Storing Method Descriptors

First, you need to declare a property `descriptor` for namespace `Method` in a NaBL file:

    properties

      descriptor of Method: MethodDescriptor

Next, you need to store the property at a method declaration by defining a Stratego rewrite rule for `nabl-prop-site`:

    nabl-prop-site(|lang, ctx, uris, states, implicits):
      Method(ty, mname, param*, var*, stmt*, exp) -> <fail>
      where
        descr := ... // create descriptor
      ; <store-descriptor(|ctx, descr)> mname

Finally, you can access the descriptor in your code generation rules with `get-descriptor`:

    method-to-jbc:
      Method(ty, mname, param*, var*, stmt*, exp) -> ...
      where
        descr := <get-descriptor> mname
      ; ...

    exp-to-jbc:
      Call(e, mname, e*) -> ...
      where
        descr := <get-descriptor> mname
      ; ...

#### Variant 2: Storing Method References

In an alternative approach, you can store complete method references instead of just method descriptors.
The property declaration in NaBL and the rewrite rule to store the property are similar to the first variant.
But this variant will require you to extract descriptors from method references at definition sites:

    method-to-jbc:
      Method(ty, mname, param*, var*, stmt*, exp) -> ...
      where
        ref   := <get-reference> mname
      ; descr := ... // extract descriptor from ref
      ; ...

As a benefit, the rule for method calls becomes simpler:

    exp-to-jbc:
      Call(e, mname, e*) -> ...
      where
        ref := <get-reference> mname
      ; ...

### Precise Stack Limit Directives

A stack limit directive tells the Java Virtual Machine the maximum number of elements at the operand stack.
To give a precise limit, you need to write a strategy `stack-limit` that maps MiniJava expressions and statements to a stack limit 

Do not do this analysis on the Java bytecode level.
{: .notice .notice-error}

For expressions, you need to consider the number of elements already on the stack before the expression is evaluated. You can either pass this number as a term parameter or adapt intermediate results accordingly.
For statements, you do not need this information since statements should not leave any elements on the stack.

The following strategies might be useful:

* `inc`  rewrites an integer number to its successor.
* `max` rewrites a pair of integer numbers to the maximum of both numbers.
* `addi` rewrites a pair of integer numbers to the sum of both numbers.
* `foldr(s1, s2, f)` right-folds a list.
  `s1` yields the starting point of the folding, `s2` is the folding strategy, and `f` is applied to each element just before each folding step.

For grading it is important that your strategy `stack-limit` does not take any parameters. You are free to use parameters in strategies that you call yourself.
{: .notice .notice-info}
