---
layout: post
author: jnr
use_math : true
categories: paper-review
tags: concept sample-inefficiency exploration exploitation generalization reproducibility
---
---
<h2 id="top"></h2>
<br>
📝 H. Nguyen and H. La, "Review of deep reinforcement learning for robot manipulation," in Proc. 3rd IEEE Int. Conf. Robot. Comput. (IRC), Naples, Italy, Feb. 2019, pp. 590–595. [link](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8675643)<br>

📌 [`Abstract`](#abs) [`Intro`](#int) [`Background`](#bac) [`Approach`](#app) [`Results & Discussion`](#res) [`Conclusion`](#con) <br><br>

<h2 id="abs">Abstract</h2>
- Address the current status of RL algorithms <br>
- Cover essential theoretical background and limit of RL algorithms in solving practical problems in robotics <br>
- Give future directions for RL research <br>
<br>

<h2 id="int">Introduction</h2>
There are some `inherent difficulties` in applying RL to solve practical robotic challenges. Our review aims to `provide our perceived picture` of using RL in the context of robot manipulation. We try to cover background knowledge, interesting research results, open problems, and provide our insights into future directions. <br>
We decided to conduct the review starting in 2013. we start in section Ⅱ by describing key RL concepts and terminology. Section Ⅲ continues with a taxonomy of RL algorithms followed by section Ⅳ with a focus on the context of robot manipulation. We describe our perspectives about future directions in section Ⅴ and section Ⅵ concludes the review.
<br><br>

<h2 id="bac">Background</h2>
💡 [KEY CONCEPTS AND TERMINOLOGY] <br><br>
![Fig. 1](images/2022-09-14.PNG) <center>Fig 1: The agent-environment interaction</center> <br>
There are four main elements of a RL system: <br>
a policy, a reward signal, a value function, and optionally a model of the environment.
- policy: defines the learning agent's way of behaving at a given time. mapping from the perceived states to actions to be taken when being in those states.
- reward signal: a reward is dependent on the current state and the action $r=R(s,a)$. the agent's ultimate goal is to maximize the cumulative reward  $G_t=R_{t+1} + \gamma*R_(t+2)+\gamma^2*R_(t+3)+\cdots$ (discount rate $\gamma\in[0 1]$)
- model of the environment: allows inferences about how the environment will behave. the dynamics of the environment is fully characterized by a distribution $p$. <br><br>

Markov Decision Process(MDP) is a classical formalization of sequential decision making. <br>
It contains:
- a set of possible states $\mathcal{S}$
- a set of possible actions $\mathcal{A}$
- a reward function $R(s,a)\in\mathcal{R}$
- a probability distribution $p(s',r\|s,a)$ of the environment <br><br>

A value function specifies what is good in the long run of a state $s$ or a state-action pair $(s,a)$ when following a particular policy $\pi$.
- value function $V^\pi(s)=E_{a\~\pi}[R(\tau)\|s_t=s]$
- action-value function $Q^\pi(s,a)=E_{a~\pi}[R(\tau)\|s_t=s, a_t=a]$
- optimal value function $V^\ast(s)=max_\piE_{a~\pi}[R(\tau)\|s_0=s]$
- optimal action-value function $Q^\ast(s,a)=max_\piE_{a~\pi}[R(\tau)\|s_0=s, a_0=a]$

Given the optimal $Q^{*}(s,a)$, we can obtain the optimal action $a^(*)(s)=arg max_a Q^(*)(s,a)$ at a given state $s$, and then we can directly construct the optimal policy $\phi^{*}$. <br>

Bellman equation is obeyed by all four types of value functions.
- $V^\pi(s)=E[G_t\|s_t=s]=E[r_t+\gamma*G_{t+1}\|s_t=s]=E[r_t+\gamma*E[G_{t+1}\|s_t=s]\|s_t=s]=E[r_t+\gamma*V^\pi(s_{t+1})\|s_t=s]$
- $G^\pi(s,a)=E[G_t\|s_t=s, a_t=a]=E[r_t+\gamma*G_{t+1}\|s_t=s, a_t=a]=E[r_t+\gamma*E[G_{t+1}\|s_t=s, a_t=a]\|s_t=s, a_t=a]$
- $V^\ast(s)=E[G_t\|s_t=s]=E[r_t+\gamma*G_{t+1}\|s_t=s]=E[r_t+\gamma*E[G_{t+1}\|s_t=s]\|s_t=s]=E[r_t+\gamma*V^\ast(s_{t+1})\|s_t=s]$
- $Q^\ast(s,a)=E[G_t\|s_t=s, a_t=a]=E[r_t+\gamma*G_{t+1}\|s_t=s, a_t=a]=E[r_t+\gamma*E[G_{t+1}\|s_t=s, a_t=a]\|s_t=s, a_t=a]=E[r_t+\gamma*Q^\ast(s_{t+1},a_{t+1})\|s_t=s, a_t=a]=E[r_t+\gamma*max_{a_{t+1}}Q^\ast(s_{t+1},a_{t+1})\|s_t=s, a_t=a]$
<br><br><br>

💡 [TAXONOMY OF RL ALGORITHMS] <br><br>
A. Model-based amd Model-free <br>
- whether the agent has the access or learns a model of the environment.
- Model-based: having a model that allows the agent to plan ahead to predict what would happen when choosing a certain action from a range of possible ones.
- Model-free: it is usually the case when the model is not available or the learning of such a model is very challenging. figure out the value functions directly from the interactions with the environment. rely heavily on reward signals for learning the value functions, so it is important to have learning-induced reward functions. it is often easier to implement and tune hyperparameters. <br><br>

B. Value-based and Policy-based <br>
- Value-based: try to estimate the action-value function $Q(s,a\|\theta)$ for the optimal $Q^\ast(s,a)$. this optimization is often performed off-policy, meaning that the policy used to generate behavior for getting training data, may be unrelated to the policy that is evaluated and improved, called the estimation policy.
- Policy-based: parameterize the policy as $\pi(s,a\|\theta)$, and the target is to optimize $\theta$ either through gradient descent on an objective function $J(\pi)$ or by maximizing local approximations of $J$. this method is often on-policy, meaning that they estimate the value of a policy while using it for control. it is less sample-efficient as they only use samples collected from the latest version of the policy. we directly optimize what we need, which allows stability and reliability improvements.
<br><br>

<h2 id="app">Approach</h2>
Robotics RL is often modeled as `partially observable MDP` as it is common for states to be unobservable or partially observable. Successful algorithms especially model-based methods, therefore, need to be `robust to a significant scale of uncertainty` in the model. we discuss three main issues, from our perspective, limiting the application of RL for real-world robotics problems. <br><br><br>

💡 [sample inefficiency] <br><br>
There are multiple causes for the problem.
1. many algorithms try to learn to perform a task from `scratch`, therefore, they would need a lot of data to learn.
2. algorithms are still `not good enough at exploiting` useful information from current data.
3. data collection in robotics is often very `time-consuming`.

evolution algorithms[^1] are the least sample-efficient as they do not use gradient for optimixation, but they might have comparable performance. Policy gradient methods is next in terms of sample-efficiency, followed by methods that uses replay buffer to estimate Q-values. Model-based algorithms are taking the lead in terms of data efficiency as they try to derive a model of the environment and use that model for training the policy instead of data from real interactions. Guided Policy Search[^2] is very data-efficient as it used trajectory optimixation to direct policy learning and avoid poor local optima. The current winner is model-based "shallow" algorithm[^3].








💡 [exploration and exploitation] <br><br>

💡 [generalization and reproducibility] <br><br>
<br><br>

<h2 id="res">Results and Discussion</h2>
The strongest motivation for future developments is how to efficiently take deep RL algorithms to the real world. From our perspective, agents/robots must learn `much faster and more efficiently`. <br>
`Model-based learning` has the biggest advantage of being sample-efficient and there existed interesting research in this direction to predict the future. <br>
There is still a `big gap between RL algorithms and humans` in terms of sample efficiency. We do not learn from scratch, and we can reuse our past knowledge to learn a new skill much more efficiently. Model-based learning approach can also help in this situation, thanks to its more potential transferability and generality. A different approach rather than approximating the dynamics is to use `multi-task learning` to reuse skills. The interesting thing in this work was learning on various tasks had better performance compared to learning in a single-task setting. <br>
Transfer learning[^4] tries to use experience from one set of tasks for faster learning and better performance on a new task. Transfer learning from tasks trained on simulators is particularly tempting as relatively cheap resources are needed. Another study performed `parallel learning` between simulated and real robots by introducing `additional alignment rewards` that encourage both agents in two domains to have similar distributions over visited states. <br>
`Inverse RL` is also promising, which can solve the nightmare of designing reasonable reward functions.
<br><br>

<h2 id="con">Conclusion</h2>
- Described RL algorithms used in robot manipulation <br>
- Adjusting RL on real robot applications is still limited <br>
- But RL in robot manipulation will have a promising future ahead <br><br>

[`Top`](#top)

---
[^1]: 진화 알고리즘. 생식, 돌연변이 등 생물학에서의 진화를 본뜬 메카니즘을 사용하는 어떤 개체군 기반의 조합최적화 알고리즘.[참고](http://www.aistudy.com/biology/genetic/evolutionary_algorithm.htm)
[^2]: guide or limit search directions while training of a deep neural network based policy model. [참고](https://koreascience.kr/article/JAKO201912262463181.pdf)
[^3]: reference [15] 참고.
[^4]: 전이학습. 한 문제를 해결하고자 얻은 지식과 정보를 다른 문제를 푸는데 사용하는 방식. 특히 컴퓨터 비전의 영역에서 전이 학습으로 수행된 모델들이 높은 성능을 보여, 많이 사용되고 있음.[참고](https://dacon.io/forum/405988)
