---
layout: post
author: jnr
use_math : true
published: true
categories: paper-review
tags: robotic-vehicle pedestrian social-norm
---

---
<h2 id="top"></h2><br>
📝 Y. F. Chen, M. Everett, M. Liu, and J. P, " Socially aware motion planning with deep reinforcement learning," in Proc. IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS),  Vancouver, BC, Canada, Sep. 2017, pp. 1343–1350. [link](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8202312) <br>

📌 [`Abstract`](#abs) [`Intro`](#int) [`Background`](#bac) [`Approach`](#app) [`Experiments`](#exp) [`Results & Discussion`](#res) [`Conclusion`](#con) <br><br>

<h2 id="abs">Abstract</h2>
- For robotic vehicles to navigate safely and efficiently in pedestrian-rich environments, it is important to model subtle human behaviors and navigation rules. <br>
- However, existing works often do not generalize well since the feature values can vary from person to person, even run to run. <br>
- it is straightforward to specify what not to do. <br>
- Using deep RL, this work develops a time-efficient navigation policy that respects common social norms. <br>
- The proposed method is shown to enable fully autonomous navigation of a robotic vehicle moving at human walking speed in an environment with many pedestrians. <br>
<br>

<h2 id="int">Introduction</h2>
Robots should navigate efficiently and safely in close proximity of people, but it is challenging because pedestrians tend to follow subtle social norms that are difficult to quantify, and pedestrians' intents are usually not known. <br>







A common approach treats pedestrians as dynamic obstacles with simple kinematics, and employs specific reactive rules for avoiding collision. Since these methods do not capture human behaviors, they sometimes generate unsafe/unnatural movements, particularly when the robot operates near human walking speed. To address this issue, more sophisticated motion models have been proposed, which would reason about the nearby pedestrians' hidden intents to generate a set of predicted paths. Subsequently, classical path planning algorithms would be employed to generate a collision-free path for the robot. Yet, spearating the navigation problem into disjoint prediction and planning steps can lead to the freezing robot problem, in which the robot fails to find any feasible action because the predicted paths could mark a large portion of the space untraversable. A key to resolving this problem is to account for cooperation, that is, to model/anticipate the impact of the robot's motion on the nearby pedestrians.

Existing work on coopertive, socially compliant navigation can be broadly classified into two categories, namely model-based and learning-based. Model-based approaches are typically extensions of multiagent collision avoidance algorithms, with additional parameters introduced to account for social interactions. Model-based methods are designed to be computationally efficient as they often correspond to intuitive geometric relations; yet, it is unclear whether humans do follow such precise geometric rules. In particular, the force parameters often need to be tuned individually, and can vary significantly for different pedestrians. Also, it has been observed that model-based methods can lead to oscillatory paths.

In comparison, learning-based approaches aim to develop a policy that emulates human behaviors by matching feature statistic. Compared with model-based approaches, learning-based methods have been shown to produce paths that more closely resemble human behavoirs, but often at a much higher computational cost. This is because computating/matching trajectory features often requires anticipating the joint paths of all nearby pedestrians, and might depend on some unobservable information. More importantly, since human behaviors are inherently stochastic, the feature statistics calculated on pedestrians' paths can vary significantly from person to person, and even run to run for the same scenario. This raises concerns over whether such feature-matching methods are generalizable to different environments.





In short, existing works are mostly focused on modeling and replicating the detailed mechanisms of social compliance, which remains difficult to quantify due to the stochasticity in people's behaviors. The main contributions of this work are i) `introducing` socially aware collision avoidance with deep RL(`SA-CADRL`) for explaining/inducing socially aware behaviors, ii) `generalizing to multiagent`(n>2) scenarios through developing a symmetrical NN structure, and iii) `demonstrating on robotic hardware` autonomous navigation at human walking speed in a pedestrian-rich environment.
<br><br>

<h2 id="bac">Background</h2>
💡 [KEY CONCEPTS AND TERMINOLOGY] <br><br>
![Fig. 1](images/2022-09-14.PNG) <center>Fig 1: The agent-environment interaction</center> <br>
There are four main elements of a RL system: <br>
a policy, a reward signal, a value function, and optionally a model of the environment.
- policy: defines the learning agent's way of behaving at a given time. mapping from the perceived states to actions to be taken when being in those states.
- reward signal: a reward is dependent on the current state and the action $r=R(s,a)$. the agent's ultimate goal is to maximize the cumulative reward $G_t=\sum_{k=t+1}^T \gamma^{k-t-1}*R_k$
- model of the environment: allows inferences about how the environment will behave. the dynamics of the environment is fully characterized by a distribution $p$. <br><br>

Markov Decision Process(MDP) is a classical formalization of sequential decision making. <br>
It contains:
- a set of possible states $\mathcal{S}$
- a set of possible actions $\mathcal{A}$
- a reward function $R(s,a)\in\mathcal{R}$
- a probability distribution $p(s',r\|s,a)$ of the environment <br><br>

A value function specifies what is good in the long run of a state $s$ or a state-action pair $(s,a)$ when following a particular policy $\pi$.
- value function $V^\pi(s)=E_{a\sim\pi}[R(\tau)\|s_t=s]$
- action-value function $Q^\pi(s,a)=E_{a\sim\pi}[R(\tau)\|s_t=s, a_t=a]$
- optimal value function $V^\ast(s)=max_{\pi}E_{a\sim\pi}[R(\tau)\|s_t=s]$
- optimal action-value function $Q^\ast(s,a)=max_{\pi}E_{a\sim\pi}[R(\tau)\|s_t=s, a_t=a]$

Given the optimal $Q^\ast(s,a)$, we can obtain the optimal action $a^\ast(s)=arg max_a Q^\ast(s,a)$ at a given state $s$, and then we can directly construct the optimal policy $\pi^\ast$. <br>

Bellman equation[^1] is obeyed by all four types of value functions.
- $V^\pi(s)=E[r_t+\gamma*V^\pi(s_{t+1})\|s_t=s]$
- $G^\pi(s,a)=E[r_t+\gamma*E[G_{t+1}\|s_t=s, a_t=a]\|s_t=s, a_t=a]$
- $V^\ast(s)=E[r_t+\gamma*V^\ast(s_{t+1})\|s_t=s]$
- $Q^\ast(s,a)=E[r_t+\gamma*max_{a_{t+1}}Q^\ast(s_{t+1},a_{t+1})\|s_t=s, a_t=a]$
<br><br><br>

💡 [TAXONOMY OF RL ALGORITHMS] <br>
A. Model-based amd Model-free <br>
- whether the agent has the access or learns a model of the environment.
- Model-based: having a model that allows the agent to plan ahead to predict what would happen when choosing a certain action from a range of possible ones.
- Model-free: it is usually the case when the model is not available or the learning of such a model is very challenging. figure out the value functions directly from the interactions with the environment. rely heavily on reward signals for learning the value functions, so it is important to have learning-induced reward functions. it is often easier to implement and tune hyperparameters. <br><br>

B. Value-based and Policy-based <br>
- Value-based: try to estimate the action-value function $Q(s,a\|\theta)$ for the optimal $Q^\ast(s,a)$. this optimization is often performed off-policy, meaning that the policy used to generate behavior for getting training data, may be unrelated to the policy that is evaluated and improved, called the estimation policy.
- Policy-based: parameterize the policy as $\pi(s,a\|\theta)$, and the target is to optimize $\theta$ either through gradient descent on an objective function $J(\pi)$ or by maximizing local approximations of $J$. this method is often on-policy, meaning that they estimate the value of a policy while using it for control. it is less sample-efficient as they only use samples collected from the latest version of the policy. we directly optimize what we need, which allows stability and reliability improvements.
<br><br>

<h2 id="app">Approach</h2>
Robotics RL is often modeled as `partially observable MDP` as it is common for states to be unobservable or partially observable. Successful algorithms especially model-based methods, therefore, need to be `robust to a significant scale of uncertainty` in the model. we discuss three main issues, from our perspective, limiting the application of RL for real-world robotics problems. <br><br>

💡 [sample inefficiency] <br>
There are multiple causes for the problem.
1. many algorithms try to learn to perform a task from `scratch`, therefore, they would need a lot of data to learn.
2. algorithms are still `not good enough at exploiting` useful information from current data.
3. data collection in robotics is often very `time-consuming`.

Evolution algorithms[^2] are the least sample-efficient as they do not use gradient for optimization, but they might have comparable performance. Policy gradient methods are next in terms of sample efficiency, followed by methods that use replay buffer to estimate Q-values. Model-based algorithms are taking the lead in terms of data efficiency as they try to derive a model of the environment and use that model for training the policy instead of data from real interactions. Guided Policy Search[^3] is very data-efficient as it used trajectory optimization to direct policy learning and avoid poor local optima. The current winner is model-based "shallow" algorithm[^4]. <br>

In order to be more data-efficient, we need to `collect more data` and use the data more efficiently. One way to have more data is `using multiple robots` to collect data simultaneously. Real data can also be augmented with `synthetic data`, possibly from simulators. In this approach, the `gap` between synthetic data and real robot data `needs to be reduced`. We can use deep learning architecture[^5], progressive networks[^6], and so on[^7]. In robotics, data is `specific to certain robots and configurations`. It will be very useful if we have a mechanism to `transform data` so that it can be widely distributed and used in multiple platforms and configurations. Finally, we will need `novel algorithms` that can use data more `efficiently`. `Model-based` approach might be one of the most potential ways to unlock sample efficiency.
<br><br><br>

💡 [exploration and exploitation]
- exploration: give more knowledge about the environment, which can lead to better future decisions.
- exploitation: choose the best action to take given the current information.

A balance between exploration and exploitation is required. Using $\epsilon$-greedy to balance exploration and exploitation, the agent will either take random action at the probability $\epsilon$ or follow the action that maximized the Q-value with the probability $1-\epsilon$. Vanilla policy gradient method[^8], Trust Region Policy Optimization(TPRO)[^9], and Proximal Policy Optimization(PPO)[^10] explore by sampling actions according to the latest version of its stochastic policy. DDPG[^11] trains a deterministic policy in an off-policy way with noise being added to the action at training time. Soft Actor-Critic(SAC)[^12] explores with entropy regularization. <br>

$\epsilon$-greedy is one of the most commonly used methods for exploration, but it `treats all actions equivalently`. So, it is unguided, too naive[^13], and does not explore areas with promising actions.
For `on-policy` algorithms, the scale of randomness is reduced due to the updated policy rule favoring more exploitation during training. As a result, the policy might be `trapped in local optima`.
For `deterministic policies`, `noise` is added to their actions during training time, and the scale of noise might be reduced to get more high-quality training time. This approach will become `insufficient` when facing `sparse and deceptive reward` problem.
We also `lack a useful benchmark` that can be used to evaluate the performance of different approaches for exploration. Also, the `performance` of exploration strategies `vary among environments and configurations`, making it hard to quantify the true improvement.
And, exploration which is facing uncertainty is very `unsafe for fragile robots`.
<br><br><br>

💡 [generalization and reproducibility] <br>
There are two main directions to investigating the generalization[^14]. The first approach is similar to robust control[^15] in control theory when policies are designed so that they can still function with environment variations by scarifying performance in other environments. The second approach is similar to adaptive control[^16], trying to adapt to the current environment.
Regarding reproducibility[^17], Network structure is one of the factors that can significantly impact performance. Random seed is another factor having a large effect on performance. To improve robustness, some research tried to close the control loop with visual feedback[^18], or optimize hyperparameters using genetic algorithms[^19]. <br>

We currently do `not have an effective benchmark` to evaluate the generalization. For a testbed for measuring generalization, we need to clearly define a set of tasks, comparison metrics, and baselines so that we can quantify fairly.
Combating reproducibility is challenging for the continuous environment such as in robots due to a `higher instability`. Besides the need for more robust RL algorithms to hyperparameters, we would possibly need to agree on proper experimental methodologies and proper evaluation methods and metrics to address the problem. Effective tools for documenting changes during experiment setup and `standard set of environments` are also useful for improving reproducibility.
<br><br>

h2 id="exp">Experiments</h2>

<h2 id="res">Results and Discussion</h2>
The strongest motivation for future developments is how to efficiently take deep RL algorithms to the real world. From our perspective, agents/robots must learn `much faster and more efficiently`. <br>
`Model-based learning` has the biggest advantage of being sample-efficient and there existed interesting research in this direction to predict the future. <br>
There is still a `big gap between RL algorithms and humans` in terms of sample efficiency. We do not learn from scratch, and we can reuse our past knowledge to learn a new skill much more efficiently. Model-based learning approach can also help in this situation, thanks to its more potential transferability and generality. A different approach rather than approximating the dynamics is to use `multi-task learning` to reuse skills. The interesting thing in this work was learning on various tasks had better performance compared to learning in a single-task setting. <br>
Transfer learning[^20] tries to use experience from one set of tasks for faster learning and better performance on a new task. Transfer learning from tasks trained on simulators is particularly tempting as relatively cheap resources are needed. Another study performed `parallel learning` between simulated and real robots by introducing `additional alignment rewards` that encourage both agents in two domains to have similar distributions over visited states. <br>
`Inverse RL` is also promising, which can solve the nightmare of designing reasonable reward functions.
<br><br>

<h2 id="con">Conclusion</h2>
- This work presented SA-CADRL, a multiagent collision avoidance algorithm that considers and exhibits socially compliant behaviors. In particular, in a RL framework, a pair of simulated agents navigate around each other to learn a policy that respects human navigation norms. <br>
- This approach is further generalized to multiagent(n>2) scenarios through the use of a symmetrical NN structure. <br>
- Moreover, SA-CADRL is implemented on robotic hardware, which enabled fully autonomous navigation at human walking speed in a dynamic environment with many pedestrians. <br>
- Future work will consider the relationships between nearby pedestrians, such as a group of people who walk together. <br>
<br>

[`Top`](#top)

---
[^1]: 
[^2]: 
[^3]: 
[^4]: 
[^5]: 
[^6]: 
[^7]: 
[^8]: 
[^9]: 
[^10]: 
[^11]: replay buffer를 사용하여 데이터를 저장하고 off-policy처럼 동작. targer network를 두어 학습시 정답을 잠시 보관.
[^12]: optimize a stochastic policy in an off-policy. introduce the entropy-regularized RL setting. [참고](https://spinningup.openai.com/en/latest/algorithms/sac.html#quick-facts)
[^13]: 순진무구한.
[^14]: 일반화. 학습된 환경의 제약에서 벗어나 일반적인 성능을 가지는 것. [참고](http://dmqm.korea.ac.kr/activity/seminar/314)
[^15]: 강인제어. 시스템의 불확실성이 존재하는 경우, 일정한 한도 이내의 불확정성에 의해 시스템의 stability와 performance가 영향을 받지 않는 고정된 제어기를 설계. [참고](https://ko.wikipedia.org/wiki/%EC%A0%81%EC%9D%91_%EC%A0%9C%EC%96%B4)
[^16]: 적응제어. 시스템의 불확실성이 존재하는 경우, 제어기나 파라미터를 적절하게 변경. 미지항에 의한 제어 오차를 측정하고 이를 이용하여 적절하게 제어 파라미터를 변환. [참고](https://ko.wikipedia.org/wiki/%EC%A0%81%EC%9D%91_%EC%A0%9C%EC%96%B4)
[^17]: the ability of a researcher to duplicate the results of a prior study. [참고](https://media.neurips.cc/Conferences/NIPS2018/Slides/jpineau-NeurIPS-dec18-fb.pdf)
[^18]: reference [38].
[^19]: refernece [39]. 유전 알고리즘은 자연세계의 진화과정에 기초한 계산 모델로, 최적화 문제를 해결하는 기법 중 하나. [참고](https://ko.wikipedia.org/wiki/%EC%9C%A0%EC%A0%84_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)
[^20]: 전이학습. 한 문제를 해결하고자 얻은 지식과 정보를 다른 문제를 푸는데 사용하는 방식. 특히 컴퓨터 비전의 영역에서 전이 학습으로 수행된 모델들이 높은 성능을 보여, 많이 사용되고 있음.[참고](https://dacon.io/forum/405988)
