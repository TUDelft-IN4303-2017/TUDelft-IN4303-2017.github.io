---
layout: page
title: "Milestone 1 FAQ"
excerpt: "Milestone 1 FAQ"
tags: ["assignment"]
context: assign
subcontext: ms1
---

{% include _toc.html %}

# Installation

##### How can I solve the "Unable to access language under test" error?

If you get the error "Unable to access the language under test: 'minijava'", then please do the following:

1. Check that you are using the latest version fo Spoofax Eclipse (at the moment: 2.3.0.20170911-122039-in4303) by going to *Help* > *Installation Details*. If you are using an older version, please download the newest version (again) as per the [instructions](/assignments/ms1/).
2. Make sure the path to your Spoofax installation does not contain a space! If this is the case, move your Spoofax installation to a location without a space.
3. Clean and build all projects (through *Project* > *Clean*, *Project* > *Build All*). You can see what's going on by opening the console (*Window* > *Show View* > *Console*).
4. Finally, close and re-open any test files that were already open.

##### The parser succesfully parses the empty string, even though this is not defined for the given start symbol.

There is an error in the JSGLR parser that always parses an empty string (regardless of start symbol). You can ignore this.

##### My tests for start symbols INT and ID fail.

Check that INT and ID are defined as `context-free start symbol` (even though they are part of the lexical syntax).

##### How can I disable the cygwin warnings about paths when building on Windows?

1. Add a system [environment variable](http://superuser.com/questions/284342/what-are-path-and-other-environment-variables-and-how-can-i-set-or-use-them)
   called `CYGWIN` with the value `nodosfilewarning`.
2. Restart eclipse.

# Setup

##### After importing the projects into Eclipse I receive several errors that have to do with importing and including files. 

If you get errors after [importing the projects into Eclipse](/assignments/ms1/lab1.html#importing-projects-into-eclipse) you should build the project by choosing *Project > Build All* from the menu.


# Syntax Definition

##### Is it possible to split my syntax definition up in several files possibly in subfolders?

Yes, that is possible. You can have the following directory tree for example:

    syntax/
      subdir/
        c.sdf3
      b.sdf3
      MyLanguage.sdf3

`MyLanguage.sdf3` should have the following imports if it depends on `b` and `c`:

    module MyLanguage

    imports
      b
      subdir/c

and `c` should be declared as

    module subdir/c

##### Why do my disambiguation tests fail?

Disambiguation tests (i.e. tests of the form `test name [[...]] parse to [[..]]`) compare the ASTs for both test fragments. If these ASTs differ, the test fails. Use the *Show AST* option in the editor's *Transform* menu to view the AST; this may give you clue as to what is going wrong. A common mistake is to introduce a constructor when parsing a parenthesized expression. This can be solved by using the `bracket` attribute.

##### Why does the `bracket` attribute have no effect?

You probably used a constructor name together with the `bracket` attribute.
As the SDF3 editor indicates, this is a mistake.

##### Why are my `context-free priorities` not translated into SDF2?

There is an error in these priorities, which causes the generator to fail.
You probably made a typo in a constructor name.

##### Why does Eclipse become unresponsive when I open or run test files?

There is very likely an error in your rules for `LAYOUT`.
Study the rules found on the slides or in the generated `Common.sdf3` in a new language project.
Fixing these issues should make running your tests way faster.

##### Why do I get a StackOverflowException and how should I handle this?

This is also very likely caused by erroneous rules for `LAYOUT`.

1. Close Eclipse.
2. Delete all files in the `target` and `src-gen` folders in your MiniJava project.
3. Fix the erroneous rules.
4. Build your project.


# Simple Term Rewriting

##### Why am I getting a *Builder failed* error when I click *Show desugared syntax*?

You probably do not have a snippet of code selected that can be transformed by the `desugar` rule, thereby causing the `editor-desugar` rule to fail.
If you select an expression and run *Show desugared syntax*, you will see the output of `desugar` applied to that expression.
Alternatively, you could edit the `editor-desugar` rule to apply the `desugar-all` strategy to transform the complete AST of your MiniJava file.
