 본 글은 고등학교 미적분학 지식을 기반으로 변분법의 핵심 원리를 설명하고, 최종적으로 오일러-라그랑주 방정식을 유도하는 것을 목표로 한다. 물리적 적용은 제외하고 수학적 개념에 한정하여 기술한다.

---

#### **1. 기본 개념 정의: 함수와 함수널**

고등 미적분학은 함수(Function)를 다룬다. 함수는 특정 숫자 $x$를 입력받아 다른 숫자 $f(x)$를 출력하는 규칙이다.

변분법은 여기서 개념을 확장하여 함수널(Functional)을 주요 대상으로 삼는다. 함수널은 특정 함수 $y(x)$ 자체를 입력받아 단일 숫자 $J[y]$를 출력하는 규칙이다.

> **함수:** 숫자 입력 → 숫자 출력 ($x \rightarrow f(x)$)
> **함수널:** 함수 입력 → 숫자 출력 ($y(x) \rightarrow J[y]$)

변분법의 목적은 함수널의 값을 극대 또는 극소로 만드는 최적의 함수 $y(x)$를 찾는 것이다. 일반적인 함수널은 다음과 같은 적분 형태로 정의된다.

$$
J[y] = \int_{a}^{b} F(x, y(x), y'(x)) \,dx
$$

여기서 $y(x)$와 그 도함수 $y'(x)$로 구성된 함수 $F$를 특정 구간에 대해 적분하면 그 결과는 단일한 숫자가 된다.

---

#### **2. 변분 원리: 함수의 미소 변화**

함수널 $J[y]$의 극값을 만드는 최적 함수 $y(x)$는 미분과 유사한 논리로 탐색한다.

어떤 함수 $y(x)$가 최적해라고 가정한다. 이 함수에 미소 변화(variation)를 가했을 때 $J$의 값이 어떻게 변하는지 관찰한다. 만약 $y(x)$가 참된 극값이라면, 임의의 미소 변화에 대해 $J$의 값은 1차적으로 변하지 않아야 한다.

미소 변화를 가한 함수는 다음과 같이 수학적으로 표현한다.

$$
y_{\text{new}}(x) = y(x) + \varepsilon\eta(x)
$$

-   $y(x)$: 기준이 되는 최적 후보 함수다.
-   $\eta(x)$ (에타): 변화의 '모양'을 결정하는 임의의 보조 함수다. 단, 경계점에서는 변화가 없다고 가정하므로 $\eta(a) = \eta(b) = 0$ 조건을 만족한다.
-   $\varepsilon$ (엡실론): 변화의 '크기'를 결정하는 무한히 작은 실수다.

분석의 핵심은 $\varepsilon$이 0에 근접할 때, $J[y_{\text{new}}]$와 $J[y]$의 차이를 $\varepsilon$에 대한 1차항으로 분석하는 것이다.

---

#### **3. 필요 도구: 테일러 1차 근사**

"테일러 전개"는 복잡한 함수를 특정 지점 근방에서 다항함수로 근사하는 기법이다. 변분법의 유도 과정에서는 1차 근사, 즉 접선의 방정식을 이용한 근사가 핵심적으로 사용된다.

함수 $f(x)$의 $x=a$ 지점 근방에서의 1차 테일러 근사는 다음과 같다.

$$
f(x) \approx f(a) + f'(a)(x-a)
$$

$x-a$를 미소 변화량 $h$로 치환하면 $f(a+h) \approx f(a) + h f'(a)$가 성립한다. 이는 변화량 $h$가 매우 작을 경우, 함수의 변화는 1차항(선형항)에 의해 지배된다는 것을 의미한다. 변분법에서 $\varepsilon$은 무한소이므로 이 1차 근사 개념이 직접적으로 적용된다.

---

#### **4. 핵심 단계: 다변수 함수의 1차 근사**

함수널 $J$의 변화량을 계산하려면 적분 내부의 $F(x, y+\varepsilon\eta, y'+\varepsilon\eta')$를 근사해야 한다. 이는 다음과 같은 논리를 통해 수행된다.

$x, y, y', \eta, \eta'$를 모두 상수로 취급하고, $\varepsilon$만을 변수로 갖는 새로운 1변수 함수 $g(\varepsilon)$를 정의한다.

$$
g(\varepsilon) = F(x, y+\varepsilon\eta, y'+\varepsilon\eta')
$$

이 함수 $g(\varepsilon)$에 $\varepsilon=0$을 기준으로 1차 테일러 근사를 적용한다.

$$
g(\varepsilon) \approx g(0) + \varepsilon g'(0)
$$

-   $g(0)$의 계산: $\varepsilon=0$을 대입하면 $g(0) = F(x, y, y')$가 된다.
-   $g'(0)$의 계산: $g(\varepsilon)$를 $\varepsilon$에 대해 미분한다. 이때 다변수 함수의 연쇄 법칙(Chain Rule)을 적용해야 한다.

$$
g'(\varepsilon) = \frac{\partial F}{\partial y}\frac{d(y+\varepsilon\eta)}{d\varepsilon} + \frac{\partial F}{\partial y'}\frac{d(y'+\varepsilon\eta')}{d\varepsilon} = \frac{\partial F}{\partial y}\eta + \frac{\partial F}{\partial y'}\eta'
$$

$\varepsilon=0$을 대입해도 결과는 동일하므로 $g'(0) = \frac{\partial F}{\partial y}\eta + \frac{\partial F}{\partial y'}\eta'$ 이다.

두 결과를 종합하여 $F$의 최종 근사식을 얻는다.

$$
F(x, y+\varepsilon\eta, y'+\varepsilon\eta') \approx F(x,y,y') + \varepsilon \left( \frac{\partial F}{\partial y}\eta + \frac{\partial F}{\partial y'}\eta' \right)
$$

---

#### **5. 최종 유도: 오일러-라그랑주 방정식**

$F$의 근사식을 함수널 $J$의 정의에 대입하여 1차 변화량 $\delta J$를 계산한다.

$$
J[y + \varepsilon\eta] \approx \int_{a}^{b} \left\{ F(x,y,y') + \varepsilon \left( \frac{\partial F}{\partial y}\eta + \frac{\partial F}{\partial y'}\eta' \right) \right\} dx
$$

$$
J[y + \varepsilon\eta] - J[y] \approx \varepsilon \int_{a}^{b} \left( \frac{\partial F}{\partial y}\eta + \frac{\partial F}{\partial y'}\eta' \right) dx
$$

$J$가 극값을 갖기 위한 조건은 1차 변화량이 0이 되는 것이다. 따라서 $\varepsilon$의 계수인 적분 값이 0이 되어야 한다.

$$
\delta J = \int_{a}^{b} \left( \frac{\partial F}{\partial y}\eta + \frac{\partial F}{\partial y'}\eta' \right) dx = 0
$$

$\eta'$ 항을 제거하기 위해 두 번째 항에 부분적분을 적용한다.

$$
\int_{a}^{b} \frac{\partial F}{\partial y'}\eta' dx = \left[ \frac{\partial F}{\partial y'}\eta \right]_{a}^{b} - \int_{a}^{b} \frac{d}{dx}\left(\frac{\partial F}{\partial y'}\right) \eta \,dx
$$

경계 조건 $\eta(a) = \eta(b) = 0$에 의해 첫 번째 항 $[ \cdot ]_{a}^{b}$은 0이 된다. 이를 $\delta J$ 식에 대입하고 $\eta$에 대해 정리한다.

$$
\delta J = \int_{a}^{b} \left\{ \frac{\partial F}{\partial y} - \frac{d}{dx}\left(\frac{\partial F}{\partial y'}\right) \right\} \eta \,dx = 0
$$

이 등식이 임의의 보조 함수 $\eta(x)$에 대해 항상 성립해야 한다(변분법의 기본 보조정리). 이를 만족하는 유일한 조건은 피적분함수에서 $\eta(x)$를 제외한 부분이 해당 구간에서 항등적으로 0이 되는 것이다.

따라서 다음의 방정식이 도출된다.

> $$
\frac{\partial F}{\partial y} - \frac{d}{dx}\left(\frac{\partial F}{\partial y'}\right) = 0
 $$

이것이 **오일러-라그랑주 방정식**이다. 이는 함수널 $J[y]$를 극값으로 만드는 최적 함수 $y(x)$가 만족해야 하는 미분 방정식이다.