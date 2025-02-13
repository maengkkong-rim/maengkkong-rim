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
📝 Y. F. Chen, M. Everett, M. Liu, and J. P, " Socially aware motion planning with deep reinforcement learning," in Proc. IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS),  Vancouver, BC, Canada, Sep. 2017, pp. 1343–1350. [link](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8202312)[^1] <br>

📌 [`Abstract`](#abs) [`Intro`](#int) [`Background`](#bac) [`Approach`](#app) [`Results & Discussion`](#res) [`Conclusion`](#con) <br><br>

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

In short, existing works are mostly focused on modeling and replicating the detailed mechanisms of social compliance, which remains difficult to quantify due to the stochasticity in people's behaviors. The main contributions of this work are i) `introducing` socially aware collision avoidance with deep RL(`SA-CADRL`-socially aware multiagent collision avoidance with deep RL algorithm) for explaining/inducing socially aware behaviors, ii) `generalizing to multiagent`(n>2) scenarios through developing a symmetrical NN structure, and iii) `demonstrating on robotic hardware` autonomous navigation at human walking speed in a pedestrian-rich environment.
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

The objective is to develop a policy, $\pi:(s_t, \tilde{s_t}^o)\mapsto{u_t}$, that minimizes the expected time to goal $E[t_g]$ while avoiding collision with nearby agents, where (2) is the collision avoidance constraint, (3) is the goal constraint, (4) is the agent's kinematics, and the expectation in (1) is with respect to the other agent's unobservable states and policy.(Fig. 1) <br><br>

![Fig. 1](images/2022-09-21-1.PNG) <center>Fig 1: constraints, kinematics, and expectation</center> <br>

Solving the RL problem amounts to finding the optimal value function that encodes an estimate of the expected time to goal, (5). The optimal policy can be retrieved from the value function, (6).(Fig. 2) <br>

![Fig. 2](images/2022-09-21-2.PNG)
![Fig. 2-2](images/2022-09-21-3.PNG) <center>Fig 2: optimal value function and optimal policy</center> <br>

A major challenge in `finding the optimal value function` is that the joint state $s^{jn}$ is a continuous, high-dimensional vector, making it impractical to discretize and enumerate the state space. Several recent works have applied `deep RL` to motion planning, they are mainly focused on `single agent` navigation in unknown static environments, and with an emphasis on `computing control inputs` directly from raw sensor data, like camera images. In contrast, this work extends the collision avoidance with deep RL framework(`CADRL`[^2]) to `characterize and induce socially aware behaviors in multiagent systems`.
<br><br><br>

💡 [characterization of social norms] <br>
Rather than trying to quantify human behaviors directly, this work notes that the complex normative motion patterns can be a consequence of simple local interactions. Reciprocity does not require a unique set of navigation rules, since both the left-handed and the right-handed rules can resolve path conflicts as shown in Fig.3. This work notes that cooperative and time-efficient properties are encoded in the CADRL formulation by using the min-time reward function and the reciprocity assumption($\tilde{\pi}=\pi$). <br><br>

![Fig. 3](images/2022-09-21-4.PNG) <center>Fig 3: symmetries in multiagent collision avoidance</center> <br>

It was interesting to observe that while `no behavioral rules` were imposed in the problem formulation, CADRL policy `exhibits certain navigation conventions`, as illustrated in Fig. 4.  As the offset increases, the CADRL agents eventually change passing direction in favor of shorter, smoother paths. However, the cooperative behaviors emerging from a CADRL solution are `not consistent with human interpretation`. Moreover, the cooperative behaviors of CADRL `cannot be controlled` - they are largely dependent on the initialization of the value network and set of randomly generated training test cases. The next section will address this issue and present a method to induce behaviors that respect human social norms. <br><br>

![Fig. 4](images/2022-09-21-5.PNG) <center>Fig 4: indications of a navigation convention from the CADRL policy</center>
<br><br>

<h2 id="app">Approach</h2>
The following presents the socially aware multiagent collision avoidance with deep RL algorithm(SA-CADRL). We first describe a strategy for shaping framework and then generalize the method to multiagent scenarios. <br><br>

💡 [inducing social norms] <br>
each agent's state is parameterized as:
- $s=[d_g, v_{pref}, v_x, v_y, \psi, r]$
- $s^o=[\tilde{p_x}, \tilde{p_y}, \tilde{v_x}, \tilde{v_y}, \tilde{r}, \tilde{d_a}, \tilde{phi}, \tilde{b_{on}}]$
- $d_g$: the agent's distance to goal. $d_g=\lVert{P_g-P}\rVert_2$
- $\tilde{d_a}$: the distance to the other agent. $\tilde{d_a}=\lVert{P-\tilde{P}}\rVert_2$
- $\phi$: the other agent's heading direction. $\phi=tan^{-1}(\tilde{v_y}/\tilde{v_x})$
- $b_{on}$: a binary flag indicating whether the other agent is real or virtual <br>

To `induce a particular norm`, a `small bias` can be introduced in the RL training process in favor of one set of behaviors over others. The advantage of this approach is that `violations` of a particular social norm are usually `easy to specify`; and this specification need not be precise. This is because the addition of a penalty breaks the symmetry in the collision avoidance problem, thereby favoring behaviors respecting the desired social norm. This work uses the following specification of a reward function $R_{norm}$(Fig. 5) for inducing the right-handed rules. An illustration of these three penalty sets is provided in Fig. 6. <br><br>

![Fig. 5](images/2022-09-21-6.PNG) <center>Fig 5: specification of a reward function</center> <br>
![Fig. 6](images/2022-09-21-7.PNG) <center>Fig 6: norm inducing reward function</center> <br>

- $q_n$: a scalar penalty
- $I(\cdot)$: the indicator function
- $\tilde{\phi_{rot}}$: the relative rotation angle between the two agents. $\tilde{\phi_{rot}}=tan^{-1}((\tilde{v_x}-v_x)/(\tilde{v_y}-v_y))$
- $\tilde{\phi}-\psi$: the angle difference($[-\pi, \pi]$)
- $\mathcal{S}_{norm}$: the parameters defining the penalty set. affect the rate of convergence

As long as training converges, the penalty sets' `size does not have a major effect on the learned policy`. This is expected because the desired behaviors are not in the penalty set. (9)-(12) in Fig. 5 can be modified to induce left-handed rules.
We trained two SA-CADRL policies to learn left-handed and right-handed norms starting from the same initialization, the results of which are shown in Fig. 7. The learned policies exhibited similar qualitative behaviors as shown in Fig. 3. Note that training is performed on randomly generated test cases, not validation test cases. <br>

![Fig. 7](images/2022-09-21-8.PNG) <center>Fig 7: SA-CADRL policies exhibiting socially aware behaviors</center>
<br><br>

💡 [training a multiagent value network] <br>
The CADRL solely performed on a two-agent system, so it was difficult to account for[^3] the relations between nearby agents. This work addresses this problem by developing a method that allows for training on multiagent scenarios directly. To `capture the multiagent system's symmetrical structure`, a `NN with weight-sharing` and `max-pooling layers` is employed, as shown in fig. 8. For a four-agent network shown in Fig. 8b, the three nearby agents' observed states can be swapped(blue input blocks) without affecting the output value. This condition is enforced through `weight-sharing`, as shown in Fig. 8a. Two symmetrical layers are used, followed by a max-pooling layer for aggregating features and two fully-connected layers for computing a scalar value. This work uses the rectified linear unit(ReLU) as the activation function in the hidden layers. <br><br>

![Fig. 8](images/2022-09-21-9.PNG) <center>Fig 8: Network structure for multiagent scenarios</center> <br>

The input to the n-agent network is a generalization of $s$ and $s^o$, $s^{jn}=[s, \tilde{s}^{o, 1}, ... \tilde{s}^{o, n-1}]$. The norm-inducing reward function is defined similarly as (9) in Fig. 5, where a penalty is given if an agent's joint configuration with the closest nearby agent belongs to the penalty set. The overall reward function is the sum of the original CADRL reward and the norm-inducing reward, that is, $R(\cdot)=R_{col}(\cdot)+R_{norm}(\cdot)$. <br>

The procedure for training a multiagent SA-CADRL policy is outlined in Algorithm 1. A value network is first initialized by training on an n-agent trajectory dataset through NN regression(line 1). Using this value network and following an $\epsilon$-greedy policy, a set of trajectories can be generated on random set cases(line 5-7). The trajectories are then turned into state-value pairs and assimilated[^4] into the experience sets $E$, $E_b$(bad experience set)(line 10-11). A subset of state-value pairs is sampled from the experience sets, and subsequently used to update the value network through back-propagation(line 12-13). The process repeats for a pre-specified number of episodes(line 3-4). <br><br>

![Al. 1](images/2022-09-21-al1.PNG) <center>Algorithm 1</center> <br>

Compared with CADRL, two important modifications are introduced in the training process. First, `two experience sets`, $E$, $E_b$, are used to `distinguish between trajectories` that reached the goals and those that ended in a collision(line 2, 11). $E_b$ improves the rate of learning by focusing on the scenarios that fared poorly for the current policy. Second, during the training process, `trajectories` generated by SA-CADRL are `reflected` in the x-axis with `probability` $\epsilon_f$(line 8). It could take a long time for an $\epsilon$-greedy policy to explore the state space and find an alternative topology. In particular, empirical results show that, without this procedure, policies can still exhibit the wrong passing side after 2000 training episodes. This procedure exploits symmetry in the problem to explore different topologies more `efficiently`. <br>

Furthermore, an n-agent network can be used to generate trajectories for scenarios with `fewer agents`(line 5). Non-existent agents can be filled by adding virtual agents - replicating the states of the closest nearby agent and set the binary bit $\tilde{b}_{on}$ to zero. The use of this parameterization avoids the need for training many different networks. A left-handed and right-handed four-agent SA-CADRL policies are trained using the network structure shown in Fig. 8. Sample trajectories generated by these policies are shown in Fig. 9, which demonstrate the preferred behaviors of each respective set of social norms. <br><br>

![Fig. 9](images/2022-09-21-10.PNG) <center>Fig 9: SA-CADRL policies generalized to multiagent scenarios</center>
<br><br>

<h2 id="res">Results and Discussion</h2>
💡 [computational details] <br>
- skip
<br><br>

💡 [simulation results] <br>
Three copies of four-agent SA-CADRL policies were trained, one wihtout the norm inducing reward $R_{norm}$, one with the left-handed $R_{norm}$, and the other with the right-handed $R_{norm}$. To demonstrate that SA-CADRL can balance between finding time-efficient paths and respecting social norms, these policies are further evaluated on randomly generated test cases. To ensure the test set if left-right balanced, each random test case is duplicated and reflected in the x-axis. <br>

The same four-agent SA-CADRL polices are used to generate trajectories for both the two-agent and the four-agent test sets. On the `two-agent` test set, `all RL-based` methods produced more `time-efficient` paths than ORCA[^5](Table 1). The `four-agent SA-CADRL(none)` policy exhibited a `stronger preference` than ORCA and CADRL in each of the passing, crossing, and overtaking scenarios. This observation suggests that i) `certain navigation conventions could emerge as a means` of resolving symmetrical collision avoidance scenarios, and ii) the `conventions don't always correspond to human social norms`. The `SA-CADRL` policies trained with $R_{norm}$, exhibited a `strong preference` of the respective social norm. Recall that this `ratio is not 1` because there is a `tradeoff` between time-optimality and social compliance, as shown in Fig. 4. This tradeoff can be controlled by tuning $q_n$ in (9) in Fig. 5. Evidently, SA-CADRL(lh/rh) achieves better social compliance at a cost of an approximately 20% larger $\bar{t_e}$, because satisfying the norms often requires traveling a longer path. <br>

