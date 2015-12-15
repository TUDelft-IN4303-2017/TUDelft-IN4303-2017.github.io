---
layout: page
title: "Lab 10: Simple Code Generation"
excerpt: "Lab 10: Simple Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

In this lab, you develop a simple code generator, that generates Java bytecode from simple MiniJava programs, which only print integer constants in their main methods.

## Overview

### Objectives

1. Write a Jasmin program which prints `42`.
2. Implement a code generator that transforms MiniJava programs into Java bytecode.
The code generator should include
    1. A transformation from MiniJava ASTs to Jasmin ASTs which handles MiniJava programs
        * with only a main class
        * with a single print statement
        * with an integer constant expression.
    2. A menu action which invokes the transformation and pretty-prints the Jasmin AST to concrete syntax.
    3. A menu action which generates a Java class file instead of a Jasmin file.
    4. A menu action which runs the program (challenge).

### Submission

You need to submit your MiniJava project with a pull request against branch `assignment10` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

The deadline for submission is December 6th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 20 points for your Jasmin program and up to 70 points for your code generator:

* transformation (50 points)
    * program (2 points)
    * main class (18 points)
    * main method (13 points)
    * print statement (17 points)
* challenge (20 points)

You can earn up to 10 points for the quality of your code. We focus on readability in general, meaningful variable names and the consistent use of Stratego paradigms. We pay special attention on the treatment of lists in your code. We will consider the fact that Stratego is new to you.

### Early Feedback

This assignment is graded manually. Thus, we do not provide early feedback for this submission.

## Detailed Instructions

### Preliminaries

#### Updating Jasmin

A new version of Jasmin is required for this lab. To update Jasmin in Eclipse:

1. go to **Help -> Install New Software...**
2. in the **Work with** field, enter `http://download.spoofax.org/update/jasmin`
3. check **JasminXT**
4. press **Next** twice
5. agree to the license agreements and press **Finish** to download and install updates
6. when a security warning pops up, press **OK** to dismiss the warning
7. when Eclipse has installed all updates, it will ask for a restart, press **Yes** to restart Eclipse

If installation fails with errors indicating that "updates are not permitted", it means that your Eclipse installation is in a location that requires admin rights to write files. This is the case on Windows if you've copied Eclipse to Program Files. Move it to a directory where you have write access, such as the desktop, to solve the problem.
{: .notice .notice-warning}

#### GitHub Repository

