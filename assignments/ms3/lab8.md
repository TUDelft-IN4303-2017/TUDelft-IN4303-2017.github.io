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

The deadline for submission is December 14th, 23:59.
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

The second argument to the `JBCHeader` constructor must be `JBCSource("")` exactly, for your solution to work.
Our code makes sure that the source is set to the correct path.
{: .notice .notice-warning}

2. Provide a rule for `class-to-jbc`, which translates a _main class from MiniJava_ into a _Jasmin class file_.
This rule should call `stmt-to-jbc` to translate the statement inside the main method.
Make sure that the class and method are `public`, and that the class has a standard initializer.

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


### Testing and Debugging

Test your compiler by running the *Spoofax -> Generation -> Generate Java (.class files) and run* builder on a MiniJava program, for example the `lab08.mjv` file in `minijava.example`.
If everything goes right, this produces a `txt` file containing the result (i.e., values printed to stdout) of running your MiniJava program.
Internally, this will:

1. Execute `program-to-jbc` on the MiniJava program, to turn it into a Jasmin program.
2. Execute the Jasmin compiler on that Jasmin program, to turn it into a Java class file.
3. Load class files into the current JVM.
4. Execute the main method of the main class.

Any of these steps can fail.
Therefore, we list the most common problems and ways to fix them.

#### Step 1: Stratego failure

In step 1, the most common error is a Stratego failure in your MiniJava to Jasmin compiler.
A failure in Stratego occurs if matching a term fails.
Sometimes the failure of a match is intentional, and alternatives are tried.
However, sometimes there are no alternatives, the failure unwinds to the top of the Stratego program, and the entire program fails.

For example, take the following Stratego rules (this is example code that has nothing to do with MiniJava):

```
transform-decl: Property(name, type) -> ...
transform-decl: Method(name, type, args) -> ...
```

If we execute `<transform-decl> Method("m", StringTy(), [])`, Stratego will first try the first `transform-decl` rule, which fails because `Method` does not match `Property`.
It will then try the second rule, which matches and transforms the method.
This is an example of where failure is intentional, to try out all alternatives.
However, if we execute `<transform-decl> Entity("e", [])`, Stratego will try both rules, but fail since all alternatives are exhausted.
This shows up as an exception stack trace in the console of Eclipse, which looks like:

```
13:37 | ERROR | o.m.s.e.t.TransformJob         - Transformation failed for ...
org.metaborg.core.transform.TransformException: Invoking Stratego strategy transform-decl failed at term:
  Entity("e", [])
Stratego trace:
  transform_decl_0_0
  ...
  at org.metaborg.spoofax.core.transform.StrategoTransformer.transform(StrategoTransformer.java:144)
  ...
```

If the Eclipse console is not visible, open it with *Window -> Show View -> Console*.
{: .notice .notice-success}

Important information here is the strategy name that failed, the term it failed at, and the Stratego stack trace with rule names.
The Java stack trace part is usually not interesting and can be ignored.
We can use this information to figure out that we're calling `transform-decl` with a term it does not support, and fix it.

Sometimes, we need to do more debugging to diagnose and fix the problem.
Unfortunately, Stratego does not have a debugger, so debugging is done by printing terms at various places in your Stratego code.

