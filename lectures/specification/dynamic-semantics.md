---
layout: page
title: "Dynamic Semantics"
excerpt: "Dynamic Semantics"
tags: ["lectures"]
context: lectures
subcontext: specification
# image: 
#    feature: "lecture.jpg"
#    credit: Delft University of Technology
#    creditlink: http://repository.tudelft.nl/view/MMP/uuid%3Aa2f25709-c56e-453e-9394-4a05acf603a4/
---

## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/bo0ZZ7O8mUb961" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/eelcovisser/dynamic-semantics-specification-and-interpreter-generation" title="Dynamic Semantics Specification and Interpreter Generation" target="_blank">Dynamic Semantics Specification and Interpreter Generation</a> </strong> from <strong><a href="//www.slideshare.net/eelcovisser" target="_blank">Eelco Visser</a></strong> </div>

## Further Reading


> [DynSem: A DSL for Dynamic Semantics Specification](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2015-003.pdf) by Vlad Vergu, Pierre Neron, and Eelco Visser. To appear in the proceedings of [RTA 2015](http://rdp15.mimuw.edu.pl/index.php?site=rta). Preprint [PDF](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2015-003.pdf).

__Abstract__: The formal definition the semantics of a programming language and
its implementation are typically separately defined, with the risk of
divergence such that properties of the formal semantics are not properties
of the implementation.
In this paper, we present DynSem, a domain-specific language for the
specification of the dynamic semantics of programming languages that aims at
supporting both formal reasoning and efficient interpretation.
DynSem supports the specification of the _operational semantics_ of a
language by means of _statically typed conditional term reduction rules_.
DynSem supports _concise_ specification of reduction rules by providing
_implicit build and match coercions_ based on reduction arrows and implicit
term constructors.
DynSem supports _modular_ specification by adopting implicit propagation
of semantic components from I-MSOS, which allows omitting propagation of
components such as environments and stores from rules that do not affect those.
DynSem supports the declaration of _native operators_ for delegation of
aspects of the semantics to an external definition or implementation.
DynSem supports the definition of auxiliary _meta-functions_, which can be
expressed using regular reduction rules and are subject to semantic component
propagation.
DynSem specifications are _executable_ through automatic generation of a
Java-based AST interpreter.
