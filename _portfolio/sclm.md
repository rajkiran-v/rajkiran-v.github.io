---
header:
  overlay_image: /assets/images/code.jpg
  caption: "Photo credit: [**Marcus Spiske**](https://unsplash.com)"
permalink: /portfolio/sclm/
date: 2018-03-16
toc: true
toc_label: "Contents"
---

# Smart Climate Indices

[Entelligent][1] currently publishes two indices, the SCLMX and SCLMK. These
indices reflect the performance of Entelligent's financial approach, which
leverages the predictive power of climate models to generate profitable market
insights.

## Design

The Smart Climate indices use machine learning to observe patterns and establish
connections between predictive climate models and financial markets. We
calculate each security's sensitivity to disruptive climate scenarios, and then
generate portfolios which are robust to variations in climate relative to the
benchmark.

Sadly, I can't give a much more detailed technical description than that without
violating the intellectual property of Entelligent. A core component of the
technological platform upon which the indices are built is a (heavily modified)
form of Markowitz optimization, also known as mean-variance optimization. The
library which underlies the index is written in Python. Scikit-learn, pandas,
and cvxpy (a convex optimization library) are all used extensively.

## 10-Year Backtest

<img src="{{ "/assets/images/scml.png" | absolute_url }}">

In order to test the efficacy of our approach, we have performed a ten-year
backtest, using the S&P 500 as a benchmark. This portfolio has a maximum weight
of 2.5% in any one security and does not turn over more than 15% of the whole
portfolio on any given rebalance period. The portfolio is rebalanced quarterly,
beginning in 2006 and running through the present. 

The Smart Climate index is less volatile than the benchmark during the financial
crisis of 2008, and shows higher performance as the market recovers and begins
to thrive. Performance is particularly strong between 2013 and 2015. An initial
investment of $10,000 in January 2006 would grow to over $26,000 if invested in
a portfolio using the optimization techniques used by Smart Climate, over $5000
more than a comparable investment in an ETF that tracks the S&P 500.

## Continuing Progress

Entelligent continues to develop and refine their methodology for portfolio
optimization and index generation. The future of financial analysis must
incorporate novel external data sources, if the analysis hopes to gain a
competitive advantage in the market. The Smart Climate indices are available on
Thompson Reuters and Bloomberg terminals, and are rebalanced quarterly.

[1]: https://entelligent.com
