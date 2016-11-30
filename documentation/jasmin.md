---
layout: page
title: "Jasmin"
excerpt: "Jasmin"
tags: ["documentation"]
context: assign
subcontext: doc
---

{% include _toc.html %}

Jasmin is an assembler for the Java Virtual Machine. It takes ASCII descriptions
of Java classes, written in a simple assembler-like syntax using the Java
Virtual Machine instruction set. It converts them into binary Java class files,
suitable for loading by a Java runtime system. We provide you with a Jasmin
editor which was built with Spoofax.

## Updating

To update the Spoofax plugin in Eclipse, perform the following steps:

1. go to **Help -> Install New Software...**
2. in the **Work with** field, enter `http://buildfarm.metaborg.org/job/metaborgcube/job/spoofax-jasmin/job/in4303/lastSuccessfulBuild/artifact/jasmin.eclipse.updatesite/target/site/`
3. press enter and wait for the update site to load
4. press **Select All**
5. press **Next** and wait for Eclipse to calculate what to install and update
6. press **Finish** to download and install updates
7. when a security warning pops up, press **OK** to dismiss the warning
8. when Eclipse has installed all updates, it will ask for a restart, press **Yes** to restart Eclipse

If installation fails with errors indicating that "updates are not permitted", it means that your Eclipse installation is in a location that requires admin rights to write files. This is the case on Windows if you've copied Eclipse to Program Files. Move it to a directory where you have write access, such as the desktop, to solve the problem.
{: .notice .notice-warning}
