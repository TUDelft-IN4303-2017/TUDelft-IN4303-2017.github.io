---
layout: page
title: "Lab 9: More Code Generation"
excerpt: "Lab 9: More Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

This lab is under construction. Proceed at own risk.
{: .notice .notice-warning}

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

You need to submit your MiniJava project with a pull request against branch `assignment9` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

If you participate in the challenge, please let us know in the WebLab assignment [Challenges (overlays)](https://weblab.tudelft.nl/in4303/2016-2017/assignment/9085/view).

The deadline for submissions is December 22nd 2017, 23:59. The extended deadline is January 12th 2018, 23:59, for a penalty of 1 point.
{: .notice .notice-warning}

### Grading

You can earn up to 75 points for your code generator:

* transformation (55 points)
    * classes (8 points)
    * methods (7 points)
    * statements (13 points)
        * block statements (2 points)
        * `if` statements (5 points)
        * `while` loops (6 points)
    * expressions (22 points)
        * logic constants (1 point)
        * unary expressions (5 points)
        * binary expressions  (14 points)
        * object and array creation (1 point)
        * `this` expressions (1 point)
        * method calls (5 points)
    * inheritance (5 points)
* challenges (20 points)
    * overlays (5 points)
    * stack limits (15 points)

### Early Feedback

We provide early feedback only for the _tranformation_ and the _stack limits_ challenge (the _overlays_ are graded manually). You have 3 early feedback attempts.

## Detailed Instructions

### Git Repository

You continue with your work from the previous assignment.
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment9` branch from your previous work.

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
   You can apply `newname` from Stratego's standard library ([docs](http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/html/term/string.html#area-in-file("term/string.str",area(50,2,50,48,1642,46)))) to a string, to obtain a fresh name starting with the given string.

4. Provide rules for `exp-to-jbc`, which translate unary and binary expressions from MiniJava into sequences of Java bytecode instructions.
   These rules should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.
   Furthermore, they should call a strategy `op-to-jbc` to translate unary and binary operators to Java bytecode instructions.
   The only exception is the `&&` operator, which needs to be treated differently, due to its lazy evaluation semantics.

<!-- This is no longer possible, right? -->
<!-- You can test each rule by selecting a code fragment in the MiniJava editor and running your code generation builder. -->

### Code Generation for Methods

Now you need to define a strategy `method-to-jbc` to handle methods without local variables.

1. Provide a rule for `method-to-jbc`, which translates a method without parameters and local variables from MiniJava into a Jasmin method. This rule should call `stmt-to-jbc` to translate the statements of the method to a Java bytecode sequence.

2. Provide a rule for `exp-to-jbc`, which translates `this` expressions from MiniJava into sequences of Java bytecode instructions.

3. Provide a rule for `exp-to-jbc`, which translates method calls without arguments from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.

   For this rule, you need to know the name of the class containing the method and the type of the method.
   This information is available from the analysis.
   Read along for instructions on how to interact with the analysis.

4. Extend the rule for `class-to-jbc`, which handles empty classes, in order to include code generation for methods.

5. Provide a rule for `exp-to-jbc`, which translates object creation expressions into sequences of Java bytecode instructions.

For grading, if you decide not to participate in the challenge, use a fixed stack size of 99 for both static and non-static methods.
{: .notice .notice-info}

### Interaction with Analysis

To make code generation easier, we have made some information available during
desugaring and analysis:

- The occurrence of a method declaration has a property `cname` with the name of its surrounding class (same for the occurrence of a field declaration).
- The occurrence of a variable/parameter declaration has a property `origin` with the value `Local()`.
- The occurrence of a field declaration has a property `origin` with the value `Field()`.
- The occurrence of a method declaration has the type `MethodType(rty, ptys)`.

The following strategies can be used to query the analysis result:

  * `nabl2-mk-occurrence(|Ns)` applied to a name `n` yields an occurrence `Ns{n}`.
  * `nabl2-get-ast-analysis` applied to any AST term yields the analysis result, which can be used with the following strategies to extract information:
    * `nabl2-get-resolved-name(|a)` applied to a reference occurrence yields a tuple `(occurrence, path)`. The term parameter `a` is the analysis result.
    * `nabl2-get-property(|a, prop)` applied to an occurrence yields the value of property `prop`. The term parameter `a` is the analysis result.
    * `nabl2-get-type(|a)` applied to an occurrence yields the type of the occurrence. The term parameter `a` is the analysis result.
  * `nabl2-get-occurrence-name` applied to an occurrence yields the name of the occurrence.
  * `nabl2-get-ast-type` applied to an AST node with a type (i.e., an expression), yields its type. The resulting type can contain occurrences if it is a class type.

The following example shows how to use these strategies. It matches a `VarRef(n)`,
resolves the reference to a declaration, and computes its `kind`, `type`, and `name`.

```
VarRef(n) -> <fail>
where
  a       := <nabl2-get-ast-analysis> n
; ref-occ := <nabl2-mk-occurrence(|"Var")> n
; dec-occ := <nabl2-get-resolved-name(|a); Fst> ref-occ
; kind    := <nabl2-get-property(|a, "origin")> dec-occ
; type    := <nabl2-get-type(|a)> dec-occ
; name    := <nabl2-get-occurrence-name> dec-occ
```

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

### Precise Stack Limit Directives

A stack limit directive tells the Java Virtual Machine the maximum number of elements at the operand stack.
To give a precise limit, you need to write a strategy `stack-limit` that maps MiniJava expressions and statements to a stack limit.

Do not do this analysis on the Java bytecode level.
{: .notice .notice-danger}

For expressions, you need to consider the number of elements already on the stack before the expression is evaluated. You can either pass this number as a term parameter or adapt intermediate results accordingly.
For statements, you do not need this information since statements should not leave any elements on the stack.

The following strategies might be useful:

* `inc`  rewrites an integer number to its successor.
* `max` rewrites a pair of integer numbers to the maximum of both numbers.
* `addi` rewrites a pair of integer numbers to the sum of both numbers.
* `foldr(s1, s2, f)` right-folds a list.
  `s1` yields the starting point of the folding, `s2` is the folding strategy, and `f` is applied to each element just before each folding step.

You should test your stack limit implementation by writing SPT tests as follows:

```spt
test stack limit [[
  class Main {
    public static void main(String[] args) {
      System.out.println([[1]]);
    }
  }
]] run stack-limit on #1 to ??
```

For grading it is important that your strategy `stack-limit` does not take any parameters. You are free to use parameters in strategies that you call yourself.
{: .notice .notice-warning}

For grading it is important that your strategy `stack-limit` operates on *expressions*, *statements*, and *lists of statements*.
{: .notice .notice-warning}
