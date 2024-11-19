---
title: "Human-in-the-Loop ML"
excerpt: "A powerful paradigm for ML systems"
date: 2023-08-08
categories: 
- post
tags: 
---
It's interesting to look back and think about what I've learned since I started working
as a data scientist back in 2016. In school we're taught about various algorithms and
models, their mathematical properties, and some common use-cases. What we're not taught
is the nuances of actually turning these models into viable products.

My experience in industry has shown me that it can be essential to have a **human in the
loop** for many ML products. In this post, I'll discuss when you do and don't need to use
human-in-the-loop ML, how humans can be incorporated ML systems, and the pros and cons
of such an approach.


# When should you use a human in the loop?

Augmenting your machine systems with human input can have dramatic strengths, but also
significant drawbacks. It can often change the properties of your system (e.g. speed,
accuracy, explainability, cost) by orders of magnitude, so it merits careful
consideration of the pros and cons.

One dimension to consider is the **importance of accuracy** in your model. Serving ads and
recommending content on social media need not be high-accuracy - if even 80% of what you
recommend is relevant, the model can still have a strong positive impact on the
product. Contrast this with applications like driverless cars. In these cases, system
"misses" carry a very high cost, and so having human intervention as an option (or
necessity) is often helpful.

Another important consideration is **cost and scale**. Involving humans in a process is much
more costly than a raw machine learning model. It is also much harder to scale; building
out a large network of laborers involves complicated financial, logistical, and legal
considerations. This is more of a concern for organizations trying to go from 1 to 100,
rather than 0 to 1, which operate on a smaller more manageable scale. This suggests a
bootstrapping strategy I will discuss later on.

Of course, adding a human into an automated system will have dramatic impacts on
**latency**. A low-latency machine-learning system can return results in milliseconds; most
human-based systems will have SLAs[^fn1] on the order of hours. For applications like online
ad serving, which must entirely run within the time a webpage loads, human intervention
is therefore a non-starter. However, for applications like content moderation, where
accuracy trumps latency concerns, human intervention can be very useful. A hybrid
approach can also address this concern; a system where the initial recommendation is
provided and acted upon quickly, and human "review" of the action is then provided
within the much-slower SLA.

Finally, there is the more fuzzy notion of **explainability and perception**. If your model
is customer-facing and there is a high cost for individual model failures (e.g. medical
diagnosis models), then the customer will often demand an explanation for why a certain
failure occured. The same holds true for some cybersecurity applications; if an attack
passes through, and a customer demands an explanation, human filtering can prevent
embarassing situations where the output of the ML system seems obviously wrong to a
human, but we cannot explain why the system made the judgement it did. (This can be more
than embarassing; a handful of such incidents can be enough to drive away customers and
hurt the market's perception of the product.)[^fn2]


# How do you incorporate humans into your ML system?

There are a few different approaches to incorporating humans into an ML system, which
each have their own advantages and disadvantages.


## Human confirmation before output

One approach is to have an automated system that makes a suggestion to a human agent,
who then confirms or modifies the suggestion *before* it is sent to the user. This is the
approach used by Stitch Fix. Their recommendation engine sends recommendations to the
stylists, who have final say in selecting what clothing is sent to the customer.

This approach is the "strongest" in terms of human intervention. Each output will be
vetted by a human, and therefore the system will achieve maximum accuracy, but will have
much slower output time and higher cost. Such an approach will only be useful for
systems where the human-in-the-loop is a characteristic aspect of the product
(e.g. Stitch Fix) since the approach leads to a system that behaves quite differently
from a fully automated ML system.


## Human verification after output

A alternative is to have an ML system that generates outputs that are sent to the user,
and then verified by human agents some time later. For example, YouTube might run a
system that detects whether a video should be removed from the site.[^fn3] It can
immediately remove the video based on the system's output, and then later have the video
undergo human review. This review could result in the video being reinstated if it is
determined to be appropriate.

This system has the benefit that the automated outputs are available immediately; in the
example above, videos deemed problematic by the model are removed within seconds.  The
downside is that there is a time gap where the user is exposed to the system's
(potentially erroneous) output. In the example above, there may be a few hours time
where a video is removed from the platform in error. That said, if your base model is
reasonably accurate, then such system misses become more rare and such an approach can
be very fruitful.

Most thoughtful system architects will employ *partial* verification. For example, in the
above example, if the model outputs >99.5% probability that a video should be removed,
then that video might not undergo human review; if the model outputs between 80% and
99.5% probability, the video would undergo review.

Partial verification is tuneable; one can cost by lowering the upper threshold (more
videos skip review) or increase recall by lowering the lower threshold (more borderline
videos get reviewed).


## Product Bootstrapping

Another partial approach is to use human intervention to bootstrap your organization. A
small startup with a good idea for an ML product might not have enough data for a highly
accurate model. For such an organization, the benefits of a high-touch approach outweigh
the costs. As the organization scales, they can transition away from the
human-in-the-loop model and focus on (mostly) independent automated systems.

Of course, this transition is not a trivial matter. It can often be difficult to remove
human verification and maintain system accuracy. However, as I said above, scale gives
advantages to machine learning products, and these advantages may make it possible for
the system to be freestanding (or at least much closer to freestanding) than it was
initially.


# Oh, the humanity

Up until now, we've been discussing the impact of the humans on the system. It would be
remiss of me not to discuss the impact of the system on the humans. I don't have any
simple conclusions here, but it's worth at least raising a few points for consideration.

Working [below the API](https://rein.pk/replacing-middle-management-with-apis) can be challenging. The work is often hourly, with strict SLAs and
a focus on productivity metrics. This can generate a high-pressure environment.
Depending on the product area, hours can be irregular. The issues with the "[gig economy](https://www.nytimes.com/2023/04/13/magazine/gig-jobs-apps.html)"
have been widely discussed in the media, and many of those issues are shared by the kind
of systems we're describing here.

However, there are also many benefits. The work is often remote, and since it is hourly,
can be flexible and work with irregular schedules. Many of the stylists at Stitch Fix
are mothers that supplement the family income by styling part-time. Before such systems
existed, it would be very hard to find work that could be done from home in the three
hours between when the baby goes to sleep and when the mother does; ideally,
incorporating humans into ML systems can enable such work and be a win-win.


# Conclusion

Going from "we could solve this with ML!" to an actually-viable product is often[^fn4] a
bumpy road. Incorporating human feedback into an automated system is a key tool to help
ease this transition. I don't have any easy recommendations here; whether and how you
should incorporate human input into your particular product is highly dependent on the
product and the market in which it is situated.

But you should consider it. I've seen human augmentation assist ML companies at every
stage of growth, from pre-seed to post-IPO. It is a tool that, in my opinion, every
technology strategist should have in their toolkit.


<!----- Footnotes ----->

[^fn1]: [Service-level agreements](https://www.cio.com/article/274740/outsourcing-sla-definitions-and-solutions.html), which "defines the level of service expected by a customer from a supplier"; in this case, the "level of service" refers to the latency of a system.
[^fn2]: Even if a model is more accurate than the human-only alternative, explainability can still be an important psychological issue for customers. Consider a driverless car that has accident rates 1/10th those of an average driver; however, when it does crash, it does so seemingly at random. Public perception and adoption of such a product would (I predict) be poor, since when we are in such critical situations, we often rely on explanations to feel safe and in-control. Note that this may be less of an issue for internal-use models, where adoption can be decreed by management, and not driven by user perception.
[^fn3]: I'm not saying this is what YouTube actually does. This is just an example.
[^fn4]: Read: always.