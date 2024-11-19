---
header:
  overlay_image: /assets/images/research/header.jpg
  caption: "Photo credit: [**Roman Mager**](https://www.unsplash.com)"
permalink: /portfolio/qm_stats/
date: 2018-01-16
toc: true
toc_label: "Contents"
---

# Statistical Analysis of Quantum Entanglement

*(This section is an overview of material which is available [on the arXiv][3].)*

Quantum mechanics is an inherently probabilistic theory of the physical
world. This is perhaps the fundamental weirdness of the theory, which made it so
hard to swallow for established giants of the field, such as Albert
Einstein.[^fnote1] Another aspect of quantum mechanics that made many physicists
uncomfortable is the phenomena of entanglement, in which actions taken upon one
particle in an entangled pair (including something as seemingly innocuous as
measuring the position of the particle) can effect the entangled partner
instantly, regardless of the separation between the two.[^fnote2] 

## Measuring Entanglement

For the first decades of quantum mechanics, the theory enjoyed immense success
in this quantitatively explaining phenomena which made no sense under the
old-fashioned "classical" theory. However, the so-called "action at a distance"
of entangled particles was not thought to be directly observable, and so it was
relegated to philosophical discussions. But then in the early 1960s, a physicist
named John Bell theorized a property of two entangled particles that one could
measure which would have two different values depending on whether the particles
were exhibiting action at a distance or not. A full explanation of [Bell's
theorem][1] is far beyond the scope of this article, but suffice to say that
entanglement was now measurable in principle. 

In the 1970s, experimentalists began conducting tests based upon Bell's theorem,
which are generally referred to as [Bell tests][2]. Refining these tests
continues to be an active area of research; the centrality of the issue being
tested means that it is important that all theoretical loopholes be closed. 

## Uncertainty in Measurement

Scientific experiments do not give a simple yes or no answer to the question at
hand; the uncertainty inherent in experimental design and implementation means
that we can gather evidence, sometimes overwhelming evidence, for a hypothesis,
but we can never be perfectly sure that it is correct. With an issue as central
as that being examined by Bell tests, the scientific community wishes to have an
extreme degree of certainty regarding the outcome of any given experiment. The
classic $$p$$-value bound of 0.05 is insufficient; physicists often look for
$$p$$-values of $$10^{-6}$$ and below in Bell tests. A [recent experiment][4]
even measured a $$p$$-value on the order of $$10^{-16}$$! 

Clearly, when making such drastic statistical claims, it is essential that the
statistical methodology involved in the calculation must be
sound. Unfortunately, many phsyicists employ statistical techniques which
careful mathematics prove to be invalid. In particular, suppose we are measuring
a quantity $$S$$, and we have a null hypothesis that $$S\leq 2$$ which we intend
to disprove. A scientists might measure many samples of $$S$$, and then
calculate a sample mean and standard deviation, and measure how many sample
deviations the mean is away from 2. If we measure $$S = 10 \pm 1$$, then we are
8 standard deviations away from the null hypothesis, which seems to be strong
evidence against the null hypothesis.

But *how strong* is this evidence? In introductory statistics courses, one often
learns $$p$$-values associated with a given number of standard deviations: one
standard deviation is a $$p$$-value of about 0.32, 2 standard deviations is
about $$p=0.05$$, and so on. This is implicitly assuming that the underlying
distribution is Gaussian, an assumption seemingly supported by the famous
central limit theorem. However, a mathematical subtlety in the CLT renders this
assumption invalid when one measures increasingly low $$p$$-values.[^fnote3]

## Building New Methods

This problem was the focus of my time working as a research assistant at the
National Institute of Standards and Technology. The group I joined had
previously developed a tool that provided a statistically valid method for
calculating $$p$$-values in Bell tests, and had proven some results regarding
the method's efficiency. The method is based on a statistical tool called **test
supermartingales**, and so I will use this term to refer to the method as a
whole from now on. I won't get in to an explanation of the method here;
it's quite technical, although those with an interest in probability and
statistics may find its simplicity quite surprising. A thorough description can
be found on the pre-print of our paper, which is [available on the arXiv][3].

In my year or so working with the group, we wrote a paper in which we perform a
thorough analysis of the efficacy of test supermartingales when applied to a
very simple situation, in which one is trying to determine the bias of an unfair
coin by observing a sequence of flips. The advantage of this setting is that the
probabilistic setting is simple enough that many of our quantities of interest
can be calculated directly; furthermore, for a given sequence of flips, we know
the *exact* $$p$$-value, and so can compare the test margtingale $$p$$-value to
this "optimal" method.

What we learn gives us optimism that our approach will be experimentally useful;
the method is shown to be very near optimal, but enjoys many advantages over
traditional methods. In particular, for most staitstical methods one must decide
beforehand how long to perform the experiment, and cannot simply wait until the
$$p$$-value is as low as desired. However, the use of test martingales allows
for so-called "arbitrary stopping," so that $$p$$-value monitoring does not
invalidate the calculated $$p$$-value.

Moving forward, we hope to see test martingales employed in the myriad new Bell
tests being performed each year. We also hope to continue to prove results on
the performance of the method in more complicated settings, so that we can
further bridge the gap between the tidy scenarios used for theoretical analysis,
and the messy, complicated situations in which these methods are actually
employed. 

<!------------------------------------ FOOTER -------------------------------->

[^fnote1]: Einstein famously declared that "God does not play dice with the
    universe," which is to say that no physical theory can be at its base
    probabilistic.

[^fnote2]: This seems to violate the laws of general relativity, which prohibit
    faster-than-light travel. This paradox is subtle, but at a gross level the
    resolution is that the random nature of the effects prevents one from
    sending any sort of information via the entangled pair.
	
[^fnote3]: Essentially, the issue is that convergence is guaranteed if one fixes
    the fraction of the distribution we are measuring. This type of convergence
    is called **convergence in distribution**. In Bell tests, however, the
    $$p$$-value gets lower as the experiment continues, which is to say we're
    moving further out into the tail of the Gaussian distribution, and so no
    such convergence guarantee exists. In fact, it can be shown that in many
    cases a Gaussian assumption gives wildly incorrect results.
	
[1]: https://en.wikipedia.org/wiki/Bell's_theorem

[2]: https://en.wikipedia.org/wiki/Bell_test_experiments

[3]: https://arxiv.org/abs/1709.04078

[4]: https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.119.010402
