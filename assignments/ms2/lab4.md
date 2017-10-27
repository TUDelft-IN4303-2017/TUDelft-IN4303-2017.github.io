---
layout: page
title: "Lab 4: Testing Name Analysis"
excerpt: "Lab 4: Testing Name Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you develop a test suite for name analysis. The test suite consists of resolution test
cases and constraint test cases.

## Overview

### Objectives

Develop a test suite for name analysis.
The test suite should provide

1. Test cases for the resolution of
  * class names,
  * field names,
  * parameter names, and
  * variable names.
2. Test cases for
  * errors on duplicate definitions of classes, fields, parameters, and variables,
  * errors on missing definitions of classes, fields, parameters, and variables,
  * warnings on variables hiding fields,
  * errors on fields hiding fields in a parent class.

### Submission

You need to submit your test project with a pull request against branch `assignment4` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such
a request.  We expect to find your Spoofax project `minijava` and your test project
`minijava.test.names` next to each other in the root of the repository.

For this lab, you should put all your tests in `minijava.test.names`.
{: .notice .notice-warning}

The deadline for submissions is October 18th 2017, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 100 points for the coverage of your test cases. Therefore, we run your test cases
against 25 correct and erroneous definitions of MiniJava. The total number of points depends on how
many test cases you pass in each of the following groups:

- resolution (44 points)
  - classes (20 points)
  - fields (12 points)
  - variables & parameters (12 points)
- constraints (56 points)
  - duplicate definitions (20 points)
  - missing definitions (16 points)
  - hiding (20 points)

### Early Feedback

We provide early feedback for the effectiveness of your test cases. This feedback gives you an
indication which parts of the name binding rules might still be uncovered by your tests. It includes
a summary on how many erroneous language definitions you reveal and how many points you earn by
detecting them. You have 3 early feedback attempts.

## Detailed Instructions

### Preliminaries

#### Updating Spoofax

A new version of Spoofax is required for this lab.
See the [Spoofax documentation](/documentation/spoofax.html#updating) on how to update Spoofax.

#### GitHub Repository

We provide you with a template for this assignment in the `assignment4` branch. See the
[Git documentation](/documentation/git.html#template) on how to check out this branch.

The template contains 4 Maven projects:

* `minijava`: an initial MiniJava project that covers concrete and abstract syntax, and desugarings,
* `minijava.example`: an empty project for your example MiniJava programs,
* `minijava.test.names`: an empty project for your MiniJava name analysis tests,
* `minijava.test.types`: an empty project for your MiniJava type analysis tests.

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

### Testing Reference Resolution

In test cases for reference resolution,
 you write syntactically correct programs and
 mark names at definition and use sites with inner square bracket blocks.
You can then relate the use site with the definition site in a `resolve x to y` clause,
  using numbers to refer to the inner blocks.
For example, the following two test cases require to resolve the type `Foo` to the name in the definition of class `Foo`:

```
module resolution

language minijava
start symbol Program

test forward class name resolution [[
  class Main {
    public static void main(String[] args) {
      System.out.println(1);
    }
  }

  class Foobar {
    [[Foo]] x;
  }

  class [[Foo]] {}
]] resolve #1 to #2

test backward class name resolution [[
  class Main {
    public static void main(String[] args) {
      System.out.println(1);
    }
  }

  class [[Foo]] {}

  class Foobar {
    [[Foo]] x;
  }
]] resolve #2 to #1
```

After copying this into an SPT file Spoofax will add the error *"Reference resolution failed"* and *"No constraint generation rule for ..."*. This is expected, since your project is missing an implementation for reference resolution (this is part of the next lab).

You can use _fixtures_ to avoid repeating parts in similar test cases. See the [SPT documentation](http://www.metaborg.org/en/latest/source/langdev/meta/lang/spt/test-suites.html#test-fixtures) for details.

You should come up with test cases for the resolution of class names, field names, parameter names, and variable names.
Start with simple test cases, but keep in mind that coverage is the main criterion for your grade.
It is important to think about
 forward and backward references,
 and resolution in the presence of homonyms.

Make sure that there are no errors in tests with a `resolve x to y` clause, these tests are invalid when there are errors.
{: .notice .notice-warning}

Do not use start symbols other than `Program`.
{: .notice .notice-warning}

### Testing Error Checking

In test cases for error checking, you need to specify the number of errors, warnings, or notes in a
test case in `errors`, `warnings`, or `notes` clauses.  For example, the following test cases
specify a correct MiniJava program, a program with two errors which are reported on the name of a
duplicate class `Foo`, and another program with an error which is reported on the name of an unknown
class `Bar`:

```
module resolution

language minijava
start symbol Program

test correct program [[
  class Main {
    public static void main(String[] args) {
      System.out.println(1);
    }
  }

  class Foo {}

  class Foobar {
    Foo x;
  }
]] 0 errors

test error on duplicate class [[
  class Main {
    public static void main(String[] args) {
      System.out.println(1);
    }
  }

  class Foo {}

  class Foo {}
]] >= 1 errors

test error on unknown class [[
  class Main {
    public static void main(String[] args) {
      System.out.println(1);
    }
  }

  class Foo {
    Bar x;
  }
]] >= 1 errors
```

You can start with test cases for duplicate and missing definitions.  Similar to your syntax test
cases, you can pair up positive (`0 errors`) and negative test cases.  For duplicate definitions, we
expect errors on the definitions with the same name.

The number of errors can be hard to predict, because errors sometimes cascade. Therefore, if you
expect any errors, you should use the `>= 1 errors` expectation, even if you expect a specific
number of errors. For example, this expectation was used in the duplicate class test, even though we
would expect exactly two errors.
{: .notice .notice-warning}

Next, you should develop test cases for fields and variables which hide fields, and main class
instantiation, subclassing, referencing.  Again, you should keep in mind that coverage is the main
criterion for your grade.
