# Monte Carlo Methods
### Intro.
몬테카를로 방식부터는 환경에 대한 충분한 정보를 가지고 있다는 가정을 더 이상 하지 않는다
이 방식은 actual/simulated experience로부터 배운다. 그럼에도 불구하고 최적행동을 찾는 강력한 알고리즘이다
<br><br>
물론 모델은 필요하다. 하지만, 이 모델은 전체 possible transition에 대한 확률분포를 알 필요가 없다. 
필요한 것은 단지 sample transition을 생산할 수 있는 모델이다.
<br><br>
이 방식은 Average sample return 방식을 이용해 RL 문제를 해결하는 종류의 일부이다
다만 몬테카를로는 episodic 한 상황에서만 정의된다
Episode가 끝났을 때, value function과 policy가 갱신되며 따라서 episode-by-episode 방식의 scheme이다 [Complete Return]

### 5.1 Monte Carlo Prediction
여느 Prediction method in RL 이 그렇듯
policy는 주어졌으며 고정, 이상태에서 stat-value function을 구한다
<br><br>
First-visit MC vs. Every-visit MC
- F - MC : episode 내 가장 처음 발견된 S = s 에 대해서 expected return을 update에 사용
- E - MC : episode 내 발견되는 S = s 마다 expected return을 update에 사용
<br>
F - MC의 경우 각 return 은 i.i.d를 만족한다<br>
각 S의 평균 state-value는 unbiased 이지만 finite variance가 존재<br>
- unbiased measure인 이유는 fixed policy에 대한 average sampling 은 sample의 갯수가 무한대에 가까워지면 실제 모평균과의 차이가 0에 수렴
- variance는 1/root(n) 이기에, sampling이 늘어날수록 줄어듬
- 결국 수렴
<br>
몬테카를로의 특징<br>
- N(S) 와 무관하게 episode 내에 있는 state만 이용한다
  - 이를 이용하면 update하고 싶은 S = s 가 있을때, 이 s를 시작점으로 하는 episode를 sampling하게 되면 N(S) 의 크기에 관계없이 target state 업데이트가 가능
  - 결국 원하는 특정 subset만 update하기에 매우 용이하고, 따라서 computational expense를 많이 줄임
- 각 state 별 estimate들은 독립적이다
  - DP 에서는 다음 step에서의 state-value를 통해 현재 step의 state-value를 update. dependent
  - 그러나 여기서는 각 state-value는 상관없이 episode의 experience에 대한 각 expected reward를 가지고 update
  - __No bootstraping - No estimation from estimation__

### 5.2 Monte Carlo Estimation of Action Values
만약 모델이 없다면, v(s) 만으로 충분하지 않다. 모델이 없다면, state-action value 를 estimation 하는 방식으로 접근해야함<br>
근데 문제는 policy가 deterministic인 경우 특정 S=s 에 대해 항상 같은 A=a만 선택하게 된다. 그렇다면 exploration을 못하게 되는 맹점이 있음. 이는 maintainin exploration 문제에 속하는데, 이를 해결하기 위해 __exploring starts__ 방식을 사용해서 해결<br>
하지만, 대부분의 실제환경에서 exploring starts는 가능하지 않다. 따라서 __stochastic policies with non-zero prob for all actions__ 방식으로 접근

### 5.3 Monte Carlo Control
Evaluation - Improvement 방식은 두가지 맹점이 존재
- evaluation에서 infinite loop를 통한 optimal value 도출은 현실적으로 불가능
- exploring start 도 사실 말이 안됨
<br>
이중 infinite loop를 해결하는 방식은 good old Value iteration 방식인, Monte Carlo with Exporing Start<br>
- 첫번째 차이는 exploring start 부분이 삽입
- 두번째 차이는 매 episode에 first visit 을 확인한 후 해당 q(s,a)에 대해 argmax 를 해줘서 policy update을 함
<br>
Avoiding convergence to Suboptimal policy<br>
- policy k 가 매번 바뀌면서 새로운 episode의 experience는 전 episode와 다른 p(s,a)를 가지게 되고 따라서 특정 policy k에 대한 convergence가 나올 수 없음

### 5.4 Monte CArlo Control without ES
ES방식에서 벗어나기 위해서는 두가지 방식이 있다
- On-policy : experience search (action selection) 에 사용하는 policy를 그대로 업데이트, 그리고 다시 search에 사용
- Off-policy
<br>
On-policy F-MC control<br>
- 다른건 다 같지만, policy update를 greedy한걸 state에 deterministic하게 정해주는게 아니라
- e-soft 방식으로 update 해줌
<br>
하지만 On-policy 방식은 convergence를 해도 epsion-policy space 에서의 convergence이기에 optimal policy는 되지 못한다

### 5.5 Off-policy Prediction via Importance Sampling
위의 on-policy 방식의 한계를 벗어나기 위해, Off-policy는 두개의 policy를 사용한다. behavior policy for search, target policy for learning

### Sample Q&A
(5.7)<br>
__Q :__ off-policy MC control 에서 non-greedy한 행동이 많을 때 초반 상태들의 학습이 느려지는 이유는 importance-sampling ratio가 작아져서라고 생각하면 될까요?<br>
__A :__ Target policy는 greedy deterministic selection을 하는 반면 behavior는 non-greedy selection을 하는경우가 많으면 π(a|s) = 0 이 돼서 해당 (s,a) pair부터 앞쪽까지는 importance sampling ratio가 다 0이 됩니다.
terminal state, action pair부터 역순으로 루프가 돌아가는데 초반은 importance × G 가 다 0이되어버려서 학습이 아예 안되는 문제점이 생겨서 학습이 느려지는것 같습니다 <br>
<br>
__Q :__ policy가 다른데 왜 state action의 흐름을 똑같이 정의하고 term을 삭제해버렸는지 모르겠습니다. state action의 흐름을 파이에 맞춰놓고 bias에 따른확률을 dominater에 넣은걸까요?<br>
__A :__ 음 policy가 달라도 두 policy의 인자가 되는 {S} 와 {A} 는 같으니깐 state action의 흐름을 똑같이 정의하는것은 문제가 되지 않습니다. 다만 behavior는 target을 coverage로 두지만, target은 behavior를 coverage로 두지않죠. state action sequence를 target에 맞추는것과, behavior에 따른 확률을 분모에 넣는건 importance sampling method가 5.4 식의 Expectation에서 expectation 요소인 
