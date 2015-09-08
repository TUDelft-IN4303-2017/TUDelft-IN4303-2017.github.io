---
layout: page
title: "Milestone 1: Syntax Analysis"
excerpt: "Milestone 1: Syntax Analysis"
tags: ["assignment"]
context: assign
subcontext: ms1
---

{% include _toc.html %}

In the first milestone, an initial Spoofax project is extended to a basic MiniJava editor, that is able

* to report syntax errors,
* to assist the creation of MiniJava programs with code templates,
* to show the abstract syntax of a MiniJava program,
* to provide an outline view of a MiniJava program,
* to support code folding, and
* to pretty-print a MiniJava program.

## Prerequisites

### Spoofax Installation

The recommended way to install Spoofax is to [download a prebuilt package from our build farm](http://buildfarm.metaborg.org/job/spoofax-in4303/lastSuccessfulBuild/artifact/dist/eclipse/).
Choose a package for your operating system, architecture, and with `-jre` in the name.
This is a full Eclipse installation with the Spoofax plugin installed, and with a built-in Java 7 environment so that you do not have to install Java.

Unpack the archive somewhere on your computer, open Eclipse, and choose a location to store your workspace.
Do not store your workspace in your Git repository!

If for some reason the prebuilt package does not work, you can also install Spoofax manually by [following the instructions here](http://metaborg.org/download/).
You need to use the `http://download.spoofax.org/update/in4303/` update site instead of the one listed in the instructions though!

## Resources

You can find the *MiniJava Language Reference Manual* in the appendix of the book
*Modern Compiler Implementation in Java* (2nd edition).
The relevant pages are available on [Google books](http://books.google.com/books?id=JNs6fWkJZbAC&pg=PA484).

The [MiniJava project](http://www.cambridge.org/us/features/052182060X/)
by Joao Cangussu, Jens Palsberg and Vidyut Samanta provides some example programs
and an HTML version of the reference manual.

We provide [answers to frequently asked questions](questions.md) for this milestone.
