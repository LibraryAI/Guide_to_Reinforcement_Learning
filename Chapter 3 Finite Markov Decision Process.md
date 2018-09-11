# Finite Markov Decision Process

### Intro.
이 장에서는 Bandit problem 으로부터 다음 조건들이 추가된다
- Actions influence immdediate and also subsequent situations
- Multiple actions
- State-dependent quantities are introduced

### 3.1 The Agent-Environment Interface
Agent : A decision maker<br>
Environment : What agent interacts with, everyting outside agent<br>
p : S X R X S X A -> [0, 1] 은 p(s', r | s, a) 로 Real space to probability domain을 만족하는 ordinary deterministic function
- MDP에서 p 는  환경의 dynamics를 의미한다. 다른말로는 transition function 혹은 transition probability
  - p를 보면 conditional argument 가 s, a로 t-1 시점의 s와 a에만 해당. 이는 Markov Property를 state에 만족하는 상황으로 제한함
  - 따라서, state configuration을 구상할 때, Markov property를 만족시키는 것을 염두에 둬야함
- p(s' | s, a) 는 p(s', r | s, a) 에서 ∀ r ∈ R 을 summing 혹은 integration해준 function(probability)
- r(s,a) = E[Rt | s, a] 는 ∑[r×p(r | s, a)] 로 ∑[r×∑p(s', r | s, a)]
- r(s,a,s') = E[Rt | s, a, s'] 는 조건부수식 전개하면 3.6식과 같이 나옴

__MDP는 결국 agent 와 environment 간의 interaction을 세개의 signal의 형태로 reduction 한 abstraction이다.__ <br>
Action of the agent, State where action decision is made, and Reward for the goal 의 형태로

### 3.2 Goals and Rewards
__Agent의 목표는 Reward signal의 총합을 최대로 얻는 것__<br>
__<->__ 일반적으로 Machine Learning 알고리즘들의 목표는 target label과 approximated value 간의 loss를 최소로 하는 것 (MLE)
- Agent의 목표는 최대 sum of reward signal을 얻는 것이지만
- Reward set을 정의 할 때, 주의해야할 점은
  - Reward를 subgoal에 할당해, optimal goal 을 subgoal의 optimization을 통해서 달성하려 하면 안된다는것
  - Subgoal(Suboptimal structure)를 고려하는 것은, How to achieve 를 설계하는 것인데, 이는 optimal goal과 다른 approximation으로 나아갈 수 있다
  - 항상 What you want to achieve 를 고려해서 설계할 것

### 3.3 Returns and Episodes
Formal definition of Goal is defined <br>
G = r1 +r2 + r3 +... <br>
Episode is introduced
- Sequence of states with terminal state
- time of Termination T is random variable
Continuing task is introduced
- Sequence of state with no identifiable episodes
- T = ∞
Discounted Reward is introduced
- Sum of Discounted return is finite
  - if discout rate < 1
  - and reward is nonzero and constant (of course when zero it is finite or zero)
- Myopic <-> Far-sighted
  - discount rate ∈ [0, 1] 의 값에 따라 얼마나 많은 subsequent state까지 고려할지를 결정

### 3.4 Unified Notation for Episodic and Continuing Tasks
Continuting taks 와 episodic task를 모두 represent 할 수 있는 notation과 diagram
- Observing state가 도입되며, episodic task 에서는 terminal 이후의 reward 에 대해서는 0를 부여, 따라서 Expected reward or goal value is not changed and observed into continuing format
- T = ∞ or discount rate = 1 의 경우 포괄하는 Gt

### 3.5 Policies and Value Functions
Value function is introduced
- Value function은 어떤 state s, 혹은 어떤 (state s, action a) pair 라는 상태에 대한 값을 도출하는 걸 의미
  - value function ≡ E[ G | s ]
  - paired value function ≡ E[ G | s, a ]
- 여기서 Expected return 은 r(s) 혹은 r(s,a)
  - r(s) = ∑p(a)r(s, a)
  - 즉, r(s) 나 r(s, a) 나 action a 가 고려됨
  - 각 state s 에서 random variable action a 에 대한 선택은 p.d.f 를 가지게 되고, 이 p.d.f 를 policy 파이라고 부르며, r(s), r(s,a) 는 이 policy에 dependent
  - Value of terminal state = 0, 그다음 expected return at terminal state 은 없기 때문에
- Value function 혹은 Q function은 어떻게 구할까?
  - Experience로부터 estimation 할 수 잇음
    - K-bandit 에서 봤듯, 각 episode에서 방문하는 state로부터 얻은 sum of discounted rewards 혹은 expected return을 episode를 반복하면서 평균내면 optimal value 로 converge 함
    - 물론, step size condition 등 convergence condition을 만족하는 경우. 
  - 물론 모든 state, 혹은 state-action pair에 대해서 이런 값들을 다 저장할 수는 없음
    - Tabular environment 의 경우 가능한 경우도 있지만, 이건 state 수 혹은 state-action pair의 수 만큼의 parameter를 필요로 하게 됨
    - 마치 Naive Bayes에서 모든 경우에 대한 likelihood를 저장하고 있는것처럼, 이것은 말도안됨
    - 따라서 approximation을 사용하게됨

### 3.6 Optimal Policies and Optimal Value function
Value function은 policy dependent 하다. Policy에 의한 decision making 에 의해서 value of the states(state-action)이 변하기 때문
- Recall policy is a function of state input to probability of actions output
- 분포의 차이에 따라서 value 값이 달라짐
- Better policy 란 모든 state s에 대해서 V(s) >= V(s) 인것
Optimal policy는 존재하기는 하는데,
- Optimal policy는 여러개일 수 있음
- 여러 분포가, 즉 여러 action이 같은 value 값을 야기할 수 있다는 것
- 하지만 optimal value function은 하나
- 이 말은 어떤 행동을 하든 최고의 return 을 보장하면 머신은 신경 안 쓴다는것
