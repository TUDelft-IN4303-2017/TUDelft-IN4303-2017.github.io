---
layout: page
title: "Milestone 2 FAQ"
excerpt: "Milestone 2 FAQ"
tags: ["assignment"]
context: assign
subcontext: ms2
---

{% include _toc.html %}

# Miscellaneous

Some students cannot access <http://metaborg.org> because the TU Delft DNS server fails to
resolve. As a workaround you can access the documentation at <http://spoofax.readthedocs.io>.

# Debugging

<a name="build-failure"></a> 

### How do I know if building my language failed?

Make sure you have the console view open -- use `Window > Show View > Console` to open it. If the
build fails, the build process logs `BUILD FAILED`, otherwise it will show `Reloading language
project eclipse:///minijava`.

Sometimes the build fails with the following exception:

```
  org.metaborg.core.MetaborgException:
    Previous build failed  and no change in the build input has been
    observed, not rebuilding. Fix the problem, or clean and rebuild
    the project to force a rebuild.
```

The build will run again after you clean you project.

### How do I know if all my constructors are correct?

Unfortunately there are no errors inside NaBL2 files if you use non-existing constructors. You can
check for this by opening the Stratego file that is generated from the NaBL2 file. This is done by
selecting `Spoofax > Analysis > Generate Stratego` for an NaBL2 file. Make sure that you import your
MiniJava signatures in the NaBL2 file, or you will also get errors for constructors that *do* exist.

<a name="analysis-failure"></a> 

### Why does my analysis crash?

This usually happens because you made an error in your NaBL2 rule definitions. At the moment there
is no static analysis on the NaBL2 files, but some common errors are:

1. A recursive call on a variable that is not bound in the match pattern. For example:
 
   ```
   [[ App(e1,e2) ^ (s) ]] := ..., [[ e ^ (s) ]], ...
   ```
  
   The variable `e` is not bound in the pattern of the rule, and will automatically be a constraint
   variable. In this case recursion on `e1` or `e2` will be correct.

1. Using a 'free' scope. For example,

   ```
   [[ ... ^ (s) ]] := ..., s' ---> s, new s2.
   ```
  
   The variable `s'` is used as a scope, but it is not bound, and will be a constraint variable
   instead of a scope. In this example `s` and `s2` can be used in scope position.

### How can I check if my analysis worked correctly?

1. Write some example programs. See if you get the expected errors and warnings, or see of Ctrl/Cmd
   clicking on a reference takes you to the expected definition.
1. Run the tests you wrote for the previous lab.
1. Inspect the detailed results of analysis by selecting the menu `Spoofax > Analysis > Show File
   Analysis`. This menu is available when your active editor is a MiniJava program. This file
   contains an overview of the generated constraints, the scope graph that was built
   etc. [See here](#result-exceptions) for more info if you get an exception when opening the
   analysis results.

<a name="result-exceptions"></a> 

### Why do I get exceptions when hovering the file or trying to show analysis results?

Sometimes the Spoofax update seems incomplete, and you get exceptions about missing primitives
(usually named something like `SG_ast_...`). This can be resolved by downloading a fresh Eclipse
with Spoofax ([see instructions](/documentation/spoofax.html#downloading)).

### Can I see which constraint rules are traversed for a program?

Make sure you have the console view open -- use `Window > Show View > Console` to open it. Whenever
a program is analyzed, console logs which terms are matched and which rules are applied.

### Why do my tests fail?

There are several possible reasons why your tests do not work.

1. An error `Unable to access the language under test: 'minijava'.` at the top of the file means the
   language was not built, or the build failed ([see here](#build-failure) for more info).
1. An error `Expected analysis to succeed` on a test means analysis has crashed, and the test cannot
   be run. [See here](#build-failure) for info on analysis crashes.
1. Error `Found unexpected matching messages outside the test region` on the test occurs when you
   test for messages (e.g., no errors), but some messages of the expected severity appear in the
   fixture instead of the test. Because this could mean an error in the location of the messages,
   this is disallowed altogether.
1. The test expectation was not met. The error messages give more information on the cause (e.g.,
   there were errors, when none were expected).

Protip: writing small example programs of the case you are investigating can be a quick way to see
if your implementation behaves the way you expect.