Similarly, the bottom rows of table 1 show that in the `four-agent` test set, `all RL-based methods outperformed` ORCA, and `SA-CADRL(lh/rh)` exhibited behaviors that r`espect the social norms`. `CADRL` produced paths that are closer to time-optimal than the other algorithms, but sometimes came `very close to other agents`. This is because CADRL was trained on a two-agent system, so its action choice is dominated by the single closest neighbor; possibly leading CADRL to select an action that avoids the closest neighbor but drives towards a third agent. In contrast, `all SA-CADRL` policies were trained on four-agent systems and they all `maintained a larger average separation distance`. <br><br>

![Table 1](images/2022-09-21-t1.PNG) <center>Table 1. SA-CADRL's performance statistics on 200 randomly generated test cases</center>
<br><br>

💡 [hardware experiment] <br>
The SA-CADRL policy is implemented on a robotic vehicle for autonomous navigation in an indoor environment with many pedestrians. Pedestrian detection and tracking are achieved by combining Lidar's pointcloud data with camera images. The speed, velocity, and size of a pedestrian are estimated by clustering the point cloud data. <br>

Motion planning uses the diffusion map algorithm[^6] for finding global paths and SA-CADRL for local collision avoidance. In particular, the diffusion map algorithm considers static obstacles in the environment to find a subgoal within the vehicle's planning horizon. SA-CADRL takes in the set of detected pedestrians, and chooses an action from the feasible directions to move the vehicle toward the subgoal. <br>

