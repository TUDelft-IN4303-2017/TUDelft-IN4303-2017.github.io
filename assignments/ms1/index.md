---
layout: page
title: "Milestone 1: Syntax Analysis"
excerpt: "Milestone 1: Syntax Analysis"
tags: ["assignment"]
context: assign
subcontext: ms1
---

{% include _toc.html %}

In the first milestone, we specify the syntax of MiniJava and build a MiniJava editor that is able

* to report syntax errors,
* to assist the creation of MiniJava programs with code completion,
* to show the abstract syntax of a MiniJava program,
* to provide an outline view of a MiniJava program,
* to format a MiniJava program.

## Prerequisites

### Spoofax

We will be building the MiniJava editor with the [Spoofax language workbench](http://spoofax.org).
The Spoofax Language Workbench supports the definition of all aspects of textual languages, such as syntax, static analysis, and compilation, using high-level, declarative meta-languages.
From a language definition using these meta-languages, Spoofax generates full-featured Eclipse and IntelliJ editor plugins, as well as a command-line interface.
The generated editors include syntax highlighting, syntax checking, parse error recovery, error markers for syntactic and semantic errors, and custom operations, such as invoking an interpreter or compiler.

In this lab, we will be using the Spoofax Eclipse plugin.
You can download an Eclipse installation with the Spoofax Eclipse plugin preinstalled from our build farm:

* [Windows 32-bits](http://buildfarm.metaborg.org/job/spoofax-in4303/lastSuccessfulBuild/artifact/dist/eclipse/spoofax-win32-x86-jre.zip)
* [Windows 64-bits](http://buildfarm.metaborg.org/job/spoofax-in4303/lastSuccessfulBuild/artifact/dist/eclipse/spoofax-win32-x86_64-jre.zip)
* [Linux 32-bits](http://buildfarm.metaborg.org/job/spoofax-in4303/lastSuccessfulBuild/artifact/dist/eclipse/spoofax-linux-x86-jre.tar.gz)
* [Linux 64-bits](http://buildfarm.metaborg.org/job/spoofax-in4303/lastSuccessfulBuild/artifact/dist/eclipse/spoofax-linux-x86_64-jre.tar.gz)
* [Mac OS X (Intel only)](http://buildfarm.metaborg.org/job/spoofax-in4303/lastSuccessfulBuild/artifact/dist/eclipse/spoofax-macosx-x86_64-jre.tar.gz)

To get started with the absolute basics of Spoofax, follow our [Getting Started guide](http://spoofax.org/en/latest/source/langdev/start.html), but skip the downloading step (start at the unpacking step) and use the file you downloaded from one of the above links.

## Resources

You can find the *MiniJava Language Reference Manual* in the appendix of the book *Modern Compiler Implementation in Java* (2nd edition).
The relevant pages are available on [Google books](http://books.google.com/books?id=JNs6fWkJZbAC&pg=PA484).

The [MiniJava project](http://www.cambridge.org/us/features/052182060X/) by Joao Cangussu, Jens Palsberg and Vidyut Samanta provides some example programs and an HTML version of the reference manual.

Documentation on Spoofax can be found on the [Spoofax website](http://spoofax.org).

We provide [answers to frequently asked questions](faq) for this milestone.
