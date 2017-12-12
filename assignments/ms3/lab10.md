---
layout: page
title: "Lab 10: Complete Code Generation"
excerpt: "Lab 10: Complete Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

In this lab, you complete your code generator to handle arbitrary MiniJava programs.

## Overview

### Objectives

1. Write MiniJava programs and corresponding Jasmin programs which cover the following constructs:

    * classes with fields,
    * methods with parameters and local variables,
    * variable and field access,
    * both kinds of assignments,
    * method calls with arguments.

2. Extend your code generator to handle these constructs.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment10` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submissions is January 19th 2018, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 75 points for your code generator:

* lab 9 functionality keeps working (20 points)
* transformation (55 points)
    * types (15 points)
    * fields (10 points)
    * methods (15 points)
    * variables (10 points)
    * assignments (15 points)

### Early Feedback

We provide early feedback only for the _lab 9 functionality_ and _tranformation_. You have 3 early feedback attempts.

## Detailed Instructions

### Git Repository

You continue with your work from the previous assignment.
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment10` branch from your previous work.

### Write Even More Jasmin Code

Before you complete your code generator, you should come up with small example MiniJava programs, which cover the following constructs:

* fields,
* parameters and local variables,
* variable and field access,
* assignments, and
* method calls with arguments.

Write corresponding Jasmin programs, which you expect to be the result of a MiniJava-to-Jasmin compiler.
Generate Java class files from them and run them.
Improve your programs until they run without errors.

### Interaction with Analysis

To ease code generation of parameters and local variables, we have desugared MiniJava programs and made additional name and type information available:

- We desugared `Param(t,n)` to `Bind(i, Param(t,n))` and `Var(t,n)` to `Bind(i, Var(t,n))`.
- The occurrence of a parameter/variable declaration has a property `index`, where parameters have index `0`, ..., `n` and variables have index `n+1`, ..., `m`.
- The occurrence of a variable declaration has a property `origin` with the value `Local()`.
- The occurrence of a parameter declaration has a property `origin` with the value `Param()`.
- The occurrence of a field declaration has a property `origin` with the value `Field()`.

The information from the previous assignment is also still available:

- The occurrence of a method declaration has a property `cname` with the name of its surrounding class (same for the occurrence of a field declaration).
- The occurrence of a method declaration has the type `MethodType(return-type, parameter-types)`.

The following strategies are useful to query the analysis result:

  * `nabl2-mk-occurrence(|Ns)` applied to a name `n` yields an occurrence `Ns{n}`.
  * `nabl2-get-ast-analysis` applied to any AST term yields the analysis result, which can be used with the following strategies to extract information:
    * `nabl2-get-resolved-name(|a)` applied to a reference occurrence yields a tuple `(occurrence, path)`. The term parameter `a` is the analysis result.
    * `nabl2-get-property(|a, prop)` applied to an occurrence yields the value of property `prop`. The term parameter `a` is the analysis result.
    * `nabl2-get-type(|a)` applied to an occurrence yields the type of the occurrence. The term parameter `a` is the analysis result.
  * `nabl2-get-occurrence-name` applied to an occurrence yields the name of the occurrence.
  * `nabl2-get-ast-type` applied to an AST node with a type (i.e., an expression), yields its type. The resulting type can contain occurrences if it is a class type.

For more information about these strategies, Ctrl/Cmd-click the `nabl2/api` import in your Stratego file.

The following example shows how to use these strategies.
It matches a variable reference, gets the analysis result, creates and resolves its occurrence, retrieves the `kind` property, and checks if kind is a parameter or local variable.

```
exp-to-jbc:
  VarRef(n) -> ...
  where
    a            := <nabl2-get-ast-analysis>
  ; ref-occ      := <nabl2-mk-occurrence(|"Var")> n
  ; (dec-occ, _) := <nabl2-get-resolved-name(|a)> ref-occ
  ; kind         := <nabl2-get-property(|a, "origin")> dec-occ
  ; <?Param() + ?Local()> kind
    ...
```

### Generate Code for Fields and Field Access

You now need to extend your code generator to handle field declarations and field access.

1. Provide rules for `type-to-jbc`, which translate MiniJava types into Jasmin types as used in field and method descriptors.

2. Provide a rule for `field-to-jbc`, which translates field declarations from MiniJava into Jasmin field declarations.

3. Extend your rules for `class-to-jbc` to handle field declarations.

4. Provide a rule for `exp-to-jbc`, which translates field access expressions from MiniJava into sequences of Java bytecode instructions. See the next section on how to figure out if a variable refers to field or a local variable.

5. Provide a rule for `stmt-to-jbc`, which translates assignments to fields from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

6. Provide a rule for `stmt-to-jbc`, which translates array assignments to fields from MiniJava into sequences of Java bytecode instructions.

You can test each rule by selecting a code fragment in the MiniJava editor and running your code generation builder.

### Generate Code for Local Variables and Variable Access

You now need to extend your code generator to handle method parameters and local variable declarations, method call arguments, variable references, and assignments.

1. Extend your rule for `method-to-jbc`, which handles method declarations.
   Support parameters by generating variable declarations, which map variable numbers in generated Java bytecode to variable names in the original MiniJava program.
   Do the same for local variables.
   To get the variable number associated with a parameter or local variable, get the `index` property.

2. Extend your rule for `exp-to-jbc`, which handles method calls.
   Support calls with arguments by calling `exp-to-jbc` recursively to translate argument expressions.

3. Provide a rule for `exp-to-jbc`, which translates variable reference expressions from MiniJava into sequences of Java bytecode instructions.

4. Provide a rule for `stmt-to-jbc`, which translates assignments to variables from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

5. Provide a rule for `stmt-to-jbc`, which translates array assignments to variables from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

### Testing and Debugging

Testing and debugging your compiler is the same as previous labs.
Refer to the section on [Testing and Debugging from assignment 8](lab8.html#testing-and-debugging).