Using this motion planning strategy, the vehicle was `able to navigate fully autonomously` in a dynamic indoor environment. In particular, the vehicle is issued randomly generated goals ten times, with an average distance between successive goals of more than 50 meters. During the experiment, the vehicle maintained safe distance from the pedestrians and generally respected social norms.
<br><br>

<h2 id="con">Conclusion</h2>
- This work presented SA-CADRL, a multiagent collision avoidance algorithm that considers and exhibits socially compliant behaviors. In particular, in a RL framework, a pair of simulated agents navigate around each other to learn a policy that respects human navigation norms. <br>
- This approach is further generalized to multiagent(n>2) scenarios through the use of a symmetrical NN structure. <br>
- Moreover, SA-CADRL is implemented on robotic hardware, which enabled fully autonomous navigation at human walking speed in a dynamic environment with many pedestrians. <br>
- Future work will consider the relationships between nearby pedestrians, such as a group of people who walk together. <br>
<br>

[`Top`](#top)

---
[^1]: reference [14]를 발전시킨 형태.
[^2]: collision avoidance with deep reinforcement learning framework. reference [14].
[^3]: 설명하다.
[^4]: 동화되다.
[^5]: specifies a reactive, geometric rule for computing a collision-free velocity vector. does not anticipate the evolution of an agent's state with respect to other agents nearby. can generate shortsighted actions and oscillatory paths. reference [14].
[^6]:  graph-based nonlinear dimensionality reduction technique that has many applications in data processing. reference [29]. 
