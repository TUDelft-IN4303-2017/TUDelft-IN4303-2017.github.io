---
layout: page
title: "Lab 2: Syntax Definition"
excerpt: "Lab 2: Syntax Definition"
tags: ["assignment"]
context: assign
subcontext: ms1
---

{% include _toc.html %}

In this lab, you define the concrete and abstract syntax of MiniJava in Spoofax.
From this definition, you generate an Eclipse editor, that provides syntax checking, error recovery and syntax highlighting.

## Overview

### Objectives

Develop a syntax definition for MiniJava in SDF3 and generate an Eclipse editor from it.
The definition should include:

1. A lexical syntax definition for
  * sort `ID`,
  * sort `INT`,
  * whitespaces,
  * single line comments and
  * nested block comments.
2. A context-free syntax definition for sorts
  * `Program`,
  * `MainClass`,
  * `ClassDecl` (including `FieldDecl`),
  * `VarDecl`,
  * `MethodDecl` (including `ParamDecl`),
  * `Type`,
  * `Statement` and
  * `Exp`.

    For grading, it is required to comply with all sort names literally.
    {: .notice .notice-warning}

3. Constructor names for context-free syntax rules.
4. Disambiguation rules.


### Submission

You need to submit your MiniJava project with a pull request against branch `assignment2` on GitHub.
The [Git documentation](/documentation/git.html#submitting-an-assignment) explains how to file such a request.
As part of your submission, we ask you to provide a short explanation of the organisation of your syntax definition in `MiniJava/syntax/README.md`.
To help us to maintain your submissions, also submit this paragraph in WebLab and add a link to your final pull request there.
Finally, to help the manual grading progress, we ask you to paste the content of `MiniJava/include/MiniJava.str` into WebLab.

The deadline for submission is September 24th, 23:59.
{: .notice .notice-warning}

### Grading

You can earn up to 5 points for the organisation of your syntax definition. We take its structure, module names, and your explanation into account.
You can earn up to 75 points for your concrete syntax definition.
Therefor, we run 333 test cases against your implementation.
You earn points, when your implementation passes test cases.
The total number of points depends on how many test cases you pass in each of the following groups:

* lexical syntax (15 points)
  * `ID` (1 point)
  * `INT` (1 point)
  * whitespace (1 point)
  * simple comments (1 point)
  * simple block comments (1 point)
  * nested block comments (challenge, 10 points)
* context-free syntax (30 points)
  * `Program` (1 point)
  * `MainClass` (2 points)
  * `ClassDecl` (2 points)
  * `VarDecl` (2 points)
  * `Type` (2 points)
  * `MethodDecl` (5 points)
  * `Statement` (6 points)
  * `Exp` (10 points)
* disambiguation (30 points)
  * associativity (10 points)
  * precedence (20 points)

You can earn up to 20 points for your abstract syntax definition.
Therefor, we run a few test cases against your implementation and generate an abstract syntax tree for the example program `MiniJava-examples/program1.mjv`.
We inspect your syntax definition and the generated AST manually.
We particular focus on
 meaningful constructor names,
 distinctness of different constructs,
 and the representation of formal and actual parameter lists.

### Early Feedback

We provide early feedback for the correctness of your syntax definition.
This feedback gives you an indication which parts of your syntax definition might still be wrong.
It includes a summary on how many tests you pass and how many points you earn by passing them.

## Detailed Instructions

### Preliminaries

#### Git Repository

You continue with your work from the previous assignment.
See the [Git documentation](/documentation/git.html#continue-from-previous-assignment) on how to create the `assignment2` branch from your previous work.

We have made some changes in the template for this assignment, you need to merge the changes from `upstream/assignment2`.
See [Pulling in changes from upstream](/documentation/git.html#pulling-in-changes-from-upstream) on how to do this. Merging these changes provides you with an example program at `MiniJava-examples/program1.mjv`.

#### Committing Derived Artifacts

While you typically do not commit derived artifacts, we require some of these artifacts for grading.
Thus, you should commit derived artifacts from the `include` directory of the `MiniJava` project.
Please check whether the derived artifacts are there when pushing your changes and submitting your pull request.

The Git plugin of Eclipse will ignore the derived artifacts by adding the include directory to the .gitignore file.
You must disable this behavior in the Eclipse preferences. Go to *Team -> Git -> Projects* in the Eclipse preferences and disable "Automatically ignore derived resources by adding them to .gitignore".
Check the MiniJava/.gitignore file and remove the /include/ entry, if it is there. Eclipse hides files starting with ., so you need to open the .gitignore file some other way.
{: .notice .notice-warning}

### Agile Software Language Engineering

Spoofax supports short development cycles.
This enables you to develop your syntax definition step by step.
In each step, you focus on a single aspect,
 for example a particular nonterminal symbol or a particular grammar rule.
After each step, you can check your progress by building the project and running your test cases.
This requires you to declare the same start symbols in your syntax definition as in your test suites.

Once you build your project, Spoofax will run all the tests in your test project automatically.
When you have many tests, Spoofax might not be able to handle them. If you experience delays, you can split tests into more files, comment out bigger tests, or close the test project.
{: .notice .notice-warning}

You can also test the generated editor in the same Eclipse instance.
This requires you to specify the start symbol that is used by the editor
  in the main editor description `editor/MiniJava.main.esv`.
Of course, this should also be a start symbol in your syntax definition.
You can test the editor by first building your project and opening a MiniJava program.  
In Eclipse, you build a project by choosing *Build Project* from the *Project* menu or by pressing the corresponding keyboard shortcut.
When you later rebuild your project, any open MiniJava editor is updated to the new version you just built.

You can also use *Show AST* in the editor's *Transform* menu to test your abstract syntax definition interactively.
While you change a MiniJava program in the editor, its corresponding AST is updated automatically.
You might notice that the editor will give you an AST even for syntactically incorrect programs.
This is because Spoofax editors support syntactic error recovery.

![Menu entry to inspect the abstract syntax of a program fragment.](http://strategoxt.org/pub/Spoofax/Features/show-abstract-syntax.png  "Menu entry to inspect the abstract syntax of a program fragment.")

### Syntax Definition

You should define your syntax in [SDF3](http://metaborg.org/sdf3/).
You can start by opening the file `syntax/MiniJava.sdf3` in your MiniJava project.
When you save this file, you should get a corresponding file `src-gen/syntax/MiniJava.sdf`.
You can also split your syntax definition over several modules in `syntax/` and import modules into module `MiniJava`.


```
module MiniJava

imports

  Common
```

The module already imports a module `Common`, which you can find in any initial Spoofax editor project.
This module provides syntax definitions for common lexical sorts such as identifiers, integers, strings, and whitespace, including single line and block comments.

#### Context-free Syntax

Start with the context-free syntax of the language.
Use the context-free grammar in the *MiniJava Language Reference Manual* as a reference.

When you define your syntax definition bottom-up, you start with sorts such as `Type` and `VarDecl`.
This allows you to run your tests frequently and check your progress.

We recommend to use *template productions* for your context-free syntax definition, since this will give you a head start for the next lab.
When you use template productions, you need to make sure that templates are correctly tokenised.
Otherwise, the parser would reject layout in certain positions, for example between `[` and `]` in an array type.
Check the SDF3 documentation for details.
In case you want to use `<` or `>` as symbols inside a template, you can use alternate template brackets `[...]`.

You need disambiguation constructs to disambiguate your syntax definition.
You can specify the associativity with attributes `left`, `right`, or `non-assoc`.
These attributes are added to the end of a rule:

```
context-free syntax

  Exp.Constr1 = ... {left}
```

You can specify precedence in a `context-free priorities` section.
In this section, you order groups of rules:

```
context-free priorities

  {
    Exp.Constr1
    Exp.Constr2
  } > { ... } > ...
```

You can also define the associativity of a group:

```
context-free priorities

  { left:
    Exp.Constr1
    Exp.Constr2
  } > { ... } > ...
```

#### Lexical Syntax

Continue with the lexical syntax definition including identifiers, integer, and simple layout.
The `Common` module in `syntax/Common.sdf3` provides already definitions, but these do not comply with MiniJava.
You can either fix these definitions in `Common`, or define and import your own module and use `Common` only for inspiration.

First, define lexical syntax rules:

```
lexical syntax

  ID     = ...
  INT    = ...
  LAYOUT = ...
```

Second, define follow restrictions to ensure longest matches:

```
lexical restrictions

  ID -/- ...
  INT -/- ...

context-free restrictions

 LAYOUT? -/- ...
```

Finally, use rejection rules to rule out reserved words.

```
lexical syntax

  ID = ... {reject}
```

In your tests, you declare `ID` and `INT` as start symbols for those tests. To get those tests running, you also need to define those as start symbols in your grammar:

```
context-free start-symbols

  ID INT
```

You now can run your tests. It is important to get your grammar working at this stage. Do not move on if you have issues here, since there is a high chance that these issues influence your other tests as well. If you experience long running times for your tests, this is most likely caused by an erroneous definition of `LAYOUT`.
{: .notice .notice-danger}

Finally, you should add lexical syntax rules for comments to your syntax definition.
Start with single line comments.
Continue with unnested block comments.
Do not forget to define follow restrictions.

### Challenge

Challenges are meant to distinguish excellent solutions from good solutions.
Typically, they are less guided and require more investigation or higher programming skills.
{: .notice .notice-success}

This lab's challenge is to support nested block comments.
