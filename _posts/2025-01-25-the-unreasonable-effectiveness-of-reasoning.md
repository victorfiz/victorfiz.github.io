---
layout: default
title: The Unreasonable Effectiveness of Reasoning
subtitle: Completely unfinished scribbles on understanding reasoning models.
date: 2025-01-25
categories: blog
type: opinion
---



... at the risk over anthropomorphising


1. Explanation of reasoning: sampling explanations, MC sampling evidence via PPO/KL-div (does this match what I know)/clipping 
2. The search for hard-to-vary explanations
3. Evidence: CoT is just gradient descent... we are searching the space for convergence onto a good explanation
4. Evidence: Models try to minimise Dirichlet energy (https://arxiv.org/pdf/2501.00070)
5. We traverse some energy path from the query to the answer (both suggested by the model). We follow this roughly through "untraversed space" (guided by the pretrain). If the points connect, we have identified new data.
6. Humans do this all the time: we can form new connections in our brain without ever maintaining contact with reality. These are plausible explanations we develop. Philosophers do this also. We also do this in the real world: we have some idea (prediction) on how things unforld... we then update our priors strongly accoding to the evidence? Humans have the luxury of being able to test their hypotheses: i.e. grounding their "reasoning" with evidence. We will need to let the models test theirs
7. In order to let nn's create "out of distribution" (scientific discoveries) results, we need to let sample the space... maybe for a very long time! Might we need to shard networks to search the space for different ideas withouth being polluted/ungrounded by other directions? 
8. Are there ways to do more efficient sampling? i.e. query/ans pairs? The brain seems to have an instant intuition for uncertainty/difficulty (then again, this isn't as true in complex planning). So should we be able to get "uncertainty" from the pretrain? Well... uncertainty in humans is a bit like surprise no? Also, it seems like humans evaluate uncertainty based on number of possible "paths" of reasoning?
9. In some sense, this is an NP problem, where it is a lot easier to verify something is true (does the energy match?) than it it to come up with something new that is true...
10. The future of training will be a "test-time-compute" paradigm (Gwern -> https://www.lesswrong.com/posts/HiTjDZyWdLEGCDzqu/?commentId=MPNF8uSsi9mvZLxqz). Labs will own the knowledge-generating brains and distill that to the rest of the world (Train Large, Then Compress: https://arxiv.org/pdf/2002.11794)
11. The bottleneck will be information: where to allocate compute to the most economically useful areas. In our economy this is fine: the ability to act on this information (innovation through allocation of labour and capital) is fairly uniformly distributed. In an oligopoly of general intelligence, innovation will increasingly depend on a few players. The bottleneck for progress will be the messaging of information from enterprises (training data) to the oligopoly. The enterprise will receive in return information on actions to take, etc. That being said, the rate at which this happens will be dependent on...


Unrelated... 

LessWrong : a possible mitigation of the alignment problem is a population of agents with separate incentives and with equal knowledge. There should be a (relatively) frequent exchange of knowledge and some way for agents to check each other's knowledge doesn't exceed the rest of the population. The interests of each agent should be tied to specific organisations.

"When a task has sufficient "policy information capacity" (mutual information between initial policy distribution and reward), RL success is guaranteed" 

