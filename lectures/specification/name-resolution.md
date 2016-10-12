---
layout: page
title: "Static Name Resolution"
excerpt: "Name Resolution"
tags: ["lecture"]
context: lectures
subcontext: specification
---

In this lecture we study the definition of name binding patterns using the scope graph framework.

## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/AcK35XBUYxtK0X" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/eelcovisser/static-name-resolution" title="Static name resolution" target="_blank">Static name resolution</a> </strong> from <strong><a target="_blank" href="//www.slideshare.net/eelcovisser">Eelco Visser</a></strong> </div>

[PDF](https://github.com/TUDelft-IN4303-2016/lectures/blob/master/07-name%20analysis/Name%20Resolution.pdf)

## Further Reading

1. Gabriel D. P. Konat, Lennart C. L. Kats, Guido Wachsmuth, Eelco Visser. [Declarative Name Binding and Scope Rules](http://researchr.org/publication/KonatKWV12). Software Language Engineering, SLE 2012   This paper introduces NaBL, a declarative language for describing the name binding and scoping rules of programming languages. While the language is being replaced by the next generation NaBL2 in Spoofax, it is still an instructive read, not in the least since the NaBL design is arguably more elegant than NaBL2 (but less expressive).
2. Eelco Visser, Guido Wachsmuth, Andrew P. Tolmach, Pierre Neron, Vlad A. Vergu, Augusto Passalaqua, Gabriel D. P. Konat. [A Language Designer's Workbench. A one-stop-shop for implementation and verification of language designs.](http://researchr.org/publication/VisserOnward14). Onward 2014
   This paper outlines the vision of a language designer's workbench that allows the description of a language design using high-level declarative meta-languages, which are used to derive implementations of various artifacts such as IDEs and interpreters, and are also the source for verification of safety properties. The paper also introduces the TS language for specification of type system rules to complement NaBL name binding rules.3. Pierre Neron, Andrew Tolmach, Eelco Visser, Guido Wachsmuth. [A Theory of Name Resolution](http://researchr.org/publication/NeronTVW15). ESOP 2015    This award winning paper introduces scope graphs for describing the binding and scoping facts of programs. A resolution calculus describes how references resolve to declarations in terms of a reachability and visibility relation. A resolution algorithm is shown to be sound and complete with respect to the calculus.     4. Hendrik van Antwerpen, Pierre Neron, Andrew P. Tolmach, Eelco Visser, Guido Wachsmuth. [A Constraint Language for Static Semantic Analysis based on Scope Graphs](http://researchr.org/publication/AntwerpenNTVW16). PEPM 2016
    This paper introduces a constraint language for name and type analysis based on scope graphs, a semantics for the language, and an algorithm for solving constraints. The paper shows an example of constraint generation for a toy language. This approach is the basis for the design of the NaBL2 language, which supports the definition of rules that transform an AST to a collection of constraints. The paper coins the term 'type-dependent name resolution' to characterize name resolutions that interact with type analysis.

## Examples

1. [Tiger in Spoofax](https://github.com/MetaBorgCube/metaborg-tiger/tree/master/metaborg-tiger) with name binding rules in NaBL2