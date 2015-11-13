---
layout: page
title: "Lab 5: Testing Name Analysis"
excerpt: "Lab 5: Testing Name Analysis"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

In this lab, you develop a test suite for name analysis.
The test suite consists of resolution test cases and constraint test cases.

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
  * errors on instantiating, subclassing, or referencing the main class.

### Submission

You can work on labs 5 and 6 in the same branch of your repository, but for grading and early feedback you need to submit your test project with a pull request against branch `assignment5` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.

Test files created during this assignment should go into the `MiniJava-tests-names` project.
{: .notice .notice-warning}

The deadline for submissions is November 8th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 100 points for the coverage of your test cases. Therefor, we run your test cases against 25 correct and erroneous definitions of MiniJava. The total number of points depends on how many test cases you pass in each of the following groups:

- resolution (40 points)
    - classes (20 points)
    - fields (8 points)
    - variables & parameters (12 points)
- constraints (60 points)
    - duplicate definitions (20 points)
    - missing definitions (16 points)
    - hiding (9 points)
    - main class (15 points)

It is important to name tests in a testsuite uniquely. Tests in different test suites can have the same name, but for grading, we need to be able to distinguish tests in the same test suite by their name.
{: .notice .notice-warning}

### Early Feedback

We provide early feedback for the effectiveness of your test cases.
This feedback gives you an indication which parts of the name binding rules might still be uncovered by your tests.
It includes a summary on how many erroneous language definitions you reveal and how many points you earn by detecting them.

## Detailed Instructions

### Preliminaries

#### Updating Spoofax

A new version of Spoofax is required for this lab. To update Spoofax in Eclipse:

1. go to **Help -> Install New Software...**
2. in the **Work with** field, enter `http://download.spoofax.org/update/in4303`
3. check **Spoofax Core** only
4. press **Next** twice
5. agree to the license agreements and press **Finish** to download and install updates
6. when a security warning pops up, press **OK** to dismiss the warning
7. when Eclipse has installed all updates, it will ask for a restart, press **Yes** to restart Eclipse

If installation fails with errors indicating that "updates are not permitted", it means that your Eclipse installation is in a location that requires admin rights to write files. This is the case on Windows if you've copied Eclipse to Program Files. Move it to a directory where you have write access, such as the desktop, to solve the problem.
{: .notice .notice-warning}

#### GitHub Repository

We provide you with a template for this assignment in the `assignment5` branch.
See the [Git documentation](/documentation/git.html#template) on how to check out this branch.

You can work on assignment 5 and 6 at the same time, but there is no need for a separate `assignment6` branch in your own repository.
Push all your work for assignment 5 and 6 to the `assignment5` branch, but when you submit your work through a pull request, be sure to select the right branch of the upstream repository.

#### Initial Editor Project

The template provides you with a fresh MiniJava editor project, which covers the concrete and abstract syntax of MiniJava and desugarings.
For grading purposes, you are required to use this project as a starting point for milestone 2.
You should build the new project, following these steps:

1. select the project folder
2. select **Build Project** from the **Project** menu
3. the console will report success or failure

#### Initial Test Projects

You also find two new test projects in your GitHub repository.
You need to import these into your Eclipse workspace, following these steps:

1. right-click into the Package Explorer
2. select **Import...** from the context menu
3. choose **General/Existing Projects into Workspace** from the list
4. select the project to import
5. press the **Finish** button

You should use `MiniJava-tests-names` for this lab.

### Testing Reference Resolution

In test cases for reference resolution,
 you write syntactically correct programs and
 mark names at definition and use sites with inner square bracket blocks.
You can then relate the use site with the definition site in a `resolve x to y` clause,
  using numbers to refer to the inner blocks.
For example, the following two test cases require to resolve the type `Foo` to the name in the definition of class `Foo`:

```
module resolution

language MiniJava
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

You can use `setup` headers and footers to avoid repeating parts in similar test cases. See the [SPT documentation](http://metaborg.org/spt/#setup-blocks) for details.

You should come up with test cases for the resolution of class names, field names, parameter names, and variable names.
Start with simple test cases, but keep in mind that coverage is the main criterion for your grade.
It is important to think about
 forward and backward references,
 and resolution in the presence of homonyms.

Make sure that there are no errors in tests with a `resolve x to y` clause, these tests are invalid when there are errors.
{: .notice .notice-warning}

Do not use start symbols other than Program.
{: .notice .notice-warning}

### Testing Error Checking

In test cases for error checking, you need to specify the number of errors, warnings, or notes in a test case
 in `errors`, `warnings`, or `notes` clauses.
For example, the following test cases specify a correct MiniJava program,
 a program with two errors which are reported on the name of a duplicate class `Foo`,
 and another program with an error which is reported on the name of an unknown class `Bar`:

```
module resolution

language MiniJava
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
]] 2 errors

test error on unknown class [[
  class Main {
    public static void main(String[] args) {
      System.out.println(1);
    }
  }

  class Foo {
    Bar x;
  }
]] 1 error
```

You can start with test cases for duplicate and missing definitions.
Similar to your syntax test cases, you can pair up positive (`0 errors`) and negative test cases.
For duplicate definitions, each definition with the same name should produce an error, which is why the `error on duplicate class` test above expects 2 errors.

For grading, it is important to specify the exact number of errors. You need to make sure that your program does not contain any other errors. For duplicate definitions, you get an error for every definition of the same name. For missing definitions, you get an error for every unresolved reference.
{: .notice .notice-warning}

Next, you should develop test cases for
 fields and variables which hide fields,
 and main class instantiation, subclassing, referencing.
Again, you should keep in mind that coverage is the main criterion for your grade.
