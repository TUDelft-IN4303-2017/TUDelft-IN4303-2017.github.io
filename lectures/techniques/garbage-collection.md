---
layout: page
title: "Garbage Collection"
excerpt: "Garbage Collection"
tags: ["lecture"]
context: lectures
subcontext: techniques
---

## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/J7f2RVCcFoOsyv" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/guwac/compiling-imperative-and-objectoriented-languages-garbage-collection" title="Compiling Imperative and Object-Oriented Languages - Garbage Collection" target="_blank">Compiling Imperative and Object-Oriented Languages - Garbage Collection</a> </strong> from <strong><a href="//www.slideshare.net/guwac" target="_blank">Guido Wachsmuth</a></strong> </div>
## Further Reading

1.  Andrew W. Appel and Jens Palsberg (2002). Liveness Analysis. In *Modern Compiler Implementation in Java*, 2nd edition. Cambridge University Press.
    
    This chapter motivates liveness analysis as a requirement for register allocation, introduces the terminology we also use in the lecture, and develops an efficent method to calculate liveness information in a backward analysis.

2. Oracle. [Java Garbage Collection Basics](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html).
    
    This tutorial covers the basics of how garbage collection works with the Hotspot JVM.
  It explains mark-and-sweep, mark-and-compact, and generational collection.
  It further explains how to monitor the garbage collection process using Visual VM,
  and which garabage collectors are available in the Java SE 7 Hotspot JVM.
  
