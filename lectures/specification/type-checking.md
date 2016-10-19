---
layout: page
title: "Type Checking"
excerpt: "Type Checking"
tags: ["lecture"]
context: lectures
subcontext: specification
---

In this lecture we study constraint-based static semantic analysis using the scope graph framework for name resolution. After first exploring the concepts, we then look at the realization of these concepts in the NaBL2 meta DSL. To illustrate NaBL2 we use excerpts from a definition of name and type analysis for the Tiger language.

## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/3skuUg0XB9223x" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/eelcovisser/type-analysis" title="Type analysis" target="_blank">Type analysis</a> </strong> from <strong><a target="_blank" href="//www.slideshare.net/eelcovisser">Eelco Visser</a></strong> </div>

[PDF](https://github.com/TUDelft-IN4303-2016/lectures/blob/master/08-type-analysis/Type%20Analysis.pdf)

## Further Reading


1. Pierre Neron, Andrew Tolmach, Eelco Visser, Guido Wachsmuth. [A Theory of Name Resolution](http://researchr.org/publication/NeronTVW15). ESOP 2015

    This award winning paper introduces scope graphs for describing the binding and scoping facts of programs. A resolution calculus describes how references resolve to declarations in terms of a reachability and visibility relation. A resolution algorithm is shown to be sound and complete with respect to the calculus. 
    
2. Hendrik van Antwerpen, Pierre Neron, Andrew P. Tolmach, Eelco Visser, Guido Wachsmuth. [A Constraint Language for Static Semantic Analysis based on Scope Graphs](http://researchr.org/publication/AntwerpenNTVW16). PEPM 2016

    This paper introduces a constraint language for name and type analysis based on scope graphs, a semantics for the language, and an algorithm for solving constraints. The paper shows an example of constraint generation for a toy language. This approach is the basis for the design of the NaBL2 language, which supports the definition of rules that transform an AST to a collection of constraints. The paper coins the term 'type-dependent name resolution' to characterize name resolutions that interact with type analysis.
