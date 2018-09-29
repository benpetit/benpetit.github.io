---
layout: post
title: Recent advances in RL I: Imagination-augmented agents
---

# Recent advances in RL I: Imagination-augmented agents

The most impressive (relatively) recent steps in artificial intelligence (notably Deep-Q networks and deep policy gradients) definitely fell in the field of <em>model-free</em> reinforcement learning. What this means is that we do not need to know the dynamics of the MDP (that is to say the transition probabilities) to train an agent on it. This is useful, but also often leads to very poor data-efficiency.

On the contrary, a recent trend in RL is to return to some degree of model-basedness. In general, model-based methods such as value iteration are known to be much more data-efficient than model-free methods. What is new here is that the model does not simply evaluate the transition probabilities, but really <em>learns</em> the dynamics of the MDP, using a neural network. The major interest of this method is that it allows the agent to be trained not only on real MDP trajectories, but also on "hallucinated" or "imagined" rollouts generated using the dynamics model. It is very reminiscent of how we humans build a model of our environment. We will explain that in more details below.

In this post, I will comment and compare two seminal papers:
1. "<em>Imagination-Augmented Agents for Deep Reinforcement Learning</em>" (Weber, et al., 2017)
2. "<em>World Models</em>" (Ha and Schmidhuber, 2018)

### <em>Imagination-Augmented Agents for Deep Reinforcement Learning</em>: a summary



### References
