---
title: "richard sutton on oak at rlc 2025"
date: 2025-08-30
tags: talks rl
---

Richard Sutton ([home page](http://incompleteideas.net/), [wikipedia](https://en.wikipedia.org/wiki/Richard_S._Sutton))
gave a [keynote talk](https://www.youtube.com/watch?v=dEweRR7tQqg) 
titled *The OaK Architecture: A Vision of SuperIntelligence from Experience*
at the 2025 [Reinforcement Learning Conference (RLC)](https://rl-conference.cc/index.html)
held at the [University of Alberta](https://www.ualberta.ca/en/science/about-us/facilities/index.html).
This talk was fun. 
It was informal and unpolished and felt like someone trying to explain ideas they are excited about but haven't totally worked out yet. Below is a summary that uses some quotes from the slides.

Sutton starts off by stating his perspective that super inelligent agents are coming, 
that they will be good for the world, 
that the path to creating them runs through reinforcement learning,
and that the biggest bottleneck is inadequate learning algorithms.

He outlines his role as one who has tried to think deeply about intelligence for fifty years
and references *The Alberta Plan for AI Research* 
([paper](https://arxiv.org/abs/2208.11173), [slides](http://www.incompleteideas.net/Talks/AlbertaPlan.pdf))
he developed along with 
[Mike Bowling](https://webdocs.cs.ualberta.ca/~bowling/) and 
[Patrick Pilarski](https://sites.ualberta.ca/~pilarski/).
The OaK acronum stands for "Options and Knowledge" and is a component of The Alberta Plan 
consituting a vision for an overall agent architecture.

In OaK, an option is a pair $\(\pi, \gamma\)$ where $\pi$ is a policy (a way of behaving)
and $\gamma$ is a termination condiation (a way of terminating the behavior).
Slightly more technically, a policy $\pi$ maps states to a probability distribution over actions
and a termination condition $\gamma$ maps states to 0 or 1.
Knowledge is what the agent learns when different options are followed until termination.

The agent has three main design goals,

* domain general: contains nothing specific to any world
* experiential: grows from runtime experience, not from a special training phase
* open ended: unlimited in sophistication (other than by computation resources)

Sutton focuses on the importance of continuous learning during runtime vs designtime.
He references the 
[The Big World Hypothesis](https://openreview.net/forum?id=Sv7DazuCn8) and 
[The Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)
in advocating for systems that are designed with only the meta-methods for learning
as opposed to any specific domain knowledge.

> We want AI agents that can discover like we can, not which contain what we have discovered
> <cite>[The Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)</cite>

After going into more details on the big world hypothesis, Sutton introduces the reward hypothesis,

> all of what we mean by goals and purposes can be well thought of as maximization of the expected value of the cumulative sum of a received scalar signal (reward).
> <cite>[Settling the Reward Hypothesis](https://arxiv.org/abs/2212.10420)</cite>

He stresses that his goal is simplicity and that he wants a system that can learn generally using a scalar reward.
Next he presents a series of architectures that lead to OaK,

* model-free: Basic RL. The agent constructs an approximate policy and/or value function, both a function of the world's state.
* non-markov: Better. The agent constructs its state representation (e.g. as a feature vector)
* model-based: Better. The agent constructs a transition model o fthe world and uses it to plan a better policy and/or value function
* OaK:
  * The agent poses auxiliary subproblems for attaining individual features
  * These enable the discovery of high and higher levels of abstraction limited only by computational resources


Next, an outline of the OaK architecture is presented as a set of steps to be performed in parallel,

* <span style="color: blue;">✔</span> Learn the policy and value function for maximizing reward
* <span style="color: red;">✔</span> Generate new state features from existing state features
* <span style="color: orange;">✔</span> Rank-order the features
* <span style="color: green;">✔</span> Create subproblems, one for each highly-ranked feature
* <span style="color: blue;">✔</span> Learn solutions to subproblems (options and sub-value functions)
* <span style="color: blue;">✔</span> Learn transition models of the options and actions
* <span style="color: green;">✔</span> Plan with the transition models
* <span style="color: orange;">✔</span> Maintain meta-data on the utility of everything; curate


With the checkmarks indicating Sutton's estimation on how much progress has been made for each,

* <span style="color: blue;">✔</span> Would be done if we could do continual deep learning and meta-learning
* <span style="color: red;">✔</span> Lots of ideas, but no specific proposal
* <span style="color: orange;">✔</span> Seems easy but can't be done until the rest are done
* <span style="color: green;">✔</span> Seems done

The rest of the talk goes into more technical details on how the OaK system might go about creating and solving its own subproblems.





# References

* [The Alberta Plan](https://arxiv.org/abs/2208.11173)
* [The Big World Hypothesis](https://openreview.net/forum?id=Sv7DazuCn8)
* [The Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)
* [On the Role of Tracking in Stationary Environments](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=93be31bc7a0c5cb93cd000a9cfe574cd5f057972)
* [Settling the Reward Hypothesis](https://arxiv.org/abs/2212.10420)
* [Reward is Enough](https://www.sciencedirect.com/science/article/pii/S0004370221000862)
* [Reward-Respecting Subtasks for Model-Based Reinforcement Learning](https://arxiv.org/abs/2202.03466)