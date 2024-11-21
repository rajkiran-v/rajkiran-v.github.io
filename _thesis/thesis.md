---
title:  "DS Interview Study Guide Part I: Statistics"
category: posts
date: 2019-08-24
excerpt: "Part I of my guide to data science interviews, focusing on statistics and experimental design."
---

As I have gone through a couple rounds of interviews for data scientist
positions, I've been compiling notes on what I consider to be the essential
areas of knowledge. I want to make these notes available to the general public;
although there are many blog posts out there that are supposed to help one
prepare for data science interviews, I haven't found any of them to be very
high-quality. 

From my perspective, there are four key subject areas that a data scientist
should feel comfortable with when going into an interview:

1. Statistics (including experimental design)
2. Machine Learning
3. Software Engineering (including SQL)
4. "Soft" Questions

I'm going to go through each of these individually. This first post will focus
on statistics. We will go over a number of topics in statistics in no particular
order. Note that **this post will not teach you statistics; it will remind you
of what you should already know.**

If you're utterly unfamiliar with the concepts I'm mentioning, I'd recommend [this
excellent MIT course on probability & statistics][1] as a good starting point. When I
began interviewing, I had never taken a statistics class before; I worked through the
notes, homeworks, and exams for this course, and at the end had a solid foundation to
learn the specific things that you need to know for these interviews. In my studying, I
also frequently use [cross-validated][16], a website for asking and answering questions
about statistics. It's good for in-depth discussions of subtle issues in
statistics. Finally, [Gelman's book][11] is the classic in Bayesian inference. If you
have recommendations for good books that cover frequentist statistics in a clear manner,
I'd love to hear them.

These are the notes that I put together in my studying, and I'm sure that there is
plenty of room for additions and corrections. I hope to improve this guide over time;
please let me know in the comments if there's something you think should be added,
removed, or changed!

# The Central Limit Theorem

The Central Limit Theorem is a fundamental tool in statistical analysis. It states
(roughly) that when you add up a bunch of independent and identically distributed random
variables (with finite variance) then their sum will converge to a Gaussian
distribution.[^fnote1]

How is this idea useful to a data scientist? Well, one place where we see a sum of
random variables is in a _sample mean_. One consequence of the central limit theorem is
that the sample mean of a variable with mean $$\mu$$ and variance $$\sigma^2$$ will
itself have mean $$\mu$$ and variance $$\sigma^2/n$$, where $$n$$ is the number of
samples.

I'd like to point out that this is pretty surprising. The distribution of the sum of two
random variables is not, in general, trivial to calculate. So it's kind of awesome that,
if we're adding up a large enough number of (independent and identically distributed)
random variables, then we _do_, in fact, have a very easy expression for the
(approximate) distribution of the sum. Even better, we don't need to know much of
anything about the distribution of we're sampling from, besides its mean and
variance - it's other moments, or general shape, don't matter for the CLT. 

As we will see below, the simplification that the CLT introduces is the basis of one of
the fundamental hypothesis tests that data scientists perform: testing equality of
sample means. For now, let's work through an example of the theorem itself.

## An Example

Suppose that we are sampling a Bernoulli random variable. This is a 0/1 random
variable that is 1 with probability $$p$$ and 0 with probability $$1-p$$. If we
get the sequence of ten draws $$[0,1,1,0,0,0,1,0,1,0]$$, then our sample mean is 

$$\hat \mu = \frac{1}{10}\sum_{i=1}^{10} x_i = 0.4$$

Of course, this sample mean is itself a random variable - when we report it, we
would like to report an estimate on its variance as well. The central limit
theorem tells us that this will, as $$n$$ increases, converge to a Gaussian
distribution. Since the mean of the Bernoulli random variable is $$p$$ and its
variance is $$p(1-p)$$, we know that the distribution of the sample mean will
converge to a Gaussian with mean $$p$$ and variance $$p(1-p)/n$$. So we could
say that our estimate of the parameter $$p$$ is 0.4 $$\pm$$ 0.155. Of course,
we're playing a bit loose here, since we're using the estimate $$\hat p$$ from
the data, as we don't actually know the _true_ parameter $$p$$.

Now, a sample size of $$n=10$$ is a bit small to be relying on a "large-$$n$$"
result like the CLT. Actually, in this case, we know the exact distribution of
the sample mean, since $$\sum_i x_i$$ is binomially distributed with parameters
$$p$$ and $$n$$. 

## Other Questions on the CLT

I find that the CLT more comes up as a piece of context in other questions
rather than as something that gets asked about directly, but you should be
prepared to answer the following questions.

- **What is the central limit theorem?** We've addressed this above - I doubt
  they'll be expecting a mathematically-correct statement of the theorem, but
  you should know the gist of it, along with significant limitations (finite
  variance being the major one).

- **When can you _not_ use the CLT?** I think the key thing here is that you
  have to be normalizing the data in an appropriate way (dividing by the sample
  size), and that the underlying variance must be finite. The answer here can
  get very subtle and mathematical, involving modes of convergence for random
  variables and all that, but I doubt they will push you to go there, unless
  you're applying for a job specifically as a statistician.
  
- **Give me an example of the CLT in use.** The classic example here is the
  distribution of the sample mean converging to a normal distribution as the
  number of samples grows large.

# Hypothesis Testing

Hypothesis testing (also known by the more verbose "null hypothesis significance
testing") is a huge subject, both in scope and importance. We use statistics to
quantitatively answer questions based on data, and (for better or for worse) null
hypothesis significance testing is one of the primary methods by which we construct
these answers. 

I won't cover the background of NHST here. It's well-covered in the MIT course; look at
[the readings][13] to find the relevant sections. Instead of covering the background,
we'll work through one exampleof a hypothesis test. It's simple, but it comes up all the
time in practice, so it's essential to know. I might go so far as to say that this is
the fundamental example of hypothesis testing in data science.

## An Example

Suppose we have two buttons, one green and one blue. We put them in front of
two different samples of users. For simplicity, let's say that each sample has
size $$n=100$$. We observe that $$k_\text{green}$$ 57 users click the green
button, and only $$k_\text{blue} = 48$$ click the blue button.

Seems like the green button is better, right? Well, we want to be able to say
how _confident_ we are of this fact. We'll do this in the language of null
hypothesis significance testing. As you should (hopefully) know, in order to do NHST, we
need a null hypothesis and a test statistic; we need to know the test statistic's
distribution (under the null hypothesis); and we need to know the probability of
observing a value "at least as extreme" as the observed value according to this
distribution.

I'm going to lay out a table of all the important factors here, and then discuss how we
use them to arrive at our $$p$$-value.

| Description | Value |
|------------|-------|
| Null Hypothesis| $$p_{blue} - p_{green} < 0$$ |
| Test Statistic | $$ \frac{k_\text{blue}}{n} - \frac{k_\text{green}}{n} $$ |
| Test Statistic's Distribution | $$N(0, (p_b(1-p_b) + p_g(1-p_g)) / n)$$ |
| Test Statistic's Observed Value | -0.09 | 
| $$p$$-value | 0.1003 |

There are a few noteworthy things here. First, we really want to know whether
$$p_g > p_b$$, but that's equivalent to $$p_b-p_g < 0$$. Second, we assume that
$$n$$ is large enough so that $$k/n$$ is approximately normally distributed,
with mean $$\mu = p$$ and variance $$\sigma^2 = p(1-p)/n$$. Third, since the
differences of two normals is itself a normal, the test statistic's distribution
is (under the null hypothesis) a normal with mean zero and the variance given
(which is the sum of the two variances of $$k_b/n$$ and $$k_g/n$$). 

Finally, we don't actually know $$p_b$$ or $$p_g$$, so we can't really compute
the $$p$$-value; what we do is we say that $$k_b/n$$ is "close enough"" to
$$p_b$$ and use it as an approximation. That gives us our final $$p$$-value.

The $$p$$-value was calculated in Python, as follows:

{% highlight python %}
from scipy.stats import norm
pb = 0.48
pg = 0.57
n = 100
sigma = np.sqrt((pb*(1-pb) + pg*(1-pg))/n)
norm.cdf(-0.09, loc = 0, scale = sigma) # 0.10034431272089045
{% endhighlight %}

Calculating the CDF of a normal at $$x=-0.09$$ tells us the probability that the test
statistic is less than or equal to $$-0.09$$, which is to say the probability that our
test statistic is at least as extreme as the observed value. This probability is
precisely our $$p$$-value.

So what's the conclusion? Well, often times a significance level is set before the test
is performed; if the $$p$$-value is not below this threshold, then the null hypothesis
is not rejected. Suppose we had set a significance level of 0.05 before the test began -
then, with this data, we would not be able to reject the null hypothesis, which is that
the buttons are equally appealing to users.

Phew! I went through that pretty quick, but if you can't follow the gist of what
I was doing there, I'd recommend you think through it until it is clear to
you. You will be faced with more complicated situations in practice; it's
important that you begin by understanding the most simple situation inside out.

## Other Topics in Hypothesis Testing

Some important follow-up questions you should be able to answer:

- **What are Type I & II error? What is a situation where you would be more concerned
  with Type I error? Vice versa?** These are discussed [on Wikipedia][15]. Type I error
  is false-positive error. You might be very concerned with Type I error if you are
  interviewing job candidates; it is very costly to hire the wrong person for the job,
  so you really want to avoid false positives. Type II error is false-negative error. If
  you are testing for a disease that is deadly but has a simple cure, then you would
  certainly NOT want to have a false negative result of the test, since that would
  result in an easily-avoidable negative outcome.

- **What is the _power_ of a test? How do you calculate it?** The power of a test is the
  probability that you will reject the null hypothesis, given an alternative
  hypothesis. Therefore, to calculate the power, you need an alternative hypothesis; in
  the example above, this would look like $$p_b-p_g = -0.1$$. Although these alternative
  hypothesis are often somewhat ad-hoc, the power analysis depends critically upon
  them. Google will turn up plenty of videos and tutorials on calculating the power of a
  test.

- **What is the significance of a test?** This is the same as the
  $$p$$-value threshold below which we reject the null
  hypothesis. (In)famously, 0.05 has become the de-facto standard throughout
  many sciences for significance levels worthy of publication.

- **Gow would you explain a p-value to a lay person**? Of course, you should
  have a solid understanding of the statistical definition of the
  $$p$$-value. A generally accepted answer is "a $$p$$-value quantifies the
  evidence for a hypothesis - closer to zero means more evidence." Of course,
  this is wrong on a lot of levels - it's actually quantifying evidence
  _against_ the null hypothesis, not _for_ the alternative. For what it's
  worth, I'm not convinced there's a great answer to that one; it's an
  inherently technical quantity that is frequently misrepresented and abused by
  people trying to (falsely) simplify its meaning.

- **If you measure many different test statistics, and get a $$p$$-value for each (all
  based on the same null hypothesis), how do you combine them to get an aggregate
  $$p$$-value?** This one is more of a bonus question, but it's worth knowing. It's
  actually not obvious how do to this, and the true $$p$$-value depends on how the tests
  depend on each other. However, you can get an upper-bound (worst-case estimate) on the
  aggregate $$p$$-value by adding together the different $$p$$-values. The validity of
  this bound results from the inclusion-exclusion principle.

# Confidence Intervals

Confidence intervals allow us to state a statistical result as a range, rather than a
single value. If we count that 150 out of 400 people sample randomly from a city
identify themselves as male, then our best estimate of the fraction of women in the city
is 250/400, or 5/8. But we only looked at 400 people, so it's reasonable to expect that
the true value might be a bit more or less than 5/8. Confidence intervals allow us to
quantify this width in a statistically rigorous way.

As per usual, we won't actually introduce the concepts here - I'll refer you to the
[readings from the MIT course][13] for an introduction. We'll focus on working through
an example, and looking at some different approaches.

## The Exact Method

Suppose that we want to find a 95% confidence inverval on the female fraction in the
city discussed above. This corresponds to a significance level of $$\alpha/2$$. One way
to get the **exact confidence inverval** is to use the CDF of our test statistic, but
substitute in the observed parameter for the true parameter, and then invert it to find
where it hits $$\alpha/2$$ and $$1-\alpha/2$$. That is, we need to find the value
$$p_l$$ that solves the equation

$$CDF\left(n, p_l\right) = \alpha/2$$

and the value $$p_u$$ that solves the equation

$$CDF\left(n, p_u\right) = 1 - \alpha/2.$$

In these, $$CDF(n,p)$$ is the cumulative distribution function of our test statistic,
assuming that the true value of $$p$$ is in fact the observed value $$\hat p$$. This is
a bit confusing, so it's worth clarifying. In our case, the sample statistic is the
sample mean of $$n$$ binomial random variables, so this CDF is the CDF of the sample
mean of $$n$$ binomial random variables with parameter $$5/8$$. Solving the two
equations above would give us our confidence inverval $$[p_l, p_u]$$.

It took me a bit of work to see that solving the above two equations would in fact give
us bounds that satisfy the definitions of a $$1-\alpha$$ confidence interval, which says
that, were we to run many experiments, we would find that the true value of $$p$$ would
fall between $$p_l$$ and $$p_u$$ with the probability

$$P\left(p_l\leq p \leq p_u\right) = 1-\alpha.$$

If you're into this sort of thing, I'd suggest you take some time thinking through why
inverting the CDF as above guarantees bounds $$[p_l, p_u]$$ that solve the above
equaiton. 

Although it is useful for theoretical analysis, I rarely use this method in
practice, because I often do not actually know the true CDF of the statistic
I am measuring. Sometimes I do know the true CDF, but even in such cases, the
next (approximate) method is generally sufficient.

## The Approximate Method

If your statistic can be phrased as a sum, then its distribution approaches a normal
distribution.[^fnote2] This means that you can solve the above equations for a normal
CDF rather than the true CDF of the sum (in the case above, a binomial CDF).

How does this help? For a normal distribution, the solutions for the above equations to
find lower and upper bounds are well known. In particular, the inverval
$$[\mu-\sigma,\mu+\sigma]$$, also called a $$1\sigma$$-interval, covers about 68% of the
mass (probability) of the normal PDF, so if we wanted to find a confidence interval of
level $$0.68$$, then we know to use the bounds $$(\overline x-\sigma, \overline
x+\sigma)$$, where $$\overline x$$ is our estimate of the true mean $$\mu$$. 

This sort of result is very powerful, because it saves us from having to do any
inversion by hand. A table below indicates the probability mass contained in various
symmetric intervals on a normal distribution:

| Inverval | Width[^fnote3] | Coverage |
|-------|----| ----|
| $$[\mu-\sigma,\mu+\sigma]$$ | $$1\sigma$$ | 0.683 |
| $$[\mu-2\sigma,\mu+2\sigma]$$ | $$2\sigma$$ | 0.954 |
| $$[\mu-3\sigma,\mu+3\sigma]$$ | $$3\sigma$$ | 0.997 |

Let's think through how we would use this in the above example, where we give a
confidence interval on our estimate of the binomial parameter $$p$$.

A binomial distribution has mean $$\mu=np$$ and variance $$\sigma^2=np(1-p)$$. Since
the sample statistical $$\hat p$$ is just the binomial divided by $$n$$, it has mean
$$\mu=p$$ and variance $$\sigma^2 = p(1-p)/n$$. The central limit theorem tells us that
the distribution of $$\hat p$$ will converge to a normal with just these parameters.

Suppose we want an (approximate) 95% confidence interval on the percentage of women in
the population of our city; the table above tells us we can just do a two-sigma
interval. (This is not _exactly_ a 95% confidence interval; it's a bit over, as we see
in the table above). The parameter $$\hat p$$ has mean $$\mu= p$$ and variance
$$\sigma^2 = p(1-p)/n$$.[^fnote4] In our case, $$\hat p=5/8$$, so our confidence
interval is $$5/8 \pm 15/1280 \approx 0.625 \pm 0.0117$$. Note that we approximated
$$p$$ with our experimental value $$\hat p$$; the theoretical framework that allows us
to do this substitution is beyond the scope of this article, but is nicely covered in
the MIT readings (Reading 22, in particular).

## The Bootstrap Method

The previous approach relies on the accuracy of approximating our statistic's
distribution by a normal distribution. Bootstrapping is a pragmatic, flexible
approach to calculating confidence intervals, which makes no assumptions on the
underlying statistics we are calculating. We'll go into more detail on
bootstrapping in general below, so we'll be pretty brief here.

The basic idea is to repeatedly pull 400 samples _with replacement_ from the sampled
data. For each set of 400 samples, we get an estimate $$\hat p$$, and thus can build an
empirical distribution on $$\hat p$$. Of course, the CLT indicates that this empirical
distribution should look a lot like a gaussian distribution with mean $$\mu= p$$ and variance
$$\sigma^2 = p(1-p)/n$$..

Once you have bootstrapped an empirical distribution for your statistic of interest (in
the example above, this is the percentage of the population that is women), then you can
simply find the $$\alpha/2$$ and $$1-\alpha/2$$ percentiles, which then become your
confidence interval. Although in this case our empirical distribution is (approximately)
normal, it's worth realizing that we can reasonably calculate percentiles _regardless_
of what the empirical distribution is; this is why bootstrapping confidence intervals
are so flexible.

As you'll see below, the downside of bootstrapping confidence intervals is that
it requires some computation. The amount of computation required can be
anywhere from trivial to daunting, depending on how many samples you want in
your empirical distribution. Another downside is that their statistical interpretation
is not exactly in alignment with the definition of a confidence interval, but I'll leave
the consideration of that as an exercise for the reader.[^fnotez] [One of the MIT
readings][17] has an in-dpeth discussion of confidence intervals generated via the
bootstrap method.

**Overall, I would recommend using the approximate method when you have good reason to
believe your sample statistic is approximately normal, or bootstrapping otherwise.** Of
course, the central limit theorem can provide some guarantees about the asympototic
distribution of certain statistics, so it's worth thinking through whether that applies
to your situations.


## Other Topics in Confidence Intervals

- **What is the definition of a confidence interval?** This is a bit more technical, but
  it's essential to know that it is **not** "there is a 95% probability that the true
  parameter is in this range." Actually, what it means is that "if you reran the
  experiment many times, then 95% of the time, the true value of the parameter you're
  estimating would fall in this range." It's worth noting that the *range* is the random
  variable here - the parameter itself (the true percentage of the population that
  identifies as female, in our example) is fixed.
  
- **How would this change if you wanted a _one-sided_ confidence interval?**
  This one isn't too bad - you just solve either $$CDF(n,p_l) = \alpha$$ or
  $$CDF(n,p_u) = 1-\alpha$$ for a lower- or upper-bounded interval,
  respectively.
  
- **What is the relationship between confidence intervals and hypothesis testing?**
  There are many ways to answer this question; it's a good one to ponder in order to get
  a deeper understanding of the two topics. One connection is the relationship between
  confidence intervals and rejection regions in NHST - Reading 22 in the MIT course
  addresses this one nicely. 

# Bootstrapping

 Bootstrapping is a technique that allows you to get insight into the quality of your
estimates, based only on the data you have. It's a key tool in a data scientist's
toolbag, because we frequently don't have a clear theoretical understanding of our
statistics, and yet we want to provide uncertainty estimates. To understand how it
works, let's look through an example.

In the last section, we sampled 400 people in an effort to understand what percentage of
a city's population identified as female. Since 250 of them identified themselves as
female, our estimate of the raio for the total population is $$5/8$$. This estimate it
itself a random variable; if we had sampled different people, we might have ended up
with a different number. What if we want to know the distribution of this estimate? How
would we go about getting that?

Well, the obvious way is to go out and sample 400 more people, and repeat this over and
over again, until we have many such fractional estimates. But what if we don't have
access to sampling more people? The natural thing is to think that we're out of luck -
without the ability to sample further, we can't actually understand more about the
distribution of our parameter (ignoring, for the moment, that we have lots of
theoretical knowledge about it via the CLT).

The idea behind bootstrapping is simple. Sample from the data you already have, with
replacement, a new sample of 400 people. This will give you an estimate of the female
fraction that is distinct from your original estimate, due to the replacement in your
sampling. You can repeat this process as many times as you like; you will then get an
empirical distribution whic approaches the true distribution of the statistic.[^fnote4]

Bootstrapping has the advantage of belig flexible, although it does have its
limitations. Rather than get too far into the weeds, I'll just point you to the
[Wikipedia article on bootstrapping][2]. There are also tons of resources about this
subject online. Try coding it up for yourself! By the time you're interviewing, you
should be able to write a bootstrapping algorithm quite easily.

[Machine Learning Mastery][9] has a good introduction to bootstrapping that uses the
scikit-learn API. [Towards Data Science][10] codes it up directly in NumPy, which is a
useful thing to know how to be able to do. Asking someone to code up a bootstrapping
function would be an entirely reasonable interview questions, so it's something you
should be comfortable doing.

## Other Topics in Bootstrapping

- **When would you _not_ want to use bootstrapping?** It might not be feasible when it
  is very costly to calculate your sample statistic. To get accurate estimates you'll
  need to calculate your statistic thousands of times, so it might not be feasible if it
  takes minutes or hours to calculate a single sample. Also, it is often difficult to
  get strong theoretical guarantees about probabilities based on bootstrapping, so if
  you need a highly statistically rigorous approach, you might be better served with
  something more analytical. Finally, if you know the distribution of your statistic
  already (for example, you know from the CLT that it is normally distributed) then you
  can get better (more accurate) uncertainty estimates from an analytical approach.

# Linear Regression

Regression is the study of the relationship between variables; for example, we
might wish to know how the weight of a person relates to their height. _Linear_
regression assumes that your input (height, or $$h$$) and output (weight, or
$$w$$) variables are _linearly related_, with slope $$\beta_1$$, intercept
$$\beta_0$$, and noise $$\epsilon$$.

$$w = \beta_1\cdot h + \beta_0 + \epsilon.$$

A linear regression analysis helps the user discover the $$\beta$$s in the
above equation. This is just the simplest application of LR; in reality, it is
quite flexible and can be used in a number of scenarios. 

Linear regression is another large topic that I can't really do justice to in this
article. Instead, I'll just go through some of the common topics, and introduce the
questions you should be able to address. As is the case with most of these topics, you
can look at the [MIT Statistics & Probability course][1] for a solid academic
introduction to the subject. You can also dig through [the Wikipedia article][3] to get
a more in-depth picture. The subject is so huge, and there's so much to learn about it,
that you really can spend as much time as you want digging into it - I'm just going to
gesture at some of the simpler aspects of it.

## Calculating a Linear Regression

Rather than go through an example here, I'll just refer you to the many available guides
that show you how to do this in code. Of course, you could do it in raw NumPy, solving
the normal equations explicitly, but I'd recommend using scikit-learn or statsmodels, as
they have much nicer interfaces, and give you all sorts of additional information about
your model ($$r^2$$, $$p$$-value, etc.)

[Real Python][7] has a good guide to coding this up - see the section "Simple Linear
Regression with scikit-learn." [GeeksForGeeks][8] does the solution in raw NumPy; the
equations won't be meaningful for you until you read up on the normal equation and how
to analytically solve for the optimal LR coefficients. If you want something similar in
R, or Julia, or MATLAB,[^fnoted] then I'm sure it's out there, you'll just have to go do
some Googling to find it.

## A Statistical View 

This subject straddles the boundary between statistics and machine-learning. It has been
quite thoroughly studied from a statistical point of view, and there are some iportant
results that you should be familiar with when thinking about linear regression from a
statistical frame.[^fnotec]

Let's look back at our foundational model for linear regression. LR assumes
that your input $$x$$ and output $$y$$ are related via 

$$y_i = \beta_1\cdot x_i + \beta_0 + \epsilon_i,$$

where $$\epsilon_i$$ are i.i.d., distributed as $$N(0, \sigma^2)$$. Since the
$$\epsilon$$ are random variables, the $$\beta_j$$ are themselves random
variables. One important question is whether there is, in fact, any
relationship between our variables at all. If there is not, then we should
$$\beta_1$$ close to 0,[^fnoteb] but they will not ever be exactly zero. One important
statistical technique in LR is **doing a hypothesis test against the null
hypothesis that $$\beta_1 = 0$$**. When a package like scikit-learn returns a
"$$p$$-value of the regression", this is the $$p$$-value they are talking
about.

Like I said before, there is a lot more to know about the statistics of linear
regression than just what I've said here. You can learn more about the statistics of LR
by looking at the [MIT course notes on the subject][6], or by digging through your
favorite undergraduate statistics book - most of them should have sections covering it.

## Validating Your Model

Once you've calculated your LR, you'd like to validate it. This is very important to
do - if you're asked to calculate a linear regression in an interview, you should always
go through the process of validating it after you've done the calculation.

I'd generally go through the following steps:

- If it's just a simple (one independent variable) linear regression, then plot the two
  variables. This should give you a good sense of whether it's a good idea to use linear
  regression in the first place. If you have multiple independent variables, you can
  make separate plots for each one.
- Look at your $$r^2$$ value. Is it reasonably large? Remember, closer to 1 is
  better. If it's small, then doing a linear regression hasn't helped much. 
- You can look at the $$p$$-value to see if it's difference from zero is
  statistically significant (see the section below). Also, you can have a very
  significant $$p$$-value while still having a low $$r^2$$, so be cautious in your
  interpretation of this one.
- You can also look at the RMSE of your model, but this number is not scaled between 0
  and 1, so a "good" RMSE is highly dependent on the units of your indepedent variable.
- Plot your residuals, for each variable. The residual is just the input minus
  the value predicted by your model, a.k.a. the error of your model. Plotting
  each residual isn't really feasible if you have hundreds of independent
  variables, but it's a good idea if your data is small enough. You should be
  looking for "homoskedasticity" - that the variance of the error is uniform
  across the range of the independent variable. If it's not, then certain
  things you've calculated (for example, the $$p$$-value of your regression)
  are no longer valid. You might also see that your errors have a bias that
  changes as the $$x_i$$ changes; this means that there's some more complicated
  relationship between $$y$$ and $$x_i$$ that your regression did not pick up.
  
Some of the questions below address the assumptions of linear regression; you
should be familiar with them, and now how to test for them either before or
after the regression is performed, so that you can be confident that your model
is valid.

## Basic Questions on LR

Hopefully you've familiarized yourself with the basic ideas behind linear
regression. Here are some conceptual questions you should be able to answer.

- **How are the $$\beta$$s calculated?** Practically, you let the library
  you're using take care of this. But behind the scenes, generally it's solving
  the so-called "normal equations", which give you the optimal (highest
  $$r^2$$) parameters possible.  You can use gradient descent to approximate
  the optimal solution when the design matrix is too large to invert; this is
  available via the `SGDRegressor` model in scikit-learn.
  
- **How do you decide if you should use linear regression?** The best case is
  when the data is 2- or 3-dimensional; then you can just plot the data and see
  if it looks like "linear plus noise". However, if you have lots of
  independent variables, this isn't really an option. In such a case, you
  should look perform a linear regression analysis, and then look at the errors
  to verify that they look normally distributed and homoskedastic (constant
  variance).

- **What does the $$r^2$$ value of a regression indicate?** The $$r^2$$ value
  indicates "how much of the variance of the output data is explained by the
  regression." That is, your output data $$y$$ has some (sample) variance, just
  on its own. Once you discover the linear relationship and subtract it off,
  then the remaining error $$y - \beta_0 - \beta_1x$$ still has some variance,
  but hopefully it's lower - $$r^2$$ is one minus the ratio of the original to
  the remaining variance. When $$r^2=1$$, then your line is a perfect fit of
  the data, and there is no remaining error. It is often used to explain the
  "quality" of your fit, although this can be a bit treacherous - see
  [Anscombe's Quartet][5] for examples of very different situations with the
  same $$r^2$$ value.
  
- **What are the assumptions you make when doing a linear regression?** The
  Wikipedia article [addresses this point][4] quite thoroughly. This is worth
  knowing, because you don't just want to jump in and blindly do LR; you want
  to be sure it's actually a reasonable approach.
  
- **When is it a bad idea to do LR?** When you do linear regression, you're assuming a
  certain relationship between your variables. Just the parameters and output of your
  regression won't tell you whether the data really are appropriate for a linear
  model. [Anscombe's Quartet][5] is a particularly striking example of how the output of
  a linear regression analysis can look similar but in fact the quality of the analysis
  can be radically different. Beyond this, it is a bad idea to do LR whenever the
  assumptions of LR are violated by the data; see the above bullet for more info there.
  
- **Can you do linear regression on a nonlinear relationship?** In many cases,
  yes. What we need is for the model to be linear in the parameters $$\beta$$;
  if, for example, you are comparing distance and time for a constantly
  accelerating object $$d = 1/2at^2$$, and you want to do regression to
  discover the acceleration $$a$$, then you can just use $$t^2$$ as your
  independent variable. The model relating $$d$$ and $$t^2$$ is linear in the
  acceleration $$a$$, as required.
  
- **What does the "linear" in linear regression refer to?** This one might seem
  trivial, but it's a bit of a trick question; the relationship $$y =
  2\log(x)$$ might not appear linear, but in fact it can be obtained via a
  linear regression, by using $$\log(x)$$ as the input variables, rather than
  $$x$$. Of course, for this to work, you need to know ahead of time that you
  want to compare against $$\log(x)$$, but this can be discovered via
  trial-and-error, to some extent. So the "linear" _does_, as you'd expect,
  mean that the relationship between independent and dependent variable is
  linear, but you can always _change_ either of them and re-calculate your
  regression. 


## Handling Overfitting

Overfitting is a very important to understand, and is a fundamental challenge in machine
learning and modeling. I'm not going to go into great detail on it here; more
information will be presented in the machine learning section of the guide. There are
some techniques for handling it that are particular to LR, which is what I'll talk about
here. 

[RealPython][7] has good images showing examples of over-fitting. You can
handle it by building into your model a "penalty" on the $$\beta_i$$s; that is,
tell your model "I want low error, **and** I don't want large coefficients.**
The balance of these preferences is determined by a parameter, often denoted by
$$\lambda$$. 

Since you have many $$\beta$$s, in general, you have to combine them in some
fashion. Two such ways to calculate the measure of "overall badness" (which I'll call
$$OB$$) are

$$OB = \sqrt{ \beta_1^2 + \beta_2^2 + \ldots + \beta_n^2 } $$

or 

$$OB = |\beta_1| + |\beta_2| + \ldots + |\beta_n|.$$

The first will tend to be emphasize outliers; that is, it is more sensitive to
single large $$\beta$$s. The second considers all the $$\beta$$s more
uniformly. If you use the first, it is called "ridge regression", and if you
use the second it is called "LASSO regression."

In mathematics, these denote the $$\ell_1$$ and $$\ell_2$$ norms of the vectors
of $$\beta$$s; you can in theory use $$\ell_p$$ norms for any $$p$$, even
$$p=0$$ (count the number of non-zero $$\beta$$s to get the overall badness) or
$$p=\infty$$ (take the largest $$\beta$$ as the overall badness). However, in
practice, LASSO and ridge regression are already implemented in common
packages, so it's easy to use them right out of the box.

As usual, there is a LOT to learn about how LASSO and ridge regression change your
output, and what kinds of problems they can address (and/or create). I'd highly
recommend searching around the internet to learn more about them if you aren't already
confident in your understanding of how they work. 

## Logistic Regression

Logistic regression is a way of modifying linear regression models to get a
classification model. The statistics of logistic regression are, generally speaking, not
as clean as those of linear regression. It will be covered in the machine learning
section, so we won't discuss it here.

# Bayesian Inference

Up until now this guide has primarily focused on frequentist topics in
statistics, such as hypothesis testing and the frequentist approach to
confidence intervals. There is an entire world of Bayesian statistical
inference, which differs significantly from the frequentist approach in both
philosophy and technique. I will only touch on the most basic application of
Bayesian reasoning in this guide. 

In this section, I will mostly defer to outside sources, who I think speak more
eloquently on the topic than I can. Some companies (such as Google, or so I'm told) tend
to focus on advanced Bayesian skills in their data science interviews; if you want to
really learn the Bayesian approach, I'd reccomend [Gelman's book][11], which is a
classic in the field.

## Bayesian vs Frequentist Statistics

It's worth being able to clearly discuss the difference in philosophy and approach
between the two schools of statistics. I particularly like the discussion in the MIT
course notes. They state, more or less, that while the Bayesians like to reason from
Bayes theorem

$$P(H|D) = \frac{ P(D|H)P(H)}{P(D)},$$

the frequentist school thinks that "the probability of the hypothesis" is a nonsense
concept - it is not a well-founded probablistic value, in the sense that there is no
repeatable experiment you can run in which to gather relative frequency counts and
calculate probabilities. Therefore, the frequentists must reason directly from
$$P(D|H)$$, the probability of the data given the hypothesis, which is just the
$$p$$-value. The upside of this is that the probabilistic interpretation of $$P(D|H)$$
is clean and unambiguous; the downside is that it is easy to misunderstand, since what
we really think we want is "the probability that the hypothesis is true."

If you want to know more about this, there are endless discussions of it all over the
internet. Like many such dichotomies (emacs vs. vim, overhand vs underhand toilet paper,
etc.) it is generally overblown - a working statistician should be familiar with, and
comfortable using, both frequentist _and_ Bayesian techniques in their analysis.

## Basics of Bayes Theorem

Bayes theorem tells us how to update our belief in light of new evidence. You
should be comfortably applying Bayes theorem in order to answer basic
probability questions. The classic example is the "base rate fallacy": 

Consider a routine screening test for a disease. Suppose the frequency of the
disease in the population (base rate) is 0.5%. The test is highly accurate with
a 5% false positive rate and a 10% false negative rate. You take the test and
it comes back positive. What is the probability that you have the disease?

The answer is NOT 0.95, even though the test has a 5% false positive rate. You should be
able to clearly work through this problem, building probability tables and using Bayes
theorem to calculate the final answer. The problem is worked through in the [MIT stats
course readings][12] (see Example 10), so I'll defer to them for the details.

## Updating Posteriors & Conjugate Priors

The above approach of calculating out all the probabilites by hand works reasonbly well
when there are only a few possible outcomes in the probability space, but it doesn't
scale well to large (discrete) probability spaces, and won't work at all in continuous
probability spaces. In such situations, you're still fundamentally relying on Bayes
theorem, but the way it is applied looks quite different - you end up using sums and
integrals to calculate the relevant terms.

Again, I'll defer to the [MIT stats course readings][13] for the details - readings 12
and 13 are the relevant ones here.

It's particularly useful to be familiar with the concept of **conjugate
priors**. In general, updating your priors involves computing an integral,
which as anyone who has taken calculus knows can be a pain in the ass. When
sampling from a distribution and estimating the parameters, there are certain
priors for which the updates based on successive samples work out to be very
simple.

For an example of this, suppose you're flipping a biased coin and trying to
figure out the bias. This is equivalent to sampling a binomial distribution and
trying to estimate the parameter $$p$$. If your prior is uniform (flat across
the interval $$[0,1]$$), then after $$N$$ flips, $$k$$ of which come up heads,
your posterior probability density on $$p$$ will be

$$f(p) \propto p^{k}((1-p)^{N-k}.$$

This is called a **$$\beta$$ distribution**. It is kind of magical that we can
calculate this without having to do any integrals - this is because the
$$\beta$$ distribution is "conjugate to" the binomial distribution. It's
important that we started out with a uniform distribution as our prior - if we
had chosen an arbitrary prior, the algebra might not have worked out as
nicely. In particular, if we start with a non-$$\beta$$ prior, then this trick
won't work, because our prior will not be conjuage to the binomial distribution.

The other important conjugate pair to know is that of the Gaussian
distribution; it is, in fact, conjuage to itself, so if you estimate the
parameters of a normal distribution, those estimates are themselves normal, and
updating your belief about the parameters based on new draws from the normal
distribution is as simple as doing some algebra.

There are many good resources available online and in textbooks discussing
conjuage priors; [Wikipedia][14] is a good place to start.

# Maximum Likelihood Estimation

We discussed before the case where you have a bunch of survey data, and want to estimate
the proportion of the population that identifies as female. Statistically speaking,
this proportion is a _parameter_ of the probability distribution over gender identity in
the that geographical region. We've intuitively been saying that if we see 250 out of
400 respond that they are female, then our best estimate of the proportion is 5/8. Let's
get a little more formal about why exactly this is our best estimate.

First of all, I'm going to consider a simplified world in which there are only two
genders, male and female. I do this to simplify the statistics, not because it is an
accurate model of the world. In this world, if the _true_ fraction of the population
that identifies as female is 0.6, then there is some non-zero probability that you would
draw a sample of 400 people in which 250 identify as female. We call this the
_likelihood_ of the parameter 0.6. In particular, the binomial distribution tells us
that

$$\mathcal{L}(0.6|n_\text{female}=250) =  {400 \choose 250} \,0.6^{250}\, (1-0.6)^{400-250} $$

Of course, I could calculate this for any parameter in $$[0,1]$$; if I were very far
from 5/8, however, then this likelihood would be very small.

Now, a natural question to ask is "which parameter $$p$$ would give us the highest
likelihood?" That is, which parameter best fits our data? That is the
**maximum-likelihood estimate** of the parameter $$p$$. The actual calculation of that
maximum involves some calculus and a neat trick involving logarithms, but I'll refer the
reader [elsewhere][18] for those details. It's worth noting that the MLE is often our
intuitive "best guess" at the parameter; in this case, as you might anticipate,
$$p=5/8$$ maximizes the likelihood of seeing 250 people out of 400 identify as female.

I won't give any question here, because I honestly have not seen any in my searching
around. Even so, I think it's an important concept to be familiar with. Maximum
likelihood estimation often provides a theoretical foundation for our intuitive
estimates of parameters, and it's helpful to be able to justify yourself in this
framework. 

For example, if you're looking at samples from an exponential distribution, and you want
to identify the parameter $$\lambda$$, you might guess that since the mean of an
exponential random variable is $$\mu= 1/\lambda$$, a good guess would be $$\lambda
\approx 1/\overline x$$, where $$\overline x$$ is your sample mean. In fact you would be
correct, and this is the MLE for $$\lambda$$; you should be familiar with this way of
thinking about parameter estimation.

# Experimental Design

Last, but certainly not least, is the large subject of experimental design. This is a
more nebulous topic, and therefore harder to familiarize yourself with quickly, than the
others we've discussed so far. 

If we have some new feature, we might have reason to think it will be good to include in
our product. For example, Facebook rolled out a "stories" feature some time ago (I
honestly couldn't tell you what it does, but it's some thing that sits on the top of
your newsfeed). However, before they expose this to all their users, they want to put it
out there "in the wild" and see how it performs. So, they run an experiment.

Designing this experiment in a valid way is essential to getting meaningful, informative
results. An interview question at Facebook might be: **How will you analyze if launching
stories is a good idea? What data would you look at?** The discussion of this question
could easily fill a full 45-minute interview session, as there are many nuances and
details to examine.

One basic approach would be to randomly show the "stories" feature to some people, and
not to others, and then see how it affects their behavior. This is an A/B test. Some
questions you should be thinking about are:

- **What metrics will we want to track in order ot measure the effect of stories?** For
  example, we might measure the time spent on the site, the number of clicks, etc.
- **How should we randomize the two groups?** Should we randomly choose every time someone
  visits the site whether to show them stories or not? Or should we make a choice for
  each _user_ and fix that choice? Generally, user-based randomization is preferable,
  although sometimes it's hard to do across devices (think about why this is).
- **How long should we run the tests? How many people should be in each group?** This
  decision is often based on a _power calculation_, which gives us the probability of
  rejecting the null hypothesis, given some alternative hypothesis. I personally am not
  a huge fan of these because the alternative hypothesis is usually quite ad-hoc, but it
  is the standard, so it's good to know how to do it. For example, you might demand that
  your test be large enough that if including stories increases site visit time by at
  least one minute, our A/B test will detect that with 90% probability. 
- **When can we stop the test?** The important thing to note here is that you **cannot**
  just stop the test once the results look good - you have to decide beforehand how long
  you want it to run.
- **How will you deal with confounding variables?** What if, due to some techincal
  difficulty, you end up mostly showing stories to users at a certain time of day, or in
  a certain geographical region? There are a variety of approaches here, and I won't get
  into the details, but it's essential that you be able to answer this concern clearly
  and thoroughly.

It's also worth considering scenarios where you have to analyze data after the fact in
order to perform "experiments"; sometimes you want to know (for example) if the color of
a product has affected how well it sold, and you want to do so using existing sales
data. What limitations might this impose? A key limitation is that of confounding
variables - perhaps the product in red mostly sold in certain geographic regions,
whereas the blue version sold better in other geographic regions. What impact will this
have on your analysis?

There are many other considerations to think about around experimental design. I don't
have any particular posts that I like; I'd recommend searching around Google to find
more information on the topic. 

If you have any friends that do statistics professionally, I'd suggest sketching our a
design for the above experiment and talking through it with them - the ability to think
through an experimental design is something that is best developed over years of
professional experience.

# Conclusion

This guide has focused on some of the basic aspects of statistics that get covered in
data science interviews. It is far from exhaustive - different companies focus on
different skills, and will therefore be asking you about different statistical concepts
and techniques. I haven't discussed time-dependent statistics at all - Markov chains,
time-series analysis, forecasting, and stochastic processes all might be of interest to
employers if they are relevant to the field of work. 

Please let me know if you have any corrections to what I've said here. I'm far
from a statistician, so I'm sure that I've made lots of small (and some large)
mistakes!

Stay tuned for the rest of the study guide, which should be appearing in the
coming months. And finally, best of luck with your job search! It can be a
challenging, and even demoralizing experience; just keep learning, and don't
let rejection get you down. Happy hunting!




<!-------------------------------- FOOTER ----------------------------> 


[1]: https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/index.htm

[2]: https://en.wikipedia.org/wiki/Bootstrapping_(statistics)

[3]: https://en.wikipedia.org/wiki/Linear_regression

[4]: https://en.wikipedia.org/wiki/Linear_regression#Assumptions

[5]: https://en.wikipedia.org/wiki/Anscombe%27s_quartet

[6]: https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading25.pdf

[7]: https://realpython.com/linear-regression-in-python/

[8]: https://www.geeksforgeeks.org/linear-regression-python-implementation/

[9]: https://machinelearningmastery.com/a-gentle-introduction-to-the-bootstrap-method/

[10]: https://towardsdatascience.com/an-introduction-to-the-bootstrap-method-58bcb51b4d60

[11]: https://www.goodreads.com/book/show/619590.Bayesian_Data_Analysis

[12]: https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading3.pdf

[13]: https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/

[14]: https://en.wikipedia.org/wiki/Conjugate_prior

[15]: https://en.wikipedia.org/wiki/Type_I_and_type_II_errors#Type_I_error

[16]: https://stats.stackexchange.com

[17]: https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading24.pdf

[18]: https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading10b.pdf

[^fnote1]: Of course, the actual statement is careful about the mode of
    convergence, and the fact that it is actually an appropriately-normalized
    version of the distribution that converges, and so on.
    
[^fnotea]: If this feels familiar, it's because it's (statistically speaking)
    the same problem we worked with in the section on hypothesis testing.
    
[^fnote2]: Again, we're being loose here - it has to have finite variance, and
    the convergence is only in a specific sense.
    
[^fnote3]: I'm being a little loose with definitions here - the width of a
    $$2\sigma$$ inverval is actually $$4\sigma$$, but I think most would still
    describe it using the phrase "two-sigma".
    
[^fnote4]: As usual, we're being a bit sloppy - we're just using the sample variance in
    place of the true variance and pretending this is correct. This will work if the
    number of samples $$n$$ is large. If you need confidence intervals with few (say,
    less than 15) samples, I recommend you look into confidence intervals based on the
    student-t distribution.

[^fnotec]: Some of the issues that arise here (for example, over- and
    under-fitting) have solutions that are more practical and less theoretical and
    statistical in nature - these will be covered in more depth in the machine
    learning portion of this guide, and so we don't go into too much detail in this
    section.
    
[^fnoteb]: $$\beta_0$$ just represents the difference in the mean of the two
    variables, so it could be non-zero even if the two are independent.
    
[^fnoted]: Why are you using MATLAB? Stop that. You're not in school anymore.

[^fnotez]: In doing bootstrapping, we're really trying to find the distribution of our
    statistic $$\hat S$$. So, what we find via this method are bounds $$(l,u)$$ such that
    $$P(l\leq \hat S \leq u)\geq C$$. How does this relate to the definition of a
    confidence interval? This is a somewhat theoretic exercise, but can be helpful in
    clarifying your understanding of the more technical aspects of confidence interval
    computation. 
