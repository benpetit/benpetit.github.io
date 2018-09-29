---
layout: post
title: Recent advances in RL I: Imagination-augmented agents
---

## Recent advances in RL I: Imagination-augmented agents

The most impressive (relatively) recent steps in artificial intelligence (notably Deep-Q networks and deep policy gradients) definitely fell in the field of <em>model-free</em> reinforcement learning. What this means is that we do not need to know the dynamics of the MDP (that is to say the transition probabilities) to train an agent on it. This is useful, but also often leads to very poor data-efficiency.

On the contrary, a recent trend in RL is to return to some degree of model-basedness. In general, model-based methods such as value iteration are known to be much more data-efficient than model-free methods. What is new here is that the model does not simply evaluate the transition probabilities, but really <em>learns</em> the dynamics of the MDP, using a neural network. The major interest of this method is that it allows the agent to be trained not only on real MDP trajectories, but also on "hallucinated" or "imagined" rollouts generated using the dynamics model. It is very reminiscent of how we humans build a model of our environment. We will explain that in more details below.

In this post, I will comment and compare the following two seminal papers:
1. "<em>Imagination-Augmented Agents for Deep Reinforcement Learning</em>" (Weber, et al., 2017)
2. "<em>World Models</em>" (Ha and Schmidhuber, 2018)

#### <em>Imagination-Augmented Agents for Deep Reinforcement Learning</em>: a summary

This paper introduces a model called "I2A", which stands for "Imagination-Augmented Agent". The model is made of the following elements:

1. An <strong>"imagination core"</strong>, which itself consists in two elements:
  1. A <em>policy network</em>, which parameterizes a policy $\hat{\pi}$.
  2. A environment model, which takes a state and an action, and returns an estimate of the reward and the following state.
2. A <strong>rollout encoder</strong>, which is basically an LSTM whose function is to encode a sequence of actions and states in a high-dimensional space.

#### References
