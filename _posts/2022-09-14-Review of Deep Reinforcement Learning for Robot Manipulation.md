---
layout: post
author: jnr
use_math : true
categories: paper-review
tags: concept sample-inefficiency exploration exploitation generalization reproducibility
---
---
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
<br>

<h2 id="bac">Background</h2>
💡 [KEY CONCEPTS AND TERMINOLOGY] <br><br>
![Fig. 1](images/2022-09-14.PNG) <center>Fig 1: The agent-environment interaction</center> <br>
There are four main elements of a RL system: <br>
a policy, a reward signal, a value function, and optionally a model of the environment.
- policy: defines the learning agent's way of behaving at a given time. mapping from the perceived states to actions to be taken when being in those states.
- reward signal: a reward is dependent on the current state and the action $r=R(s,a)$. the agent's ultimate goal is to maximize the cumulative reward  $G_t$=R_{t+1}+\gamma*R_(t+2)+\gamma^2*R_(t+3)+\cdots$ (discount rate $\gamma\in [0 1]$)
- model of the environment: allows inferences about how the environment will behave. the dynamics of the environment is fully characterized by a distribution $p$. <br><br>

Markov Decision Process(MDP) is a classical formalization of sequential decision making. <br>
It contains:
- a set of possible states $\mathcal{S}$
- a set of possible actions $\mathcal{A}$
- a reward function $R(s,a)\in\mathcal{R}$
- a probability distribution $p(s',r\|s,a)$ of the environment <br><br>

A value function specifies what is good in the long run of a state $s$ or a state-action pair $(s,a)$ when following a particular policy $\pi$.
- value function $V^\pi(s)=E_(a~\pi)[R(\tau)\|s_t=s]$
- action-value function $Q^(phi)(s,a)=E_(a~phi)[R(tau)|s_t=s, a_t=a]$
- optimal value function $V^(*)=max_phi E_(a~phi)[R(tau)|s_0=s]$
- optimal action-value function $Q^(*)(s,a)=max_phi E_(a~phi)[R(tau)|s_0=s, a_0=a]$

Given the optimal $Q^{*}(s,a)$, we can obtain the optimal action $a^(*)(s)=arg max_a Q^(*)(s,a)$ at a given state $s$, and then we can directly construct the optimal policy $\phi^{*}$. <br>

Bellman equation is obeyed by all four types of value functions.
- 


<br>

💡 [TAXONOMY OF RL ALGORITHMS]
<br>










<h2 id="app">Approach</h2>
💡 [sample inefficiency]

💡 [exploration and exploitation]

💡 [generalization and reproducibility]
<br>

<h2 id="res">Results and Discussion</h2>
The strongest motivation for future developments is how to efficiently take deep RL algorithms to the real world. From our perspective, agents/robots must learn `much faster and more efficiently`. <br>
`Model-based learning` has the biggest advantage of being sample-efficient and there existed interesting research in this direction to predict the future. <br>
There is still a `big gap between RL algorithms and humans` in terms of sample efficiency. We do not learn from scratch, and we can reuse our past knowledge to learn a new skill much more efficiently. Model-based learning approach can also help in this situation, thanks to its more potential transferability and generality. A different approach rather than approximating the dynamics is to use `multi-task learning` to reuse skills. The interesting thing in this work was learning on various tasks had better performance compared to learning in a single-task setting. <br>
Transfer learning[^1] tries to use experience from one set of tasks for faster learning and better performance on a new task. Transfer learning from tasks trained on simulators is particularly tempting as relatively cheap resources are needed. Another study performed `parallel learning` between simulated and real robots by introducing `additional alignment rewards` that encourage both agents in two domains to have similar distributions over visited states. <br>
`Inverse RL` is also promising, which can solve the nightmare of designing reasonable reward functions.
<br>

[^1]: 전이학습. 한 문제를 해결하고자 얻은 지식과 정보를 다른 문제를 푸는데 사용하는 방식. 특히 컴퓨터 비전의 영역에서 전이 학습으로 수행된 모델들이 높은 성능을 보여, 많이 사용되고 있음.

<h2 id="con">Conclusion</h2>
- Described RL algorithms used in robot manipulation <br>
- Adjusting RL on real robot applications is still limited <br>
- But RL in robot manipulation will have a promising future ahead <br>






## Blockquote

The following is a blockquote:

> Suspendisse tempus dolor nec risus sodales posuere. Proin dui dui, mollis a consectetur molestie, lobortis vitae tellus.

## Thematic breaks (<hr>)

Mauris viverra dictum ultricies[^3]. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod. **You can put some text inside the horizontal rule like so.**

---
{: data-content="hr with text"}

Mauris viverra dictum ultricies. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod. **Or you can just have an clean horizontal rule.**

---

Mauris viverra dictum ultricies. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod. Or you can just have an clean horizontal rule.

## Code
Now some code:

```
const ultimateTruth = 'follow middlepath';
console.log(ultimateTruth);
```

And here is some `inline code`! 

## Tables

Now a table:

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

## Images

![theme logo](http://www.abhinavsaxena.com/images/abhinav.jpeg)

This is an image[^4]

