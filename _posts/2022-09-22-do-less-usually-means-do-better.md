---
layout: post
title: Do less usually means do better
categories:
  - Programming
---

I've recently had a discussion with a fellow programmer friend about the features that they were currently implementing 
at his company. He told me that almost all were customer requested features, and that they didn't know how to handle 
the high volume because they would all **bring value**.

Of course, customer requests should be looked at and taken into consideration, but do they all **bring value**? 

The first question we should ask is: who determines what that value is? Is it actually quantifiable? Or is it only 
subjectively quantifiable, as in the customer sees the value for their sole use case? 

I believe that we, as programmers, should always take a step back and not take our mind off the intrinsic "luggage" such
features bring: increased mental overload, inherent complexity and, at the end of the day, stuff that needs to be 
maintained. They can be tackled, of course, but is it worth the burden? We should never stop thinking about this. 

Of course, if the feature request passes this "luggage check" stage, it should be taken into consideration. However,
I still don't think we should jump to the conclusion that it should be immediately taken to design and implementation as is.
The next question we should ask ourselves is: can we apply the [Pareto principle](https://en.wikipedia.org/wiki/Pareto_principle), 
which is - can we implement 80% of the feature with 20% of the effort? 

Last but not least, I think we should never forget that the number of customers you currently have is finite, while the 
number of customers you _could have_ is virtually infinite. Therefore, stop there for a second and ask a final question:
if new customers enter the platform, and they see your software for the first time, will they think it's overly complex?
Or will it be easy to set up, use and in the end they'll have a great experience?

We should never cede to ask the tough questions and analyze whether a request, either internal or from customers, is 
actually worth implementing because, most of the time, less software means better software.
