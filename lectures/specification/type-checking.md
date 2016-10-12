---
layout: page
title: "Type Checking"
excerpt: "Type Checking"
tags: ["lecture"]
context: lectures
subcontext: specification
---

## Slides

under construction

## Further Reading
1. Pierre Néron, Andrew Tolmach, Eelco Visser, Guido Wachsmuth. [A Theory of Name Resolution](http://researchr.org/publication/NeronTVW15). ESOP 2015    This award winning paper introduces scope graphs for describing the binding and scoping facts of programs. A resolution calculus describes how references resolve to declarations in terms of a reachability and visibility relation. A resolution algorithm is shown to be sound and complete with respect to the calculus.     2. Hendrik van Antwerpen, Pierre Neron, Andrew P. Tolmach, Eelco Visser, Guido Wachsmuth. [A Constraint Language for Static Semantic Analysis based on Scope Graphs](http://researchr.org/publication/AntwerpenNTVW16). PEPM 2016
    This paper introduces a constraint language for name and type analysis based on scope graphs, a semantics for the language, and an algorithm for solving constraints. The paper shows an example of constraint generation for a toy language. This approach is the basis for the design of the NaBL2 language, which supports the definition of rules that transform an AST to a collection of constraints. The paper coins the term 'type-dependent name resolution' to characterize name resolutions that interact with type analysis.
