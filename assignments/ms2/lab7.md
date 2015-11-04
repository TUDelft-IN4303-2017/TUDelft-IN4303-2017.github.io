---
layout: page
title: "Lab 7: Testing Type Analysis"
excerpt: "Lab 7: Testing Type Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you develop a test suite for type analysis.
The test suite consists of type projection test cases, resolution test cases and constraint test cases.

## Overview

### Objectives

Develop a test suite for type analysis.
The test suite should provide

1. Test cases for the types of expressions
1. Test cases for the resolution of method names
2. Test cases for
  * errors on duplicate method definitions
  * errors on missing method definitions
  * errors on overloaded methods
  * errors on cyclic inheritance
  * errors on fields hiding fields in a parent class
  * notes on overriding methods
  * type errors in expressions
  * type errors in statements
  * type errors in method definitions

### Submission

You need to submit your test project with a pull request against branch `assignment7` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

Test files created during this lab should go into the `MiniJava-tests-types` project.
{: .notice .notice-warning}

The deadline for submissions is November 15th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 100 points for the coverage of your test cases.

It is important to name tests in a testsuite uniquely. Tests in different test suites can have the same name, but for grading, we need to be able to distinguish tests in the same test suite by their name.
{: .notice .notice-warning}

### Early Feedback

We provide early feedback for the effectiveness of your test cases.
This feedback gives you an indication which parts of the name binding rules might still be uncovered by your tests.
It includes a summary on how many erroneous language definitions you reveal and how many points you earn by detecting them.

## Detailed Instructions

### Preliminaries

#### Git Repository

You continue with your work from the previous assignment.
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment7` branch from your previous work.

### Testing Types of Expressions

In test cases for type analysis,
 you write syntactically correct programs and
 mark expressions with inner square bracket blocks.
You can then specify the expected type of the marked expression in a `run`-`to` clause.
For example, the following two test cases require an integer literal to be of type `Int()`
and a variable reference to be of its declared type `Bool()`:

```
module types

language MiniJava
start symbol Program

test integer literal type [[
  class Main {
    public static void main (String[] args) {
        System.out.println([[1]]);
    }
  }
]] run get-type to Int()

test variable reference type [[
  class Main {
    public static void main (String[] args) {
        System.out.println(new Foo().run());
    }
  }

  class Foo {
    public int run() {
      boolean x;
      int y;

      if ([[x]])
        y = 1;
      else
        y = 0;

      return y;
    }
  }
]] run get-type to Bool()
```

You can use `setup` headers and footers to avoid repeating parts in similar test cases. See the [SPT documentation](http://metaborg.org/spt/#setup-blocks) for details.

When using get-type on objects the expected `ClassType` constructor also requires annotations.
These annotations should be added to the constructor using a wild card as done below.

```
test expression id type [[
  class Foobar {
    Foo x;
    public Foo method() {
      return [[x]];
    }
  }
]] run get-type to ClassType("Foo"{_})
```

You should come up with test cases for the types of all kinds of expressions.
Just like previous testing assignments, this assignment is all about the coverage of your test suite.

### Testing Method Name Resolution

We did not test method names in assignment 5, since method name resolution requires type analysis.
Types are available now, so we can test method name resolution.
Consider the following test case as an example:

```
test method name resolution [[
  class Main {
    public static void main (String[] args) {
      System.out.println(new Foo().[[run]]());
    }
  }

  class Foo {
    public int [[run]]() {
      return 1;
    }
  }
]] resolve #1 to #2
```

The type of the callee expression determines the class in which the method declaration can be found.
In this example, the expression `new Foo()` is of type `ClassType("Foo"{_})` and
the corresponding class `Foo` contains a method declaration for `run()`.

You should come up with test cases for the resolution of method names.
Start with simple test cases, but keep in mind that method name resolution is quite complex
 and that coverage is the main criterion for your grade.
It is important to think about forward and backward references,
 resolution in the presence of homonyms and overriding,
 and the influence of class hierarchies on resolution.

You should also come up with test cases for error checking on method names.
This should include test cases for errors on duplicate definitions, missing definitions, and method overloading
 as well as for notes on method overriding.
Similar to previous test cases, you can pair up positive (`0 errors`) and negative test cases.

### Testing Type Error Checking

A type error occurs, when the type of an expression does not conform to its expected type.
Consider the following test case as an example:

```
test print boolean [[
class Main {
    public static void main (String[] args) {
        System.out.println([[true]]);
    }
}
]] 1 error
```

In MiniJava, `System.out.println()` can only print integers.
Thus, there should be an error on `true`, when we pass it to the print statement.
Similarly, type errors can occur in other statements, expressions, and method declarations.
You should come up with test cases for such errors.
Subtyping is a common source for errors not only in programs, but also in language implementations.
It is therefore important to have positive and negative typing tests, which involve correct and incorrect subtyping.

Again, keep in mind that coverage is the main criterion for your grade.
