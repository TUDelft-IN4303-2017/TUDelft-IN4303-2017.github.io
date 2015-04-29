---
layout: page
title: "Milestone 2: Semantic Analysis"
excerpt: "Milestone 2: Semantic Analysis"
tags: ["assignment"]
context: ms2
---

{% include _toc.html %}

In this milestone, the MiniJava editor from the previous milestone is extended with semantic analyses. The extended editor should be able

* to resolve references in a MiniJava program,
* to calculate types of MiniJava expressions,
* to report semantic errors, warnings, and notes, and
* to provide semantic code completion.

## Initial Projects

We provide you with a fresh MiniJava editor project, which covers the concrete and abstract syntax of MiniJava and desugarings.
For grading purposes, you are required to use this project as a starting point for milestone 2.
You should build the new project, following these steps:

1. select the project folder
2. select **Build Project** from the **Project** menu
3. the console will report success or failure

You also find two new test projects in your GitHub repository.
You need to import these into your Eclipse workspace, following these steps:

1. right-click into the Package Explorer
2. select **Import...** from the context menu
3. choose **General/Existing Projects into Workspace** from the list
4. select the project to import
5. press the **Finish** button

You should use `MiniJava-tests-names` for assignment 5 and `MiniJava-tests-types` for assignment 7.

## Resources

You can find the *MiniJava Language Reference Manual* in the appendix of the book
*Modern Compiler Implementation in Java* (2nd edition).
The relevant pages are available on [Google books](http://books.google.com/books?id=JNs6fWkJZbAC&pg=PA484).

The [MiniJava project](http://www.cambridge.org/us/features/052182060X/)
by Joao Cangussu, Jens Palsberg and Vidyut Samanta provides some example programs
and an HTML version of the reference manual.

The following research papers cover relevant topics of this milestone:

1. L. Kats, R. Vermaas, E. Visser: [Integrated Language Definition Testing: Enabling Test-Driven Language Development](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2011-011.pdf), OOPSLA 2011
2. G. Konat, L. Kats, G. Wachsmuth, E. Visser: [Declarative Name Binding and Scope Rules](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2012-015.pdf), SLE 2012
3. G. Wachsmuth, G. Konat, V. Vergu, D. Groenewegen, E. Visser: [A Language Independent Task Engine for Incremental Name and Type Analysis](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2013-014.pdf), SLE 2013
