이전 글에서 유도한 오일러-라그랑주 방정식을 물리학에 적용한다. 이는 '최소 작용의 원리'를 통해 이루어지며, 뉴턴 역학의 벡터 기반 접근법을 에너지라는 스칼라 기반 접근법으로 대체하는 라그랑주 역학의 기초가 된다. 본 문서에서는 라그랑지안을 정의하고, 이를 이용해 주요 물리계의 운동 방정식을 유도한다.

---

#### **1. 물리적 원리: 최소 작용의 원리**

자연 현상은 특정 물리량이 극값(주로 최솟값)을 갖는 경로를 따라 발생한다는 변분 원리가 물리학 전반에 걸쳐 나타난다. 라그랑주 역학은 **작용(Action)** 이라 불리는 물리량을 기반으로 한다.

작용 $S$는 '라그랑지안(Lagrangian)' $L$이라는 함수를 시간에 대해 적분한 함수널로 정의된다.

$$
S[q] = \int_{t_1}^{t_2} L(q, \dot{q}, t) \,dt
$$

**최소 작용의 원리 (Principle of Least Action):**
> 물리계는 작용 $S$를 최소화(정확히는 정상화, stationary)하는 경로 $q(t)$를 따라 움직인다.

이 원리는 변분법의 문제와 수학적으로 동일한 구조를 갖는다. 작용 $S$가 극값을 가져야 하므로, 1차 변분 $\delta S$는 0이 되어야 한다.

$$
\delta S = 0
$$

따라서 작용 $S$의 피적분함수인 라그랑지안 $L$은 이전 글의 함수 $F$와 동일한 역할을 수행한다. 변수들을 다음과 같이 대응시킬 수 있다.

-   독립 변수 $x \rightarrow$ 시간 $t$
-   종속 함수 $y(x) \rightarrow$ 일반화 좌표 $q(t)$
-   함수 도함수 $y'(x) \rightarrow$ 일반화 속도 $\dot{q}(t)$
-   피적분함수 $F \rightarrow$ 라그랑지안 $L$

결과적으로, 오일러-라그랑주 방정식은 물리계의 운동을 기술하는 **라그랑주의 운동 방정식**이 된다.

> $$
 \frac{\partial L}{\partial q} - \frac{d}{dt}\left(\frac{\partial L}{\partial \dot{q}}\right) = 0
 $$

---

#### **2. 라그랑지안의 정의**

대부분의 역학계에서 라그랑지안 $L$은 계의 총 운동 에너지 $T$에서 총 위치 에너지 $V$를 뺀 값으로 정의된다.

> $$
 L = T - V
 $$

라그랑주 역학의 장점은 힘, 가속도 등 벡터량을 직접 다루는 대신, 에너지라는 스칼라량을 통해 운동 방정식을 유도한다는 점이다. 이는 복잡한 좌표계나 구속 조건이 있는 문제에 특히 강력한 방법론을 제공한다.

---

#### **3. 운동 방정식 유도 예시**

##### **3.1. 용수철 진동자 (단조화 진동자)**

-   **계 설정:** 질량 $m$의 물체가 용수철 상수 $k$인 용수철에 연결되어 있다. 평형점으로부터의 변위를 일반화 좌표 $x$로 둔다.
-   **운동 에너지 (T):** $T = \frac{1}{2}m\dot{x}^2$
-   **위치 에너지 (V):** $V = \frac{1}{2}kx^2$
-   **라그랑지안 (L):**
    $$
    L = T - V = \frac{1}{2}m\dot{x}^2 - \frac{1}{2}kx^2
    $$
-   **라그랑주 방정식 적용:**
    -   $\frac{\partial L}{\partial x} = -kx$
    -   $\frac{\partial L}{\partial \dot{x}} = m\dot{x} \implies \frac{d}{dt}\left(\frac{\partial L}{\partial \dot{x}}\right) = m\ddot{x}$
-   **운동 방정식:**
    $$
    (-kx) - (m\ddot{x}) = 0 \implies m\ddot{x} + kx = 0
    $$
    이는 단조화 진동자의 운동 방정식이다.

##### **3.2. 단진자 (Simple Pendulum)**

