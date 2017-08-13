---
layout: page
title: "Lab 10: Complete Code Generation"
excerpt: "Lab 10: Complete Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

This lab is under construction. Proceed at own risk.
{: .notice .notice-warning}

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

The deadline for submissions is January 18th, 23:59.
{: .notice .notice-warning}

### Grading

Furthermore, you can earn up to 75 points for your code generator:

* lab 9 functionality (20 points)
* transformation (55 points)
    * types (15 points)
    * fields (10 points)
    * methods (15 points)
    * variables (10 points)
    * assignments (15 points)

In this assignment, we also give you the chance to earn up to 10 bonus points.
This works like a challenge, but it gives you extra points on top of the total points of your assignment.

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

To make code generation easier, we have made some information available during
desugaring and analysis:

- We desugared `Param(t,n)` to `Bind(i, Param(t,n))` and `Var(t,n)` to `Bind(i, Var(t,n))`.
- The occurrence of a parameter/variable declaration has a property `index`, where parameters have index `0`, ..., `n` and variables have index `n+1`, ..., `m`.

See the [interaction with analysis](lab9.html#interaction-with-analysis) section from last week for instructions on how to retrieve these properties.

1. Extend your rule for `method-to-jbc`, which handles method declarations.
   Support parameters by generating variable declarations, which map variable numbers in generated Java bytecode to variable names in the original MiniJava program.
   Do the same for local variables.
   To get the variable number associated with a parameter or local variable, get the `index` property.

2. Extend your rule for `exp-to-jbc`, which handles method calls.
   Support calls with arguments by calling `exp-to-jbc` recursively to translate argument expressions.

3. Provide a rule for `exp-to-jbc`, which translates variable access expressions from MiniJava into sequences of Java bytecode instructions.

4. Provide a rule for `stmt-to-jbc`, which translates assignments to variables from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

5. Provide a rule for `stmt-to-jbc`, which translates array assignments to variables from MiniJava into sequences of Java bytecode instructions.
   This rule should call `exp-to-jbc` to translate expressions to Java bytecode sequences.

### Bonus

#### Generate Precise Ranges for Local Variables

A precise range of a local variable covers only the parts in the code where the variable is defined and used:

* There is only one continuous range for each variable (in contrast, variable liveness as discussed in the lecture can be fragmented).
* The range should cover at least all instructions between the first and last load or store (whatever comes first/last) of a local variable.
* Loops might extend the range, since they might require a variable to survive.

You should extend your code generator to generate precise ranges.
Similar to the stack limit challenge from last week, the analysis should be performed on the MiniJava code, not on the Java bytecode.
