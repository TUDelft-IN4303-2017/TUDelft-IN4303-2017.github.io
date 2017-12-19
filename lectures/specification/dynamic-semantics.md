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

A compiler translates programs to programs in a lower level target language, such as assembly code for some machine architecture or byte code for a virtual machine. How do we know that such a translation is correct? A generated program is correct if it 'behaves the same as' the program it is compiled from. But how do we know what that is? Judging the correct behavior of generated code, requires knowing the expected behavior of source level code. If the only way of executing programs is through compilation, we have no such judgement. A formal definition of the dynamic semantics of the source level language provides a specification that we can use in judging the correctness of a compiler.

In this lecture we study the specification of the dynamic semantics of a source language through an operational semantics specification. We look at different styles of semantics specification, i.e. 'small-step' and 'big-step' operational semantics. We look at the specification of big-step operational semantics in DynSem, a meta-DSL for dynamic semantics specification. And we look at an approach for representing memory layout at run-time based on the scope graph approach for static name binding.

## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/tOI3vWaXwVEEHa" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/eelcovisser/declare-your-language-dynamic-semantics" title="Declare Your Language: Dynamic Semantics" target="_blank">Declare Your Language: Dynamic Semantics</a> </strong> from <strong><a href="https://www.slideshare.net/eelcovisser" target="_blank">Eelco Visser</a></strong> </div>

[PDF](https://github.com/metaborg/declare-your-language/blob/master/source/dynamics/dyl-9-dynamic-semantics.pdf)


## Further Reading

> Vlad A. Vergu, Pierre Néron, Eelco Visser. [DynSem: A DSL for Dynamic Semantics Specification](http://drops.dagstuhl.de/opus/volltexte/2015/5208/). In Maribel Fernández, editor, 26th International Conference on Rewriting Techniques and Applications, RTA 2015, June 29 to July 1, 2015, Warsaw, Poland. Volume 36 of LIPIcs, pages 365-378, Schloss Dagstuhl - Leibniz-Zentrum fuer Informatik, 2015. [PDF](http://drops.dagstuhl.de/opus/volltexte/2015/5208/pdf/28.pdf)

__Abstract__: The formal semantics of a programming language and its implementation are typically separately defined, with the risk of divergence such that properties of the formal semantics are not properties of the implementation. In this paper, we present DynSem, a domain-specific language for the specification of the dynamic semantics of programming languages that aims at supporting both formal reasoning and efficient interpretation. DynSem supports the specification of the operational semantics of a language by means of statically typed conditional term reduction rules. DynSem supports concise specification of reduction rules by providing implicit build and match coercions based on reduction arrows and implicit term constructors. DynSem supports modular specification by adopting implicit propagation of semantic components from I-MSOS, which allows omitting propagation of components such as environments and stores from rules that do not affect those. DynSem supports the declaration of native operators for delegation of aspects of the semantics to an external definition or implementation. DynSem supports the definition of auxiliary meta-functions, which can be expressed using regular reduction rules and are subject to semantic component propagation. DynSem specifications are executable through automatic generation of a Java-based AST interpreter. 

> Casper Bach Poulsen, Pierre Néron, Andrew P. Tolmach, Eelco Visser. [Scopes Describe Frames: A Uniform Model for Memory Layout in Dynamic Semantics](http://drops.dagstuhl.de/opus/volltexte/2016/6114/). In Shriram Krishnamurthi, Benjamin S. Lerner, editors, 30th European Conference on Object-Oriented Programming, ECOOP 2016, July 18-22, 2016, Rome, Italy. Volume 56 of LIPIcs, Schloss Dagstuhl - Leibniz-Zentrum fuer Informatik, 2016. [PDF](http://drops.dagstuhl.de/opus/volltexte/2016/6114/pdf/LIPIcs-ECOOP-2016-20.pdf)

__Abstract__: Semantic specifications do not make a systematic connection between the names and scopes in the static structure of a program and memory layout, and access during its execution. In this paper, we introduce a systematic approach to the alignment of names in static semantics and memory in dynamic semantics, building on the scope graph framework for name resolution. We develop a uniform memory model consisting of frames that instantiate the scopes in the scope graph of a program. This provides a language-independent correspondence between static scopes and run-time memory layout, and between static resolution paths and run-time memory access paths. The approach scales to a range of binding features, supports straightforward type soundness proofs, and provides the basis for a language-independent specification of sound reachability-based garbage collection. 