We provide you with a template for this assignment in the `assignment10` branch.
See the [Git documentation](/documentation/git.html#template) on how to check out this branch.

#### Initial Editor Project

The template provides you with a fresh MiniJava editor project, which covers the syntax and analysis of MiniJava.
For grading purposes, you are required to use this project as a starting point for milestone 3.
You should import and build the new project, following these steps:

1. Import the projects into your workspace:
    1. right-click into the Package Explorer
    2. select **Import...** from the context menu
    3. choose **General/Existing Projects into Workspace** from the list
    4. select the MiniJava and Jasmin-examples project
    5. press the **Finish** button
2. Build the project:
    1. select the project folder
    2. select **Build Project** from the **Project** menu
    3. the console will report success or failure

The MiniJava project contains the following implementations:

* MiniJava signatures in `reference/src-gen/signatures/MiniJava-sig`.
* MiniJava pretty-printer in `reference/src-gen/pp/MiniJava-pp`
* MiniJava syntactic completions in `reference/src-gen/completions/MiniJava-esv`.
* MiniJava desugaring signatures in `reference/desugar-signatures`.
* JasminXT signatures in `reference/src-gen/jasmin-signatures/JasminXT*-sig`

These implementations are already imported into the initial project.

The Jasmin-examples project contains several Jasmin examples. You can try to run them using the builders from the JVM menu.

### Write Jasmin Code

Consider the following simple MiniJava program:

```
class Simple {
    public static void main(String[] args) {
        System.out.println(42);
    }
}
```

Write a Jasmin program `simple.j`, which you expect to be the result of a MiniJava-to-Jasmin compiler.
Generate a Java class file from it and run it.
Improve your program until it runs without errors.

### Implement a Code Generation Strategy

Code generation should be a service of your MiniJava editor.
The initial project contains several builders in the *Generate* menu.
The *Generate Java bytecode* builder calls the `generate-jbc` strategy which is defined in `trans/codegen/build.str`.
The implementation relies on a strategy `program-to-jbc`, which transforms MiniJava programs into Java bytecode.
We provide an implementation of `program-to-jbc` that always fails in `trans/codegen/classes.str`.

You need to implement `program-to-jbc`.
You will do this stepwise over the remaining labs.
During this lab, you should implement it for programs that contain only a main class, which prints a single integer constant.
To understand Jasmin's abstract syntax, you can either
  study example ASTs generated by a Jasmin editor,
  study the grammar in the Jasmin project,
  or have a closer look into `reference/src-gen/jasmin-signatures/-`.

1. Provide a rule for `exp-to-jbc`, which translates an integer constant from MiniJava into a sequence of Java bytecode instructions, that loads this constant to the operand stack.
   Note that it is important to generate a sequence here, even if you only need a single instruction, because in general a MiniJava expression translates into a sequence of bytecode instructions.

2. Provide a rule for `stmt-to-jbc`, which translates a print statement from MiniJava into a sequence of Java bytecode instructions.
This rule should call `exp-to-jbc` to translate the expression inside the print statement to a Java bytecode sequence.

3. Provide a rule for `class-to-jbc`, which translates a main class from MiniJava into a Jasmin class file.
This rule should call `stmt-to-jbc` to translate the statement inside the main method to a Java bytecode sequence.

4. Provide a rule for `program-to-jbc`, which translates a MiniJava program into a list of Jasmin class files.
This rule should call `class-to-jbc` to translate the main class of the program into a Jasmin class file.

#### Testing

For testing purposes, you can define another menu entry which calls a strategy `to-jbc`, which dispatches to your different implementation rules:

    to-jbc = program-to-jbc + class-to-jbc + stmt-to-jbc + exp-to-jbc

This allows you to test your implementation by selecting a code fragment in the MiniJava editor and running your code generation builder.

#### Some Notes on Sequences

You can represent sequences as lists.
Sequences of bytecode instructions are never nested.
Thus, your generated sequences should also be flat lists.
This requires you to compose sequences from recursive calls with surrounding instructions.
In general, there are four different approaches to this in Stratego:

1. Generate nested lists and flatten these lists afterwards by applying `flatten-list` (not recommended).

2. Compose lists with a head and tails notation. For example, `seq` might be a sequence generated by a recursive call. You can precede this sequence with instructions `instr1`, ..., `instrn` by writing `[ instr1, ..., instrn | seq ]`.

3. Compose lists explicitly.
    * `<conc> (l1, l2)` concatenates two lists `l1`and `l2`.
    * `<concat> [l1, ..., ln]` concatenates lists `l1` ... `ln`.  

4. Use special list variables (recommended). Stratego provides special variable names for sequences. These names end in `*`, for example `instr*`. When using such variables in a list, Stratego will inline the list elements at that position instead of creating a nested list. For example,

        instr1* := [LDC(Int("1")), LDC(Int("2"))]; instr2* := [LDC(Int("0")), instr1*, LDC(Int("3"))]

    is equivalent to

        instr1* := [LDC(Int("1")), LDC(Int("2"))]; instr2* := [LDC(Int("0")), LDC(Int("1")), LDC(Int("2")), LDC(Int("3"))]

#### Generate Concrete Syntax

As you may have noticed, your implementation follows the code generation by transformation paradigm. The result of `to-jbc` is a Jasmin AST. The builder strategy `generate-jbc` generates concrete Jasmin syntax by calling `jasmin-pp` which is defined in `trans/jasmin.str`. Take a look at the code in `trans/codegen/build.str` to see what is going on.

### Generate Java Class Files

At this point, you can generate Jasmin class files from simple MiniJava programs. To run your programs in the Java Virtual Machine, you need to generate Java class files.
The builder *Generate Java class files* calls the builder strategy `generate-jc` that generates the Jasmin file and turns it into a Java class file.

To translate a Jasmin AST into a Java class file we use the strategy `jasmin-generate` defined in `trans/jasmin.str`. As you can see, this strategy requires the path to the source file in a Jasmin directive. The source directive is used by the JVM for debugging purposes and error messages. The Jasmin builder uses it to figure out where to store the class file. Thus, make sure your Jasmin AST has the form `JBCFile(JBCHeader(_, JBCSource(<double-quote> path), _, _, _, _, _, _, _, _), _, _)`. The `path` term is passed to `program-to-jbc` as a strategy argument for you to use.

### Notes on names in Jasmin

Even though in MiniJava some keywords are reserved, Jasmin has its own keywords. Some Jasmin implementations consider those as reserved, some do not. Typical examples are keywords such as `field` or `class`. In the Spoofax implementation, we do not reserve these, but other Jasmin implementations might do, particularly if they may not have the latest updates (Debian for example). See [StackOverflow](http://stackoverflow.com/questions/10371352/how-to-handle-field-named-as-keywords-in-jasmin) for more information about this.

### Debugging stratego code

Stratego is hard to debug, so here are some tips. Using `debug` is not part of the assignment, but it will make your life easier.

#### The `debug` strategy

The `debug` strategy prints the current term. There is also an overloaded version `debug(msg)`. This will print the term produced by applying `msg` followed by the current term. Note that this allows you to add some text while leaving the current term intact. For example, compare the following version of `generate-jbc` to the one provided in the initial project:

    generate-jbc:
        (selected, position, ast, path, project-path) -> None()
        with
          <debug(!"Path: ")> path
        ; dir := <remove-extension> path
        ; <debug(!"Dir: ")> dir
        ; <try(mkdir)> dir
        ; index-setup(|<language>, project-path)
        ; task-setup(|project-path)
        ; <program-to-jbc(|path); debug(!"program-to-jbc: "); map(write-jbc(|dir))> ast

The runtime library `lib/runtime/nabl/utils.str` contains several shorthand functions. The strategy `ppdebug0` produces more readable output by prettyprinting the current term before calling `debug(!"0")`. Moreover, the strategy `ppdebugna0` calls `ppdebug0` but strips the annotations first.

## Challenge

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation or higher programming skills.
{: .notice .notice-success}

The JVM provides different Java bytecode instructions to load integer constants, which differ in memory consumption both in the constant pool and in the actual bytecode. As explained in the lecture, generated code can be optimised after code generation. This keeps code generation and optimisation separated, and allows reuse of optimisations in different compiler backends.

Implement an optimisation strategy `optimize-jbc`, which optimises a sequence of Java bytecode instructions by replacing instructions to load integer constants with more efficient variants. Integrate this strategy into your code generation by applying it in `class-to-jbc` to the sequence of generated Java bytecode instructions.
