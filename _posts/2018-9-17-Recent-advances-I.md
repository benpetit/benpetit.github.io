---
layout: post
mathjax: true
title: "Recent advances in RL I: Imagination"
categories: [blog, Reinforcement learning]
tags: [blog, reinforcement learning, rl, AI, artificial intelligence, imagination, I2A, World models]
---

The most impressive (relatively) recent steps in artificial intelligence (notably Deep-Q networks and deep policy gradients) definitely fell in the field of <em>model-free</em> reinforcement learning. What this means is that we do not need to know the dynamics of the MDP (that is to say the transition probabilities) to train an agent on it. This is useful, but also often leads to very poor data-efficiency.

On the contrary, a recent trend in RL is to return to some degree of model-basedness. In general, model-based methods such as value iteration are known to be much more data-efficient than model-free methods. What is new here is that the model does not simply evaluate the transition probabilities, but really <em>learns</em> the dynamics of the MDP, using a neural network. The major interest of this method is that it allows the agent to be trained not only on real MDP trajectories, but also on "hallucinated" or "imagined" rollouts generated using the dynamics model. It is very reminiscent of how we humans build a mental model of our environment. We will discuss that in more details below.

In this post, I will comment and compare the following two seminal papers:
1. "<em>Imagination-Augmented Agents for Deep Reinforcement Learning</em>" (Weber, et al., 2017)
2. "<em>World Models</em>" (Ha and Schmidhuber, 2018)

#### <em>Imagination-Augmented Agents for Deep Reinforcement Learning</em>: A short summary

This paper introduces a model called "I2A", which stands for "Imagination-Augmented Agent". The model is made of the following elements:

1. An <strong>"imagination core"</strong>, which itself consists in two elements:
    1. A <em>policy network</em>, which parameterizes a policy $$\hat{\pi}$$.
    2. An <em>environment model</em>, which takes a state and an action, and returns an estimate of the reward and the following state.
2. A <strong>rollout encoder</strong>, which is basically an LSTM network whose function is to encode a sequence of actions and states into a high-dimensional space.
3. An <strong>aggregator</strong>, which simply concatenates a certain number of rollout representations (outputs of the rollout encoder).
4. A <strong>model-free RL algorithm</strong>, which learns a policy $$\pi$$. In the paper, an A3C model is used (see [1] for more details). This algorithm only takes the actual "real" observation as input (it is completely independent from the previous elements).
5. A <strong>policy module</strong>, which takes as inputs both the <em>aggregated rollout representations</em> and the <em>output of the model-free RL algorithm</em>, and outputs a policy vector and an estimated value.

The way these elements interact with each other could be summarized as follows:
1. The agent makes an observation $$o$$.
2. This observation is used as input of the A3C model, which outputs a policy $$\pi$$ and an estimate of the current state's value.
3. $$o$$ is also fed into the imagination core, where it is used to simulate a number of rollouts of the policy $$\hat{\pi}$$. More precisely, the <em>policy network</em> is used to sample an action, and this action plus the current state are fed into the <em>environment model</em>, which generates an estimate of the next state. This new state is in turn fed into the policy network, etc. Repeating this operation allows us to generate a certain number of "imagined" rollouts (we use the word "imagined" because these rollouts are not obtained by interacting with the actual environment, but only an approximate version based on the <em>environment model</em>).
4. Each "imagined" rollout is then encoded using the <em>rollout encoder</em>, and the subsequent representations are aggregated using the <em>aggregator</em>.
5. These aggregated rollout representations, plus the output of the A3C are fed into the <em>policy module</em>, which finally outputs a policy, which in turns is used to sample the actual action.

{% include image.html url="../images/i2a_architecture.png" description="Scheme of the I2A model (source: original paper)" %}

Here is a few facts on how the model is trained (check Annex A if you want a deeper dive):
1. The <em>environment model</em> can be pre-trained, or trained simultaneously with the rest of the I2A model. According to the authors, the pre-training the environment model allows for faster training of the entire architecture (not really surprising).
2. The authors suggest that using an entropy regularizer on the output policy $$\pi$$ favors exploration and accelerates the training (using a small coefficient, here $$10^{-2}$$).
3. They also add a "policy distillation" term to the global loss, which is simply the cross-entropy between $$\pi$$ and $$\hat{\pi}$$. This forces the rollout policy $$\hat{\pi}$$ to resemble the overall model's actual policy $$\pi$$. WHY DOES IT HAVE TO BE DIFFERENT? - JUST A MORE COMPACT REPRESENTATION?
4. Distributed training (A3C) over multiple workers (32-64) with RMSprop. Performed a round of hyperparameter optimization.
5. As it is often the case, uses only the best $$3$$ agents to build the learning curves.

The authors claim strong improvements over the baseline (a basic A3C) in Sokoban, both in terms of data efficiency and overall performance. Sokoban is a puzzle game in which the agent has to push boxes to specific target locations. It can be considered a complex game, as the agent needs to carefully plan its moves ahead of time to avoid being stuck (some moves are irreversible, which can make the puzzle unsolvable). Each episode is randomly generated, it "impossible" for the model to overfit to a specific environment (see Alex Irpan's excellent blog post [2] for a strongly opinionated view on overfitting in deep RL).

The performance curves (see below) show that the I2A agent consistently outperforms the baseline in terms of overall performance. The authors also claim that is can reach interesting levels of generalization (e.g.: they tried to train the model to solve simple Sokoban instances, then evaluated its performance on more complex instances, which was surprisingly good - see 4.5). However, we also see that for a small number of steps (ie at the beginning of the training), the baseline outperforms the I2A agent. COMPRENDRE!

STILL QUITE DATA-INEFFICIENT. UP TO A BILLION FRAMES (HOURS OF PLAY)!

WHY DOES THE ROLLOUT POLICY HAVE TO BE DIFFERENT?

OTHER INTERESTING FACTS:
- Works well even with imperfect environment model! Model stability? (much less variance than Standard MC... -> Weak variance reduction still works quite well!). BUT rollout encoder IS important!
- Other imperfect environment model that predicts no rewards: works almost as well!
- Difference between Fig. 5 (right) and Fig. 4 (left): in Fig. 4, the I2A model learns slower!
- Better planning than MCTS!

The authors also demonstrate the strong link between the unroll depth (that is to say the number of BLAHBLAHBLAH)

#### References

[1] - "<em>Asynchronous methods for deep reinforcement learning</em>", Mnih, et al. (2016.)  
[2] - "<em>Deep Reinforcement Learning Doesn't Work Yet</em>", Alex Irpan (2018), available at ![https://www.alexirpan.com/2018/02/14/rl-hard.html]{https://www.alexirpan.com/2018/02/14/rl-hard.html}

https://bair.berkeley.edu/blog/2017/07/18/learning-to-learn/
