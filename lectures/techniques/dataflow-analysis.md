---
layout: page
title: "Dataflow Analysis"
excerpt: "Dataflow Analysis"
tags: ["lecture"]
context: lectures
subcontext: techniques
---

## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/B52IxM5YSnVSHf" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/eelcovisser/dataflow-analysis-71061007" title="Dataflow Analysis" target="_blank">Dataflow Analysis</a> </strong> from <strong><a target="_blank" href="//www.slideshare.net/eelcovisser">Eelco Visser</a></strong> </div>

[PDF](https://github.com/TUDelft-IN4303-2016/lectures/blob/master/12-dataflow-analyses/Dataflow%20Analysis.pdf)

## Further Reading

1.  Andrew W. Appel and Jens Palsberg (2002). Liveness Analysis. In *Modern Compiler Implementation in Java*, 2nd edition. Cambridge University Press.
    
    This chapter motivates liveness analysis as a requirement for register allocation, introduces the terminology we also use in the lecture, and develops an efficent method to calculate liveness information in a backward analysis.

2.  Andrew W. Appel and Jens Palsberg (2002). Dataflow Analysis. In *Modern Compiler Implementation in Java*, 2nd edition. Cambridge University Press.
    
    This chapter takes a more general approach to dataflow analysis. It introduces intermediate representations as the basis for such analyses and presents various analyses in terms of *generate* and *kill* effects. It finally discusses optimisations based on dataflow analyses such as common-subexpression elimination, constant propagation, copy propagation, and dead-code elimination.
  
3.  Flemming Nielson, Hanne R. Nielson, and Chris Hankin (2005). Principles of Program Analysis, 2nd corrected edition. Springer.
    
    This book gives an overview of the four major approaches to program analysis: data flow analysis, constrained based analysis, abstract interpretation, and type and effect systems. The presentation demonstrates the extensive similarities between the approaches. It presents the mathematical foundations and develops a generic framework for data flow analyses.
