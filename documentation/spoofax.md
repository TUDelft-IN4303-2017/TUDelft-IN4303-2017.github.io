---
layout: page
title: "Spoofax"
excerpt: "Spoofax"
tags: ["documentation"]
context: assign
subcontext: doc
---

{% include _toc.html %}

We use the [Spoofax language workbench](http://spoofax.org) in the lab to develop a MiniJava editor.

The Spoofax Language Workbench supports the definition of all aspects of textual languages, such as syntax, static analysis, and compilation, using high-level, declarative meta-languages.
From a language definition using these meta-languages, Spoofax generates full-featured Eclipse and IntelliJ editor plugins, as well as a command-line interface.
The generated editors include syntax highlighting, syntax checking, parse error recovery, error markers for syntactic and semantic errors, and custom operations, such as invoking an interpreter or compiler.

## Downloading

In this lab, we will be using the Spoofax Eclipse plugin.
You can download an Eclipse installation with the Spoofax Eclipse plugin preinstalled from our build farm:

* [Windows 32-bits](http://buildfarm.metaborg.org/job/metaborg/job/spoofax-releng/job/in4303/lastSuccessfulBuild/artifact/dist/spoofax/eclipse/spoofax-windows-x86-jre.zip)
* [Windows 64-bits](http://buildfarm.metaborg.org/job/metaborg/job/spoofax-releng/job/in4303/lastSuccessfulBuild/artifact/dist/spoofax/eclipse/spoofax-windows-x64-jre.zip)
* [Linux 32-bits](http://buildfarm.metaborg.org/job/metaborg/job/spoofax-releng/job/in4303/lastSuccessfulBuild/artifact/dist/spoofax/eclipse/spoofax-linux-x86-jre.tar.gz)
* [Linux 64-bits](http://buildfarm.metaborg.org/job/metaborg/job/spoofax-releng/job/in4303/lastSuccessfulBuild/artifact/dist/spoofax/eclipse/spoofax-linux-x64-jre.tar.gz)
* [Mac OS X (Intel only)](http://buildfarm.metaborg.org/job/metaborg/job/spoofax-releng/job/in4303/lastSuccessfulBuild/artifact/dist/spoofax/eclipse/spoofax-macosx-x64-jre.tar.gz)

To install Spoofax, unpack the downloaded archive into a location with write access. To run Spoofax, just start Eclipse.

On Windows, do **not** unpack the Eclipse installation into Program Files, because no write access is granted there, breaking Spoofax.
{: .notice .notice-warning}

Fore more detailed instructions on installing and running Spoofax, follow our [Getting Started guide](http://spoofax.org/en/latest/source/langdev/start.html#unpack), but skip the download step (start at the unpack step) and use the file you downloaded from one of the above links.

## Updating

To update the Spoofax plugin in Eclipse, perform the following steps:

1. go to **Help -> Install New Software...**
2. in the **Work with** field, enter `http://buildfarm.metaborg.org/job/metaborg/job/spoofax-releng/job/in4303/lastSuccessfulBuild/artifact/dist/spoofax/eclipse/site/`
3. press enter and wait for the update site to load
4. press **Select All**
5. press **Next** and wait for Eclipse to calculate what to install and update
6. press **Finish** to download and install updates
7. when a security warning pops up, press **OK** to dismiss the warning
8. when Eclipse has installed all updates, it will ask for a restart, press **Yes** to restart Eclipse

If installation fails with errors indicating that "updates are not permitted", it means that your Eclipse installation is in a location that requires admin rights to write files. This is the case on Windows if you've copied Eclipse to Program Files. Move it to a directory where you have write access, such as the desktop, to solve the problem.
{: .notice .notice-warning}

## Documentation

We host documentation for Spoofax and its meta-languages at [metaborg.org](http://metaborg.org):

* [Getting Started with Language Development](http://metaborg.org/en/latest/source/langdev/start.html)
* [The SPT Testing Language](http://metaborg.org/en/latest/source/langdev/meta/lang/spt.html)
* [The Syntax Definition Formalism SDF3](http://metaborg.org/en/latest/source/langdev/meta/lang/sdf3.html)
* [The NaBL2 Name Binding Language](http://metaborg.org/en/latest/source/langdev/meta/lang/nabl2/index.html)
* [The Stratego Transformation Language](http://metaborg.org/en/latest/source/langdev/meta/lang/stratego/index.html)
