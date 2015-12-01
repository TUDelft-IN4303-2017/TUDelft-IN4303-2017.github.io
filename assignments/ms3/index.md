---
layout: page
title: "Milestone 3: Code Generation"
excerpt: "Milestone 3: Code Generation"
tags: ["assignment"]
context: assign
subcontext: ms3
---

{% include _toc.html %}

In this milestone, you extend the MiniJava editor from the previous milestone to a MiniJava compiler, which is able to generate Java class files from MiniJava programs.

## Jasmin Editor

[Jasmin](http://jasmin.sourceforge.net/) is an assembler for the Java Virtual Machine. It takes ASCII descriptions of Java classes, written in a simple assembler-like syntax using the Java Virtual Machine instruction set. It converts them into binary Java class files, suitable for loading by a Java runtime system.
We provide you with a Jasmin editor which was built with Spoofax.

### Installation

1. Choose *Install New Software...* from the *Help* menu.
2. Use the JasminXT update site `http://download.spoofax.org/update/jasmin` in the *Work with:* text box.
3. Tick *JasminXT* and choose *Next*.
4. Choose *Next*, accept the license agreement, and choose *Finish*.
5. Wait for JasminXT to be installed and restart Eclipse.

### Usage

You can edit Jasmin files with a Spoofax editor. The editor's menu entries allow you to inspect the abstract syntax of Jasmin files, to generate Java class files from them, and to execute those class files. The [GitHub repository](https://github.com/metaborg/spoofax-jasmin) contains various [example programs](https://github.com/metaborg/spoofax-jasmin/tree/master/org.spoofax.lang.jasmin.tests/examples). You should explore them in order to get used to Jasmin's syntax.

## Material

We consider the following material to be useful for this milestone.

1. [Lecture on Virtual Machines](lectures/techniques/virtual-machines)

    In this lecture, we discussed the architecture of the JVM, some Java bytecode instructions, and different approaches to code generation in Spoofax.

2. Java Virtual Machine Specification

Oracle provides an [online version][JVM] of the Java Virtual Machine Specification. Chapters 3, 4, and 6 are particular helpful for this milestone. Furthermore, you can find a comprehensive list of [Java bytecode instructions][] in the English Wikipedia.

[JVM]: http://java.sun.com/docs/books/jvms/second_edition/html/VMSpecTOC.doc.html (The Java Virtual Machine Specification, 2nd edition)
[Java bytecode instructions]: http://en.wikipedia.org/wiki/Java_bytecode_instruction_listings (List of Java bytecode instructions)

### Stratego Standard Library

Many useful strategies from Stratego's standard library are documented in this [API Documentation][LibDoc].

[LibDoc]: http://releases.strategoxt.org/docs/api/libstratego-lib/stable/docs/ (Stratego Library API Documentation)