You can use the `debug` strategy to print the current term to the console.
Furthermore, there are several useful shorthand versions of the debug strategy in the `libspoofax/stratego/debug` file, which is imported into `codegen/classes.str`.
You can view this library file by Ctrl/Cmd clicking the import, or by having a look at the [online version](https://github.com/metaborg/spoofax/blob/in4303/meta.lib.spoofax/trans/libspoofax/stratego/debug.str).
It contains the following three debugging strategies:

* `debug(|t)` and shorthands (`debug0`-`debug9`, `debuga`-`debugz`) that prefix the message with 0-9 and a-z. For example, `<debug0> SomeTerm()` prints `0: SomeTerm()`.
* `ppdebug(|t)` and shorthands that pretty-print the term before printing, splitting the term up into multiple lines.
* `ppdebugna(|t)` and shorthands that pretty-print and remove annotations (`term{annotations}`) from terms, to provide more readable terms when the annotations are not needed.

#### Step 2: Jasmin compiler failure

In step 2, the Jasmin to Java Class file compiler can fail if the Jasmin to Java class file compiler gets an incorrect Jasmin AST.
This can happen when your MiniJava to Jasmin compiler produces a (slightly) wrong Jasmin AST.
A common mistake is the nesting of lists (i.e., `[[..]]` somewhere in the AST), which is never allowed.
These kind of errors also show up as an exception in the Eclipse console, but have a slightly different stack trace:

```
13:37 | ERROR | o.m.s.e.t.TransformJob         - Transformation failed for ...
org.metaborg.core.transform.TransformException: Invoking Stratego strategy run-jc failed at term:
  ..
Stratego trace:
  run_jc_0_0
  with_1_1 <==
  map_1_0
  jasmin_generate_0_1
  call_in_language_0_2
  at org.metaborg.spoofax.core.transform.StrategoTransformer.transform(StrategoTransformer.java:144)
  ...
```

The important part here is the `call_in_language_0_2` part of the stack trace, which indicates that that it failed when calling the Jasmin compiler.

To fix these kind of errors, you must inspect the Jasmin AST that you generate, and make sure the AST is valid.
You can do this by using the *Spoofax -> Generation -> Generate Jasmin (.j.aterm files)* builder.
This builder generates a list of Jasmin terms for each class in the MiniJava program.
You can also use the `ppdebug` strategy to print the AST that comes out of `program-to-jbc`.

Optionally, if you really cannot find the error, you can compare the AST that your compiler generates, against the AST from a parsed Jasmin program.
First write the Jasmin program you are trying to compile to in a Jasmin file.
That is, create a file such as `debug.j` in `jasmin.example`, and write a Jasmin file that exactly matches what your MiniJava to Jasmin compiler would generate, but in concrete Jasmin syntax.
Then, on the Jasmin file you created, use the *Spoofax -> Syntax -> Show abstract syntax (for selection)* builder to show the AST for that Jasmin file.
Now, compare the Jasmin AST that your compiler generates, with the AST from the Jasmin file, find the differences, and fix your compiler.

#### Step 3: Class loading failure

In step 3, loading class files may fail if one or more class files do not pass verification.
The verifier checks for many common mistakes, such as the correctness of the maximum stack size and local variable size, and if the right type of operands are on the operand stack.
These kind of errors are shown in an Eclipse *Problem Occurred* popup.
Press the *Details* button on this popup to see the actual error.
If for some reason this popup does not show up, you can also see the error in the Eclipse *Error Log*, which you can open with *Window -> Show View -> Error Log* if it is not visible.
For example, the following error is produced when the wrong type of operands are on the operand stack:

```
An internal error occurred during: "Transforming resources".
(class: Arrays, method: main signature: ([Ljava/lang/String;)V) Expecting to find object/array on stack
```

To fix these kind of errors, inspect the bytecode that you generate with the *Spoofax -> Generation -> Generate Jasmin (.j files)* builder.
This builder generates Jasmin files for all classes in your MiniJava program, into a directory with the name of the MiniJava file.
For example, `test.mjv` with class `Main` generates `test/Main.j`.
It does not open the Jasmin files for you, so you have to open them yourself.

See the documentation on [verification of Java class files](http://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.10) for more information on verification.

#### Step 4: Execution error

In step 4, executing the bytecode of the class file can fail for a number of reasons.
For example, accessing an array outside of its bounds causes an `ArrayIndexOutOfBoundsException`.
These errors show up as exceptions in the produced `txt` file.
Note that these kind of errors do not necessarily have to be errors in your compiler, they can be a programming error in the MiniJava program.
For example, the following section of a MiniJava program causes an `ArrayIndexOutOfBoundsException` intentionally:

```
int[] a;
int b;
a = new int[2];
b = a[10];
```

To fix these kind of errors, first inspect the MiniJava program to see if the error is intentional.
If not, use the *Spoofax -> Generation -> Generate Jasmin (.j files)* builder to inspect the bytecode (see previous subsection for details).

## Challenge

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation or higher programming skills.
{: .notice .notice-success}

The JVM provides different Java bytecode instructions to load integer constants, which differ in memory consumption both in the constant pool and in the actual bytecode.
As explained in the lecture, generated code can be optimised after code generation.
This keeps code generation and optimisation separated, and allows reuse of optimisations in different compiler backends.

Implement an optimisation strategy `optimize-jbc`, which optimises a sequence of Java bytecode instructions by replacing instructions to load integer constants with more efficient variants.
Integrate this strategy into your code generation by applying it in `class-to-jbc` to the sequence of generated Java bytecode instructions.
