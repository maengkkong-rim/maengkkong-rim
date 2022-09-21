---
layout: post
author: jnr
use_math : true
published: true
categories: paper-review
tags: SA_CADRL social-norm multiagent symmetrical-NN robot
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
Robots should navigate efficiently and safely, but it is challenging because pedestrians tend to follow subtle social norms that are difficult to quantify, and pedestrians' `intents are usually not known`. A common approach treats pedestrians as dynamic obstacles with simple kinematics, but it sometimes generates unsafe/unnatural movements. And separating the navigation problem into disjoint prediction and planning steps can lead to the `freezing robot problem`, in which the robot fails to find any feasible action because the predicted paths could mark a large portion of the space untraversable. A key to resolving this problem is to account for cooperation, that is, to model/anticipate the impact of the robot's motion on the nearby pedestrians. <br>

Existing work on cooperative, socially compliant navigation can be broadly classified into two categories, namely model-based and learning-based. `Model-based` approaches are typically extensions of multiagent collision avoidance algorithms, with additional parameters introduced to account for social interactions. They are designed to be computationally efficient as they often correspond to intuitive geometric relations; yet, it is `unclear whether humans do follow` such precise `geometric rules`. In particular, the force parameters often `need to be tuned individually`, and can vary significantly for different pedestrians. Also, it has been observed that model-based methods can lead to `oscillatory paths`. <br>

In comparison, `learning-based` approaches aim to develop a policy that emulates human behaviors by matching feature statistics. Compared with model-based approaches, their paths `resemble human behavoirs` more closely, but often at a much `higher computational cost`. This is because computing/matching trajectory features often requires anticipating the joint paths of all nearby pedestrians, and might depend on some unobservable information. More importantly, since human behaviors are inherently stochastic, the `feature statistics` calculated on pedestrians' paths `can vary` significantly. This raises concerns over whether such feature-matching methods are generalizable to different environments. <br>

In short, existing works are mostly focused on modeling and replicating the detailed mechanisms of social compliance, which remains difficult to quantify due to the stochasticity in people's behaviors. The main contributions of this work are i) `introducing` socially aware collision avoidance with deep RL(`SA-CADRL`) for explaining/inducing socially aware behaviors, ii) `generalizing to multiagent`(n>2) scenarios through developing a symmetrical NN structure, and iii) `demonstrating on robotic hardware` autonomous navigation at human walking speed in a pedestrian-rich environment.
<br><br>

<h2 id="bac">Background</h2>
💡 [collision avoidance with deep RL]
- $s_t$: an agent's state at time t. the state vector is partitioned into observable and unobservable parts. $s_t=[s_t^o, s_t^h]$
- $u_t$: an agent's action at time t. let the action be the agent's velocity. $u_t=v_t$
- $\tilde{s_t}$: state of a nearby agent
- $s^o$: let the observable states be the agent's position, velocity, and radius. $s^o=[p_x, p_y, v_x, v_y, r]\in\mathbb{R}^5$. obtained from sensor measurements
- $s^h$: let the unobservable states be the agent's intended goal position, preferred speed, and orientation. $s^h=[p_{gx}, p_{gy}, v_{pref}, \psi]\in\mathbb{R}^4$
- $s^{jn}$: an agent's joint configuration with its neighbor. $s^{jn}=[s, \tilde{s}^o]$
- $R_{col}(s^{jn}, u)$: reward function. for reaching its goal and penalize the agent for colliding with others
- $P(s_{t+1}^{jn}, s_t^{jn}\|u_t)$: the unknown state-transition model. take into account the uncertainty in the other agent's motion due to its hidden intents ($\tilde{s}^h$) <br>

The objective is to develop a policy, $\pi:(s_t, \tilde{s_t}^o)\mapsto{u_t}$, that minimizes the expected time to goal $E[t_g]$ while avoiding collision with nearby agents, where (2) is the collision avoidance constraint, (3) is the goal constraint, (4) is the agent's kinematics, and the expectation in (1) is with respect to the other agent's unobservable states and policy.(Fig. 1) <br>

![Fig. 1](images/2022-09-21-1.PNG) <center>Fig 1: constraints, kinematics, and expectation</center> <br>

Solving the RL problem amounts to finding the optimal value function that encodes an estimate of the expected time to goal, (5). The optimal policy can be retrieved from the value function, (6).(Fig. 2) <br>

![Fig. 2](images/2022-09-21-2.PNG)
![Fig. 2-2](images/2022-09-21-3.PNG) <center>Fig 2: optimal value function and optimal policy</center> <br>

A major challenge in `finding the optimal value function` is that the joint state $s^{jn}$ is a continuous, high-dimensional vector, making it impractical to discretize and enumerate the state space. Several recent works have applied `deep RL` to motion planning, they are mainly focused on `single agent` navigation in unknown static environments, and with an emphasis on `computing control inputs` directly from raw sensor data, like camera images. In contrast, this work extends the collision avoidance with deep RL framework(`CADRL`) to `characterize and induce socially aware behaviors in multiagent systems`.
<br><br><br>

💡 [characterization of social norms] <br>
Rather than trrying to quantify human behaviors directly, this work notes that the complex normative motion patterns can be a consequence of simple local interactions. Reciprocity does not require a unique set of navigation rules, since both the left-handed and the right-handed rules can resolve path conflicts as shown in Fig.3. This work notes that cooperative and time-efficient properties are encoded in the CADRL formulation through using the min-time reward function and the reciprocity assumption($\tilde{\pi}=\pi$). 







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
