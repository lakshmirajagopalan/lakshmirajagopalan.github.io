---
layout: post
title: "Markov Decision Processes: The Foundation of Reinforcement Learning"
categories: [Blogging, Machine Learning]
tags: [reinforcement learning, machine learning, markov decision process]
seo:
  date_modified: 2026-02-26 22:30:00 +0530
---
In the [last post]({% post_url 2026-02-20-rl-first-pass %}), bandits had no state, we chose an action and received a reward. The next chapter deals with something more general called the Markov Decision Process (MDP) where we can choose different actions in different states.

## 1. Elements of MDP
It is defined by the elements
- Set of States: **S**
- Set of Actions: **A**
- Reward signal: **r**
- Environment dynamics: **p**

### Markov Property

**p(s', r | s, a)** defines the joint probability of reaching *s'* and getting reward *r* after taking action *a* in state *s*.  
Also *p* satisfies the **Markov Property** i.e. next state and reward (*s'* & *r*) depends only on current state (s) and action (a). In other words, ***p*** completely characterizes the environment.

### Derived Quantities from **p**

#### 1. State transition property
The probability of landing in s'  

$$
p(s' | s, a) = \sum_{r} p (s', r | s, a)
$$

#### 2. Expected reward for a state-action pair

$$
r(s, a) = \sum_{s'}\sum_{r} r \cdot p (s', r | s, a)
$$

#### 3. Expected reward for specific transition
Expected reward when we actually transition to s'

$$
r(s, a, s') = \sum_{r} r \cdot \frac{p(s', r | s, a)}{p(s' | s, a)}
$$

## 2. Episodes and Returns
An **episode** is a sequence of states ending in a terminal state.
A **return** from time t is the cumulative sum of rewards from that step to the terminal state.

$$
G_t = R_{t+1} + R_{t+2} + \ldots + R_T
$$

But for continuing tasks (with no terminal state), we use discounting so the sum is bounded.

$$
G_t = \sum_{k=0}^{\infty}\gamma^k R_{t+k+1}
$$

where $0 \leq \gamma \leq 1$.

So the unified return form to include both types of tasks:
$$
G_t = \sum_{k=t+1}^{T} \gamma^{k-t-1} R_{k}
$$
- Small $\gamma$: short-sighted (values immediate rewards more). For continuing tasks ($T = \infty$), $\gamma < 1$ is required for the sum to converge.
- Large $\gamma$: far-sighted. Works for both episodic and continuing tasks.


## 3. Policies and Value Functions
### 1. Policy
It defines the probability of taking action 'a' in state 's'.

$$
\pi(a|s)
$$

### 2. State-Value function
Defines the expected return at state 's' following policy $\pi$:

$$
v_{\pi}(s) = \mathbb{E}[G_t | S_t = s]
$$

### 3. Action-Value Function
Defines the expected return at state 's' taking action 'a', then following $\pi$:

$$
q_{\pi}(s, a) = \mathbb{E}[G_t | S_t = s, A_t = a]
$$

## 4. Monte Carlo Estimation
A preview of what's to come: when we run episodes and record returns each time we visit state 's' and average them, the estimate converges to $v_{\pi}(s)$. Similarly, averaging over state-action pairs, the value converges to $q_{\pi}(s, a)$. Monte Carlo methods estimate these values purely from experience.

## 5. Recursive Relations (Bellman Equations)
The key insight is that we can expand the return recursively. The state-value function satisfies:

$$
\begin{aligned}
v_{\pi}(s) &= \mathbb{E}[G_t | S_t = s] \\
&= \mathbb{E}[R_{t+1} + \gamma G_{t+1} | S_t = s] \\
&= \sum_{a}\pi(a | s) \sum_{s', r} p(s', r | s, a) [r + \gamma \mathbb{E}[\ G_{t+1}\ |\ S_{t+1} = s'\ ] ] \\
&= \sum_{a}\pi(a | s) \sum_{s', r} p(s', r | s, a)\bigl[r + \gamma v_{\pi}(s')\bigr]
\end{aligned}
$$

Value of state = Expected immediate reward + discounted next state-value,  
weighted by:
- policy probabilities — $\pi$
- transition probabilities — $p$

## 6. Optimal Value Functions and Optimal Policy

$$
\begin{aligned}
v_{\ast}(s) &= \max_{\pi} v_{\pi}(s) \quad \forall s \in S \\
q_{\ast}(s, a) &= \max_{\pi} q_{\pi}(s, a) \quad \forall s \in S, a \in A
\end{aligned}
$$

The *Bellman Optimality equation* states that the value of a state under the optimal policy must equal the expected return from the best action in that state:

$$
\begin{aligned}
v_{\ast}(s) &= \max_{a \in A(s)} q_{\ast}(s, a) \\
&= \max_{a} \sum_{s', r} p(s', r | s, a) \bigl[ r + \gamma v_{*}(s') \bigr]
\end{aligned}
$$

If there are n states, we can solve for n equations in n unknowns to obtain $v_{\ast}(s)$. Once we have that, and provided 'p' is known, we compute the expected value in the above equation for each 'a' in 's' and pick the max. Since $v_{\ast}(s')$ already encodes the optimal value of state s', we only need to look one-step ahead and not the entire future. Hence the optimal policy is greedy.
