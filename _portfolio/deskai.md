---
header:
  overlay_image: /assets/images/code.jpg
  caption: "Photo credit: [**Marcus Spiske**](https://unsplash.com)"
permalink: /portfolio/deskai/
date: 2018-03-16
toc: true
toc_label: "Contents"
---

# DeskAI: Automated Campaign Management for Advertisers

The bulk of my time at [the Trade Desk][1] has been spent conceptualizing, testing,
and prototyping a machine learning algorithm which automates the process of
managing an online advertising campaign.

## Summary

The Trade Desk uses a linear model to compute bids for online advertisement
space auctions. Currently, professional traders adjust the coefficients of this
model based on their intuition, knowledge, and expertise. I build a platform
that connects this model to TensorFlow, and allows it to be automatically
trained in mini-batches, which can be updated as the campaign progresses.

We tested this approach on a campaign which posted advertisements for triathalon
coaching. Our automated approach outperformed a campaign using their current
optimization tool by a factor of almost two-to-one. We measure performance by
cost per click, and overall our campaign showed a CPC of $1.63, while the
control campaign had a CPC of $2.73.

Read on for more details.

## Background

Online advertising space is sold through a bidding process, which occurs as the
user loads a webpage. The bidders have access to information such as IP address,
the users browsing history (if the appropriate cookies have been enabled),
geographic location, site on which the ad will be presented, shape of available
space, and so on. The goal of the bidding process is to use this information to
evaluate whether a user presents a particularly valuable advertising
opportunity. 

Before the Trade Desk, most companies used what are called "line items"; these
are basically case statements, instructing the system to bid a certain amount if
a set of criteria are met; for example, "bid $3 if the user is from California
and has previous visited the page." The innovation of the Trade Desk is the
development of independent *bid adjustments*. Each factor (located in
California, site is yahoo.com, etc) has its own individual bid adjustment, and
the total bid is calculated by multiplying these adjustments together.

Currently, these adjustments are primarly manipulated by *traders*,
professionals whose job it is to manage and optimize campaigns. Some limited
statistical optimization also takes place, but the scope of this is quite
limited.

## Automation

The foundational realization behind DeskAI is that the Trade Desk is
fundamentally running a linear model. That is to say, they are
calculating bids in the same way that a logistic regressor would calculate odds;
by multiplying together individual odds from a variety of factors.

The concept of the Trade Desk's platform as a linear model raises a natural
question: What is the loss function associated with this platform? That is to
say, how is the performance of a given set of bids evaluated? Once such a loss
function is defined, then **this model can be trained.** This insight allows for
computation of optimial bid factors across a variety of fields (time, location,
site, device, etc.) very rapidly. 

DeskAI establishes a loss function, extracts training data, and trains the
linear model to establish optimal bid adjustments. The training (stochatic
gradient descent) is performed in a streaming fasion using TensorFlow. The
library which implements DeskAI is built in a modular, object-oriented fashion
to allow for simple implementation and flexibility.

## Testing

We tested the model with an advertising campaign that promoted triathalon
training. Our metric of success was user clicks on the advertisement shown. We
ran both an experimental campaign (with DeskAI enabled) and a control campaign
(in which the typical TTD campaign planning tool was used).

The campaign optimized by DeskAI achieved twice the click-through rate of the
control campaign. Note that this came at effectively no additional cost; the
cost-per-click of the DeskAI campaign was half that of the control. We also
observed that the DeskAI campaign won a large fraction of the spaces it bid on,
indicating that it is bidding in a strategic manner; DeskAI is only bidding on
space that is desirable, but when it does so it bids generously to ensure that
the space is secured.

## The Future

I look forward to watching this project grow and develop at the Trade
Desk. Automation is the way of the future, and we have established yet another
scenario in which it is highly efficient to employ machine learning techniques
to analyze and optimize performance.

[1]: http://www.thetradedesk.com
