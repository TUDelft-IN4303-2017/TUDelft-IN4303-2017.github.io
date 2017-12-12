---
layout: page
title: "Lab 9: More Code Generation"
excerpt: "Lab 9: More Code Generation"
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
    * method calls _without arguments_,
    * block statements,
    * `if` statements,
    * `while` statements,
    * methods _without parameters and local variables_,
    * classes _without fields_.
2. Extend your code generator to handle these constructs.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment9` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submissions is December 22nd 2017, 23:59. The extended deadline is January 12th 2018, 23:59, for a penalty of 1 point.
{: .notice .notice-warning}

### Grading

You can earn up to 65 points for your code generator:

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
* challenge (10 points)
    * stack limits (10 points)

### Early Feedback

We provide early feedback for the _tranformation_ part and the _stack limits_ challenge. You have 3 early feedback attempts.

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

### Interaction with Name and Type Information

To ease code generation, we have made the following name and type information available to your compiler:

- The occurrence of a method declaration has a property `cname` with the name of its surrounding class (same for the occurrence of a field declaration).
- The occurrence of a method declaration has the type `MethodType(return-type, parameter-types)`.

The following strategies are useful to query the analysis result:

  * `nabl2-mk-occurrence(|Ns)` applied to a name `n` yields an occurrence `Ns{n}`.
  * `nabl2-get-ast-analysis` applied to any AST term yields the analysis result, which can be used with the following strategies to extract information:
    * `nabl2-get-resolved-name(|a)` applied to a reference occurrence yields a tuple `(occurrence, path)`. The term parameter `a` is the analysis result.
    * `nabl2-get-property(|a, prop)` applied to an occurrence yields the value of property `prop`. The term parameter `a` is the analysis result.
    * `nabl2-get-type(|a)` applied to an occurrence yields the type of the occurrence. The term parameter `a` is the analysis result.
  * `nabl2-get-occurrence-name` applied to an occurrence yields the name of the occurrence.

For more information about these strategies, Ctrl/Cmd-click the `nabl2/api` import in your Stratego file.

The following example shows how to use these strategies.
The first rule matches method declarations, gets the analysis result, creates an occurrence from the method declaration name, and retrieves the `cname` property of the method.
The second rule matches method calls, gets the analysis result, creates an occurrence from the method reference name, and resolves this to an occurrence of the method declaration which can be used in other query strategies.

```
method-to-jbc:
  Method(t, m, _, _, stms, e) -> ...
  with
    a       := <nabl2-get-ast-analysis>
  ; dec-occ := <nabl2-mk-occurrence(|"Method")> m
  ; cname   := <nabl2-get-property(|a, "cname")> dec-occ
    ...

exp-to-jbc:
  Call(e, m, _) -> ...
  with
    a            := <nabl2-get-ast-analysis>
  ; ref-occ      := <nabl2-mk-occurrence(|"Method")> m
  ; (dec-occ, _) := <nabl2-get-resolved-name(|a)> ref-occ
    ...
```

### Code Generation for Methods

Now you need to define a strategy `method-to-jbc` to handle methods without local variables.

1. Provide a rule for `method-to-jbc`, which translates a method without parameters and local variables from MiniJava into a Jasmin method. This rule should call `stmt-to-jbc` to translate the statements of the method to a Java bytecode sequence.

2. Provide a rule for `exp-to-jbc`, which translates `this` expressions from MiniJava into sequences of Java bytecode instructions.

3. Provide a rule for `exp-to-jbc`, which translates method calls without arguments from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` recursively to translate subexpressions to Java bytecode sequences.

   For this rule, you need to know the name of the class containing the method and the type of the method.
   Read the instructions above on how to use name and type information.

4. Extend the rule for `class-to-jbc`, which handles classes, in order to include code generation for methods.

5. Provide a rule for `exp-to-jbc`, which translates object creation expressions into sequences of Java bytecode instructions.

For grading, if you decide not to participate in the challenge, use a fixed stack size of 99 for both static and non-static methods.
{: .notice .notice-info}

### Testing and Debugging

Testing and debugging your compiler is the same as the previous lab.
Refer to the section on [Testing and Debugging from assignment 8](lab8.html#testing-and-debugging).

## Challenges

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation and programming skills.
{: .notice .notice-success}

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
