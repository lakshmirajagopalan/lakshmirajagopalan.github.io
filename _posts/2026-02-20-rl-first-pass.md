---
layout: post
title: "Reinforcement Learning: A First Pass Through Sutton & Barto"
categories: [Blogging, Machine Learning]
tags: [reinforcement learning, machine learning, artificial intelligence]
seo:
  date_modified: 2026-02-21 00:00:00 +0530
---

I came across this big book *Reinforcement Learning by R S Sutton* at the library today.
With AI agents becoming more common, I felt it will be important to understand how we can guide and reinforce their behavior in a structured way.

I managed to get through the first couple of chapters only. It is definitely dense and technical, so I left some of the exercises for later. But even from the early chapters, I am starting to get a clearer picture of the core ideas.

Here is my attempt to summarize what I have understood so far.

## 1. What is Reinforcement Learning

RL is about *Learning how to act through interaction* (like learning to read a bicycle)

An agent:
- Takes an action
- Observes the outcome on the env
- Receives a reward
- Adjusts its behavior 
- Repeat

The objective is not immediate correctness but to *maximize cumulative reward over time*.


## 2. RL vs Supervised vs Unsupervised
Unlike supervised learning, the agent is never told the correct action. It must discover good behavior through *trial and error*. It is only provided a reward feedback. This feedback affects the next decisisons and it optimizes(max) for long-term cummulative reward.   
Supervised is *instructive* while RL is *evaluative*.

## 3. Core Elements of RL
RL has 4 major components:

### 1. Policy (π)
Defines the agent's behaviour. It maps state -> action
### 2. Reward Signal (R)
The immediate feedback signal. 
### 3. Value function (Q)
Estimates how good is state/action in the long run.
### 4. Model(Optional)
A model(optional) to predict what next state will occur and what reward will be received.  
With model - we can plan  
Without it - We learn from experience alone 

## 4. Exploration vs Exploitation

This is the central dilemma.

**Exploit**: Choose the best-known action.  
**Explore**: Try new actions to discover better ones. 

#### Greedy:
Always exploit the highest estimated value. But can get stuck in suboptimal actions.

#### $\epsilon$-Greedy:
Explore with probability $\epsilon$, exploit with prob 1-$\epsilon$

## 5. Modes of learning

#### Trial and error
- originates from animal learning

#### Optimal Control 
- uses *Value functions* to act optimallly for long-term reward

#### Temporal Difference
- uses *secondary reinforcers* in addition to primary reinforcers.

## Chapter 2: Multi-Armed Bandits
Simple - no states just considering actions and feedback.

In k-armed bandit problem, each of the k action has a expected reward-*value* for a selected action 'a'.

$$
q^{*}(a) = E[R_t | A_t=a]
$$

But since we don't know the *true value*, we estimate **Q<sub>t</sub>(a)** through various *Action-Value* methods.

### Action-Value methods
#### 1. Sample Average Method
We estimate value by averaging the observed rewards  

$$
\begin{aligned}
Q_t(a) = \frac{\sum_{i=1}^{t} R_i * \mathbf{1}_{\{A_t=a\}}}{\sum_{i=1}^{t} \mathbf{1}_{\{A_t=a\}}}
= \frac{sum\ of\ rewards\ when\ a\ taken\ prior\ to\ t}{num\ of\ times\ a\ taken\ prior\ to\ t}
\end{aligned}
$$

and for greedy case, the action chosen then is

$$
A_t = argmax\ Q_t(a)\ over\ all\ action\ a
$$

*Incremental computation* of the estimated value fn:

$$
\begin{align}
Q_{n+1} = Q_n + \frac{1}{n}[R_n - Q_n] \\\\

NewEstimate = OldEstimate + LearningRate[Target - OldEstimate]
\end{align}
$$

The learning rate, $\alpha = \frac{1}{n}$ changes with each step(time) and works for stationary bandit problems - where the reward probabilities do not change over time.

#### 2. Exponential Recency-Weighted Average
For non-stationary bandit problemns, we would like to give more weight to recent rewards than older ones. With constant $\alpha$, the estimate value fn:

$$
Q_{n+1} = Q_n + \alpha[R_n - Q_n]
$$

and after recursive substitution, the weight given to R<sub>i</sub> depends on *n-i*  i.e. how old was R<sub>i</sub> observed.

$$
Q_{n+1} = (1-\alpha)^n Q_1 + \sum_{i=1}^{n} \alpha(1-\alpha)^{n-i} * R_i
$$

### Encouraging Exploration
#### 1. Setting Optimistic Initial Values

When rewards are +ve and the initial *Q<sub>1</sub>* is set 0, the first action(say: a) always increase only *Q<sub>a</sub>* causing the agent to get stuck and greedy pick only this. Setting the initial estimates to higher value (> the max reward for each action), cause any initial action to lower the Q(based on exponential-recency weighted average) thus encouraging exploration of other actions.

#### 2. Upper Confidence Bound (UCB)
Explore the non-greedy actions not randomly but more principled - based on how optimal they are.  

$$
\begin{align}
A_t = argmax[Q_t(a) +c\sqrt{\frac{ln\ t}{N_t(a)}}] \\\\
\ \ \ = argmax [estimated + uncertainity]
\end{align}
$$

Here, we are assuming that the upper bound for the value could be *value <= Estimate + uncertainity* (hence the term).

Q<sub>t</sub> is the exploitation portion and the second part is the exploration-uncertainity portion.  
*c* is the confidence scaling factor (low c - greedy, high c - aggressive exploration).

When a particular 'a' gets tried often N<sub>t</sub>(a) increases along with numerator -> uncertainity decreases, hence the overall sum can be smaller than others with more uncertainity and similar Q<sub>t</sub>. For other actions, only the numerator increases, hence the uncertainity increases.

Using the natural log in numerator, makes the uncertainity increase get smaller with time.

### Gradient Bandit Algorithms
Another approach to selecting an action, is to find a preference(**H<sub>t</sub>(a)**) for an action instead of the value(**Q<sub>t</sub>(a)**) of an action. Preference is relative to other actions and we take a softmax to get the probability
of each action.

$$\pi_t(a) = \frac{e^{H_t(a)}}{\sum_{b=1}^{k}{e^{H_t(b)}}}$$

All initial preferences are 0 **H<sub>1</sub>(a)=0**. The learning algo to incrementally updates the preference based on the gradient ascent as

$$
\begin{aligned}
H_{t+1}(A_t) = H_t(A_t) + \alpha(R_t - \bar{R_t})(1 - \pi_t(A_t)) \\
H_{t+1}(a) = H_t(A_t) - \alpha(R_t - \bar{R_t})\pi_t(a)\ \ \ \ \ \ \ \ for\ all\ a <> A_t
\end{aligned}
$$


$\bar{R_t}$ - average rewards upto t (also a baseline).  
${R_t - \bar{R_t}}$ - how better is the reward now with this action A<sub>t</sub>.
If the diff is +ve, increase the preference for this action, decrease the preferences for other actions and vice-versa.
${(1 - \pi_t(A_t))}$ - If the action is already highly prefered, then change should be less and rare actions should be rewarded more.