-   **계 설정:** 질량 $m$의 물체가 길이 $l$인 줄에 매달려 있다. 연직선과의 각도를 일반화 좌표 $\theta$로 둔다.
-   **운동 에너지 (T):** 물체의 속력은 $v = l\dot{\theta}$이므로, $T = \frac{1}{2}mv^2 = \frac{1}{2}ml^2\dot{\theta}^2$.
-   **위치 에너지 (V):** 최저점을 위치 에너지 기준점(0)으로 하면, 높이 $h = l(1-\cos\theta)$이다. 따라서 $V = mgh = mgl(1-\cos\theta)$.
-   **라그랑지안 (L):**
    $$
    L = T - V = \frac{1}{2}ml^2\dot{\theta}^2 - mgl(1-\cos\theta)
    $$
-   **라그랑주 방정식 적용:**
    -   $\frac{\partial L}{\partial \theta} = -mgl\sin\theta$
    -   $\frac{\partial L}{\partial \dot{\theta}} = ml^2\dot{\theta} \implies \frac{d}{dt}\left(\frac{\partial L}{\partial \dot{\theta}}\right) = ml^2\ddot{\theta}$
-   **운동 방정식:**
    $$
    (-mgl\sin\theta) - (ml^2\ddot{\theta}) = 0 \implies \ddot{\theta} + \frac{g}{l}\sin\theta = 0
    $$
    이는 단진자의 비선형 운동 방정식이다.

##### **3.3. 이중 진자 (Double Pendulum)**

-   **계 설정:** 질량 $m_1$, 길이 $l_1$의 첫 번째 진자에 질량 $m_2$, 길이 $l_2$의 두 번째 진자가 연결되어 있다. 각 진자의 연직선과의 각도를 일반화 좌표 $\theta_1$, $\theta_2$로 둔다.
-   **좌표 및 속도:**
    -   $m_1$: $(x_1, y_1) = (l_1\sin\theta_1, -l_1\cos\theta_1)$
    -   $m_2$: $(x_2, y_2) = (l_1\sin\theta_1 + l_2\sin\theta_2, -l_1\cos\theta_1 - l_2\cos\theta_2)$
    -   $v_1^2 = \dot{x}_1^2 + \dot{y}_1^2 = l_1^2\dot{\theta}_1^2$
    -   $v_2^2 = \dot{x}_2^2 + \dot{y}_2^2 = l_1^2\dot{\theta}_1^2 + l_2^2\dot{\theta}_2^2 + 2l_1l_2\dot{\theta}_1\dot{\theta}_2\cos(\theta_1-\theta_2)$
-   **운동 에너지 (T):** $T = \frac{1}{2}m_1v_1^2 + \frac{1}{2}m_2v_2^2$
    $$
    T = \frac{1}{2}(m_1+m_2)l_1^2\dot{\theta}_1^2 + \frac{1}{2}m_2l_2^2\dot{\theta}_2^2 + m_2l_1l_2\dot{\theta}_1\dot{\theta}_2\cos(\theta_1-\theta_2)
    $$
-   **위치 에너지 (V):** $V = V_1 + V_2 = m_1gy_1 + m_2gy_2$
    $$
    V = -(m_1+m_2)gl_1\cos\theta_1 - m_2gl_2\cos\theta_2
    $$
-   **라그랑지안 (L):** $L=T-V$. 위 식들을 조합하여 얻는다.
-   **라그랑주 방정식 적용:** $\theta_1$과 $\theta_2$에 대해 각각 방정식을 적용한다. 계산 과정이 복잡하므로 결과만 기술한다.
-   **운동 방정식:**
    1.  **($\theta_1$에 대한 방정식):**
        $$
        (m_1+m_2)l_1^2\ddot{\theta}_1 + m_2l_1l_2\ddot{\theta}_2\cos(\theta_1-\theta_2) + m_2l_1l_2\dot{\theta}_2^2\sin(\theta_1-\theta_2) + (m_1+m_2)gl_1\sin\theta_1 = 0
        $$
    2.  **($\theta_2$에 대한 방정식):**
        $$
        m_2l_2^2\ddot{\theta}_2 + m_2l_1l_2\ddot{\theta}_1\cos(\theta_1-\theta_2) - m_2l_1l_2\dot{\theta}_1^2\sin(\theta_1-\theta_2) + m_2gl_2\sin\theta_2 = 0
        $$

이 연립 미분 방정식은 이중 진자의 복잡하고 혼돈적인(chaotic) 움직임을 완벽하게 기술한다.