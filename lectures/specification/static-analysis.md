---
layout: page
title: "Static Analysis"
excerpt: "Static Analysis"
tags: ["lectures"]
context: lectures
subcontext: specification
# image: 
#    feature: "lecture.jpg"
#    credit: Delft University of Technology
#    creditlink: http://repository.tudelft.nl/view/MMP/uuid%3Aa2f25709-c56e-453e-9394-4a05acf603a4/
---


## Slides

<iframe src="//www.slideshare.net/slideshow/embed_code/key/ybw1FZjOIoKg7y" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/eelcovisser/static-analysis-71062165" title="Static Analysis" target="_blank">Static Analysis</a> </strong> from <strong><a target="_blank" href="//www.slideshare.net/eelcovisser">Eelco Visser</a></strong> </div>

[PDF](https://github.com/TUDelft-IN4303-2016/lectures/blob/master/05b-static-semantics/Static%20Analysis.pdf)

## Further Reading

1.  Gabriël Konat, Lennart Kats, Guido Wachsmuth, and Eelco Visser (2013). [Declarative Name Binding and Scope Rules](http://repository.tudelft.nl/assets/uuid:ebf61531-bbdf-4bde-b991-7733d9ae4af4/TUD-SERG-2012-015.pdf). SLE 2012.
    
    This paper introduces the concepts of NaBL and explains them with C# as an example language.
  The syntax of NaBL has slightly changed over the last two years, but the core concepts are still the same.
  
2.  Guido Wachsmuth, Gabriël Konat, Vlad Vergu, Danny Groenewegen, and Eelco Visser (2013). [A language independent task engine for incremental name and type analysis](http://swerl.tudelft.nl/twiki/pub/Main/TechnicalReports/TUD-SERG-2013-014.pdf). SLE 2013.
    
    This paper looks behind the scenes and explains how incremental name and type analysis is realised in Spoofax.
  
3.  Eelco Visser, Guido Wachsmuth, Andrew Tolmach, Pierre Neron, Vlad Vergu, Augusto Passalaqua, and Gabriël Konat (2014). [A Language Designer's Workbench. A One-Stop-Shop for Implementation and Verification of Language Designs](http://web.cecs.pdx.edu/~apt/onward14.pdf). Onward! 2014.
    
    This paper promotes declarative language definitions as a single source for language implementation and verification.
  It presents a proof-of-concept language designer’s workbench that supports generation of IDEs, interpreters, and verification infrastructure from a single source. 
  This constitutes a first milestone on the way to a system that fully automates language implementation and verification.
