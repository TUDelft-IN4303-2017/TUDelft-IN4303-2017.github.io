---
layout: page
title: "Lab 8: Simple Code Generation"
excerpt: "Lab 8: Simple Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

In this lab you develop a simple code generator that generates Java bytecode for simple MiniJava programs.
After finishing this lab your compiler will compile MiniJava programs with a main method that contains a print statement that prints an integer.

## Overview

### Objectives

1. Write a Jasmin program which prints `42`.
2. Implement a code generator that transforms MiniJava programs into Java bytecode.
The code generator should include a transformation from MiniJava ASTs to Jasmin ASTs which handles MiniJava programs
  * with only a main class
  * with a single print statement
  * with an integer constant expression.

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment8` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

Also, you need to **paste the contents** of `simple.j` in the WebLab assignment [Jasmin Code](https://weblab.tudelft.nl/in4303/2016-2017/assignment/9076/view).
If you participate in the challenge, you need to paste your optimisation in the WebLab assignment [Java Bytecode Optimisation](https://weblab.tudelft.nl/in4303/2016-2017/assignment/9079/view).

The deadline for submission is December 7th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 40 points for your _Jasmin program_, up to 40 points for your _code generator_, and up to 20 points for the _challege_.

### Early Feedback

We provide early feedback only for the _code generator_. You have 3 early feedback attempts.

## Detailed Instructions

### Preliminaries

#### Updating Spoofax and Jasmin

New version of Spoofax and Jasmin are required for this lab.
See the [Spoofax documentation](/documentation/spoofax.html#updating) on how to update Spoofax, and the [Jasmin documentation](/documentation/jasmin.html#updating) on how to update Jasmin.

You can also [download a fresh Eclipse](/documentation/spoofax.html#downloading), which includes an up to date version of both Spoofax and Jasmin.
You should reuse your old workspace with the new Eclipse to retain your settings.

#### GitHub Repository

We provide you with a template for this assignment in the `assignment8` branch.
See the [Git documentation](/documentation/git.html#template) on how to check out this branch.

#### Initial Editor Project

The template provides you with a fresh MiniJava editor project, which covers the syntax and analysis of MiniJava.
For grading purposes, you are required to use this project as a starting point for milestone 3.
You should import and build the new project, following these steps:

1. Import the projects into your workspace:
    1. right-click into the Package Explorer
    2. select **Import...** from the context menu
    3. choose **Maven/Existing Maven Projects** from the list
    4. select the _minijava_, _minijava.example_, and _jasmin.examples_ projects
    5. press the **Finish** button
2. Build the project:
    1. select the project folder
    2. select **Build Project** from the **Project** menu
    3. the console will report success or failure

The `reference` directory of the `minijava` project contains MiniJava's signatures, pretty-printer, syntactic completions, desugaring signatures, and analysis implementation, as well as Jasmin signatures.
These implementations are already imported into the initial project.

The `jasmin.examples` project contains several Jasmin examples.
You can try to run them by opening a `.j` file and using the builders from the Spoofax menu.

The `minijava.example` project contains example programs for this and following labs.
Put your own example programs in this project.


### Write Jasmin Code

Consider the following simple MiniJava program:

```java
class Simple {
    public static void main(String[] args) {
        System.out.println(42);
    }
}
```

Write a Jasmin program `simple.j` which you expect to be the result of a MiniJava-to-Jasmin compiler.
Generate a Java class file from it and run it.
Improve your program until it runs without errors.
The contents if your Jasmin program should be pasted into WebLab, see the submission info for more details.


### Implement a Code Generation Strategy

Code generation should be a service of your MiniJava editor.
The initial project contains several builders in the *Spoofax -> Generation* menu.
The *Generate Jasmin (.j files)* builder calls the `generate-jbc` strategy which is defined in `trans/codegen/build.str`.
The implementation relies on a strategy `program-to-jbc`, which transforms MiniJava programs into Java bytecode.
We provide an implementation of `program-to-jbc` that always fails in `trans/codegen/classes.str`.

You need to implement `program-to-jbc`.
You will do this stepwise over the remaining labs.
During this lab, you should implement it for programs that contain only _a main class_, which _prints a single integer constant_.

To understand Jasmin's abstract syntax, you can either
  study example ASTs generated by a Jasmin editor,
  study the [grammar](https://github.com/MetaBorgCube/spoofax-jasmin/tree/master/jasmin/syntax) in the Jasmin project,
  or have a closer look into Jasmin's signatures in `reference/src-gen/jasmin-signatures/` of your MiniJava project.

1. Provide a rule for `program-to-jbc`, which translates a _MiniJava program_ into a _list of Jasmin class files_.
This rule should call `class-to-jbc` to translate the main class of the program.
While in this lab your compiler should only support a single main class, in subsequent labs you must handle MiniJava programs with multiple classes.
Therefore, a program is translated into a **list** of Jasmin class files.

2. Provide a rule for `class-to-jbc`, which translates a _main class from MiniJava_ into a _Jasmin class file_.
This rule should call `stmt-to-jbc` to translate the statement inside the main method.

3. Provide a rule for `stmt-to-jbc`, which translates a _print statement from MiniJava_ into a _list of Java bytecode instructions_.
This rule should call `exp-to-jbc` to translate the expression inside the print statement.

4. Provide a rule for `exp-to-jbc`, which translates an _integer constant from MiniJava_ into a _list of Java bytecode instructions_, that loads this constant to the operand stack.
Note that it is important to generate a **list** here, even if you only need a single instruction, because in general a MiniJava expression translates into a sequence of bytecode instructions.

#### Some Notes on Lists

Lists of bytecode instructions are never nested.
Thus, your generated sequences should also be flat lists.
This requires you to compose lists from recursive calls with surrounding instructions.
In general, there are three different approaches to this in Stratego:

1. Generate nested lists and flatten these lists afterwards by applying `flatten-list` (not recommended).

2. Compose lists with a head and tails notation. For example, `lst` might be a sequence generated by a recursive call. You can precede this sequence with instructions `instr1`, ..., `instrn` by writing `[ instr1, ..., instrn | lst ]`.

3. Compose lists explicitly with `concat`. For example `<concat> [l1, ..., ln]` concatenates lists `l1` ... `ln`.


### Debugging Stratego code

Stratego does not have a debugger, so debugging is done by printing terms at various places in your Stratego code.

You can use the `debug` strategy to print the current term.
There is also an overloaded version `debug(|t)` that prefixes the output by the result of `t`.
For example, `<debug(|"1: ")> SomeTerm()` prints `1: SomeTerm()`
This allows you to print some text without changing the current term.

There are several useful shorthand versions of the debug strategy in the `libspoofax/stratego/debug` file, which is imported into `codegen/classes.str`.
You can view this library file by Ctrl/Cmd clicking the import, or by having a look at the [online version](https://github.com/metaborg/spoofax/blob/in4303/meta.lib.spoofax/trans/libspoofax/stratego/debug.str).
It contains shorthands for `debug(|t)` that prefix the message with 0-9 and a-z.
Furthermore, the `ppdebug(|t)` strategy and shorthands pretty-print the term before printing, so that the term is not on one very long line.
Finally, `ppdebugna(|t)` and shorthands pretty-print and strip annotations (`term{annotations}`) from terms, to provide more readable terms when the annotations are not needed.


## Challenge

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation or higher programming skills.
{: .notice .notice-success}

The JVM provides different Java bytecode instructions to load integer constants, which differ in memory consumption both in the constant pool and in the actual bytecode.
As explained in the lecture, generated code can be optimised after code generation.
This keeps code generation and optimisation separated, and allows reuse of optimisations in different compiler backends.

Implement an optimisation strategy `optimize-jbc`, which optimises a sequence of Java bytecode instructions by replacing instructions to load integer constants with more efficient variants.
Integrate this strategy into your code generation by applying it in `class-to-jbc` to the sequence of generated Java bytecode instructions.
