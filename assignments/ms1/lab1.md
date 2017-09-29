---
layout: page
title: "Lab 1: Testing Syntax Analysis"
excerpt: "Lab 1: Testing Syntax Analysis"
tags: ["assignment"]
context: assign
subcontext: ms1
---

{% include _toc.html %}

In this lab, you develop a test suite for syntax analysis.
The test suite consists of positive and negative test cases.

## Overview

### Objectives

Develop a test suite for syntax analysis.
The test suite should provide

1.  Syntactically valid and invalid test cases for sorts
    * `Program`,
    * `MainClass`,
    * `ClassDecl`,
    * `VarDecl`,
    * `MethodDecl`,
    * `Type`,
    * `Statement`,
    * `Exp`,
    * `ID` and
    * `INT`.

    Test cases for `FieldDecl` and `ParamDecl` should be covered in the tests for `ClassDecl` and `MethodDecl`, respectively.

    For grading, it is required to comply with these sort names literally.
    {: .notice .notice-warning}

2. Disambiguation tests for associativity and precedence in expressions.
3. Test cases for mandatory and optional whitespace.

### Submission

You need to submit your test project with a pull request against branch `assignment1` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.
We expect to find your Spoofax project `minijava` and your test project `minijava.test.syntax` next to each other in the root of the repository.

The deadline for submission is 20th September 2017, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 100 points for the effectiveness of your test cases.
Therefore, we run your test cases against 196 correct and erroneous syntax definitions of MiniJava.
There are two kinds of erroneous syntax definitions:

- Syntax definitions, which are too *liberal*, accept invalid language constructs.
- Syntax definitions, which are too *restrictive*, do not accept valid language constructs.

You earn points,
  when your test cases reveal certain kinds of errors in erroneous syntax definitions
  while passing correct syntax definitions.
The total number of points depends only on how many kinds of errors you reveal,
  not on the number of test cases that reveal these errors.

### Early Feedback

We provide early feedback for the validity and effectiveness of your test cases.

- A test case is *valid*, if it passes on correct syntax definitions.
- A test is *effective*, if it reveals an erroneous syntax definition.

The feedback lists for different language constructs how well you discover liberal and restrictive syntax definitions.
It also includes a summary on how many valid tests you provide, how many erroneous syntax definitions you reveal with these tests, and how many points you earn by detecting them.
You have 3 early feedback attempts.

## Detailed Instructions

### Preliminaries

#### Git Repository

We provide you with a template for this assignment in the `assignment1` branch.
See the [Git documentation](/documentation/git.html#template) on how to check out this branch.

The template contains 3 Maven projects:

* `minijava`: the initial Spoofax project for MiniJava,
* `minijava.example`: an empty project for your example MiniJava programs,
* `minijava.test.syntax`: an empty project for your MiniJava syntax tests.

#### Importing projects into Eclipse

The projects from the template have to be imported into Eclipse:

1. Start Eclipse.
2. Select your workspace.
3. Import the projects into your workspace:
    1. Right-click into the Package Explorer.
    2. Select Import... from the context menu.
    3. Choose Maven -> Existing Maven Projects from the list.
    4. Press next.
    5. Select the root of your Git repository.
    6. Select all projects.
    7. Make sure that Copy projects into workspace is **off**.
    8. Press finish

If you get an error about `Resource '/.org.eclipse.jdt.core.external.folders' already exists`, you can safely ignore that.

### Anatomy of a Test Suite

In Spoofax, a test suite consists of one or more `.spt` files.
Consider the following example file:

    module example

    language minijava
    start symbol INT

    test double digit   [[42]]  parse succeeds
    test floating point [[4.2]] parse fails

The first lines specify
  the name of the test `module`,
  the `language` under test,
  and the `start symbol` used for a test.
In this `example` module, we like to test sort `INT` of the `minijava` language.

The last two lines specify a positive and a negative test case.
Each test case consists of
  a name,
  a code fragment in double square brackets, and
  a condition which determines
  what kind of test should be performed (parsing) and
  what the expected outcome is (success or failure).

You get instant feedback while editing a test suite.
Since you have not yet implemented the MiniJava editor, all your positive test cases fail.
You will work on this during the next lab.
To read more on SPT, visit the [documentation](http://www.metaborg.org/en/latest/source/langdev/meta/lang/spt/index.html).

### MiniJava Syntax Definition

To write your own test cases, you need to understand MiniJava's syntax.
You can find the ultimate MiniJava syntax definition in the [*MiniJava Language Reference Manual*](http://www.cambridge.org/us/features/052182060X/mjreference/node1.html).
The manual says nothing about reserved words.
But it states that the meaning of a MiniJava program is given by the meaning as a Java program.
Therefore, you should treat all reserved words in Java as reserved in MiniJava as well and provide test cases which address this issue.
This includes not only reserved keywords, but also reserved literals.

### Test Cases

You can now start writing your own test cases.

#### Lexical and Context-free Syntax

Start with the lexical syntax and think about valid and invalid identifiers.
Try to focus on corner cases such as

* identifiers made of a single character,
* mixings of letters and digits (Which kinds of mixing are allowed? Which are not?),
* underscores in identifiers.

The content for the tests for lexical symbols should no be surrounded by layout.
{: .notice .notice-warning}

Next, you should walk through the whole syntax definition.
Keep thinking of valid and invalid code fragments.
Identify interesting corner cases and turn them into positive and negative test cases.

Try to keep your test cases small.
Each test case should focus on a single aspect.
For example, a negative test case should only contain one error.
The name of a test case should reflect the aspect it addresses.

You can organise test cases which focus on similar aspects in test modules.
Again, the name of a test module should reflect these aspects.
Finally, you can organise test suites in folders.

When writing tests that target the differences between Java and MiniJava, only consider
the syntactic differences. For example, identifiers in Java are limited to a certain size.
This is **should not** be checked by your tests as this limitation is not specified in
the context-free grammar of Java.
{: .notice .notice-warning}

#### Disambiguation

Next, you need to focus on disambiguation.
Come up with positive test cases which specify the correct ASTs for nested expressions.
As an alternative, you can also specify two fragments in concrete syntax, which should result in the same AST:

    test left associative addition [[21 + 14 + 14]] parse to minijava [[(21 + 14) + 14]]

You can find a table listing the associativity and priorities of operators in [this book](http://introcs.cs.princeton.edu/java/11precedence/).
Do not focus only on binary expressions.

#### Layout

Finally, you can focus on layout.
Think about places where whitespace is not optional but mandatory and define corresponding positive and negative test cases.
Finish with test cases for single-line comments, standard block comments, and nested block comments.
Single-line comments cannot only end with a newline character, but also at the end of file.

Contrary to some grammars on the web, we allow whitespace around the dots in `System.out.println`.
{: .notice .notice-warning}
