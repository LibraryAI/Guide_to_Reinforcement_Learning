# Multi-Armed Bandits
### Intro.
Multi-armed bandit 알고리즘은 K-armed bandit 알고리즘으로 불리기도 하며, 강화학습의 action-value update method, 
exploration/exploitation trade-off, 효율적 탐색과 같은 주요 주제를 매우 간단한 환경에서 설명하는 좋은 예시이다. <br>
강화학습의 evaluative feedback 방식과 지도학습의 instructive feedback의 차이에 주목해서 보면 좋을 것 같다. <br>
<br>
_Pure evaluative iteration 방식은 지도학습과 달리 instructor 가 없기 때문에_
> __"Exploration is mandatory to evaluate all and do it right"__

### 2.1 K-armed bandit problem
- Nonassociative, evaluative feedback problem
- S : 1개의 주어진 상황(state) , A : {a1 ... ak) (k개의 action) R : {p1, ... , pk} (k개의 reward distribution)
- 한 번에 하나의 action 만 취할 수 있음
- <a href="http://www.codecogs.com/eqnedit.php?latex=p(r_i)" target="_blank"><img src="http://latex.codecogs.com/gif.latex?p(r_i)" title="p(r_i)" /></a> = mean 과 variance 로 이루어짐 (가우시안)
- <a href="http://www.codecogs.com/eqnedit.php?latex=q^{*}(a)&space;=&space;E[R_t|A_t&space;=&space;a]&space;=&space;\sum&space;[p(r_i)*r_i]" target="_blank"><img src="http://latex.codecogs.com/gif.latex?q^{*}(a)&space;=&space;E[R_t|A_t&space;=&space;a]&space;=&space;\sum&space;[p(r_i)*r_i]" title="q^{*}(a) = E[R_t|A_t = a] = \sum [p(r_i)*r_i]" /></a>, 굉장히 많은 실행을 했을 때, asymptotically converge 한 optimal value
- 각 a에 대한 optimal q*(a) 를 모르기 때문에,
  - 현재 가장 큰 value를 가지는 greedy action이 best action이 아닐 uncertainty가 존재
  - 이 때, 다른 걸 해볼거냐 -> exporation
  - 그래고 그냥 greedy한거 할래 -> exploitation
- 이 책은 balancing exploration, expoitation problem에 대해서 깊게 접근하지않음

### 2.2 Action-value methods
- Action-value method는 action의 value를 estimate 하고, 그 estimation을 가지고 decision selection을 하는 method들의 통칭
- True action value 는 mean reward of action selected
- sample-average method : Sum of sample rewards of a_i  / Sum of count of a_i
- Greedy behavior : 항상 argmax 를 선택하는 behavior <-> e-greedy : probability e 만큼 random action selection (near greedy)
  - Asymptotically optimal action converge를 하더라도 e 만큼 다른걸 선택해야하는 단점
  
### 2.3 The 10-armed Testbed
- Depending on Variance
  - Variance가 0이라면, 이것은 deterministic reward distribution
    - 따라서 한번의 exploration으로 모든 action에 대해 true action value를 얻을 수 있음
    - 이 경우 greedy method가 훨씬 효율적
  - Varinace가 크다면, greedy action이 안 좋은 action일 수 있기 때문에 exploration을 많이 해서 true action value를 알아봐야 함
- Depending on stationary assumption
  - Unstationary 한 reward distribution 이라면 (이 말은 reward 분포가 바뀔 수 있다는 말)
    - 현재의 greedy가 다음 step에서는 non-greedy할 수 있으므로, 계속 exploration을 해야함

### 2.4 Incremental Implementation
- Computationally efficient transform of sample average mtehod
- Q'' = Q' + (R' - Q')/n
- NewEstimate = OldEstimate + StepSize*[Target - OldEstimate]
- Target 은 noisy 할 수 있지만(variance) Estimate이 목표로하는 방향
- Sample Average method에서는 stepsize가 increase

### 2.5 Tracking a Nonstationary Problem
- Nonstationary problem 에서는 recent 한 reward에 distant reward 보다 더 큰 비중을 줘야한다
- 이를 위한 방법으로는 constant step size (whereas the other method was incremental step size)
  - sample average의 stepsize (1/n) 은 convergence condition을 모두 만족
반면, constant step size (weighted average) 는 두번째 condtion을 만족시키지 못함
  - 따라서, weighted average method 는 converge 하지 않지만, nonstationary problem에서는 converge 가 존재하기 힘들고, 빠르게 변화된 상황에 adapt 하는게 필요하기에 더 좋음
  - 게다가, sample average method 역시 convergence까지 사실은 엄청난 수의 trial이 필요하기에 emprically seldom used (theoretical)

### 2.6 Optimistic Inital Values
- 이 방법들은 inital estimate 에 의해 statistically biased
  - Sample avg method 는 all action execution 후 unbiased로 바뀌지만
  - Weighted avg method 는 계속해서 유지됨 (시간이 지날수록 미미하지만)
    - 식에서 볼 수 있음
    - Bias(theta) = E(theta) - True(theta)
    - 이 말은 E(theta) = True(theta) 가 된다면 unbiased, 결국 theat 가 asymptotically convergence를 달성하느냐는 뜻
- Initial Action value는 exploration을 용이하게 할 수 있음
  - Large optimistic action value를 할당한다면,
    - 실제 action value 보다 높기 때문에 어떤 action을 선택하든 그 action은 처음 몇번째visit 이 있을 때 까지는 greedy selection으로 선택되기 힘듬
    - Decaying period 혹은 exploration period 를 초기에 all action에게 준다는 것
  - 이건 실제로 stationary problem에서는 효과적임, 반면 nonstationary 에서는 어차피 환경은 바뀌기에 이런 초기 exploration으로 땡 끝나는 방법은 임시방편에 불과해서 그냥그럼
  
### 2.7 Upper-Confidence-Bound Action Selection
- e-greedy는 exploration을 수행하지만, all action을 대상으로 indiscriminately 수행한다. 따라서, 비효율적
- Nearly greedy 한 것을 prefer 하는 exploration이 더 효율적이지 않을까 하는 생각에서 나온 방법
- UCB Action Selection은 전체 action set의 action-value를 대상으로 ln(t) / N_t(a) 라는 preference measure를 주어서 이를 해결하려 함
- N_t(a) 는 특정 a가 얼마나 자주 selection 되었는지를 나타내기에, 너무 빈번하게 selection된 action의 경우 Q(a) 에 preference measure가 작은 반면, 선택이 적었던 action은 값이 커짐
- 이 preference measure term은 measure of uncertainty 라 생각할 수 있음
- 잘 방문안될 수록 uncertainty 가 높고, 대신 방문하게 되면 uncertainty가 reset 됨

### 2.8 Gradient Bandit Algorithm
- Gradient Bandit 은 각 action이 선택될 확률을 softmax of preference value로 구함
- 따라서, 전체 preference value의 magnitude는 영향없음
- Probability of selecting an action이 probability이기 때문에 근본적으로 exploratory method
- preference value를 update 하는데, 이는 현재 시점에서의 reward와 average reward를 비교해 reward(a, t)가 더 컸다면 해당 action의 preference는 키우고 나머지는 내리는 구조



  
