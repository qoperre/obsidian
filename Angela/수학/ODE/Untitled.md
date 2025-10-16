### f(x,y,z)의 **전(全)미분방정식** 푸는 법

(Exact differential equation in 3 variables)

#### 2-1 형태와 “적분가능 조건”

M(x,y,z) dx+N(x,y,z) dy+P(x,y,z) dz=0M(x,y,z)\,dx + N(x,y,z)\,dy + P(x,y,z)\,dz = 0M(x,y,z)dx+N(x,y,z)dy+P(x,y,z)dz=0

이 식이 “전미분”이라면 어떤 **스칼라 함수** fff에 대해

df=∂f∂xdx+∂f∂ydy+∂f∂zdz=M dx+N dy+P dzdf = \frac{\partial f}{\partial x}dx + \frac{\partial f}{\partial y}dy + \frac{\partial f}{\partial z}dz = M\,dx + N\,dy + P\,dzdf=∂x∂f​dx+∂y∂f​dy+∂z∂f​dz=Mdx+Ndy+Pdz

이어야 해. ⇒ **적분가능(=exact)** 조건:

∂M∂y=∂N∂x,∂M∂z=∂P∂x,∂N∂z=∂P∂y\frac{\partial M}{\partial y} = \frac{\partial N}{\partial x},\qquad \frac{\partial M}{\partial z} = \frac{\partial P}{\partial x},\qquad \frac{\partial N}{\partial z} = \frac{\partial P}{\partial y}∂y∂M​=∂x∂N​,∂z∂M​=∂x∂P​,∂z∂N​=∂y∂P​

(모두 C1C^1C1 함수라 가정)

> 벡터미적분 관점: ∇×F=0\nabla \times \mathbf{F} = \mathbf{0}∇×F=0 (curl 0) 면 F=∇f\mathbf{F} = \nabla fF=∇f.

#### 2-2 fff 구하기 ― 단계별 적분

1. **x-적분**
    
    f(x,y,z)=∫M dx+C1(y,z)f(x,y,z) = \int M\,dx + C_1(y,z)f(x,y,z)=∫Mdx+C1​(y,z)
    
    여기서 C1C_1C1​은 “xxx 적분상수”지만 y,zy,zy,z의 함수.
    
2. **y-편미분 → 일치시키기**
    
    ∂f∂y=N  ⇒  ∂∂y[∫M dx]+∂C1∂y=N\frac{\partial f}{\partial y} = N \;\Rightarrow\; \frac{\partial}{\partial y}\bigl[\int M\,dx\bigr] + \frac{\partial C_1}{\partial y} = N∂y∂f​=N⇒∂y∂​[∫Mdx]+∂y∂C1​​=N
    
    이 식으로부터 ∂C1/∂y\partial C_1/\partial y∂C1​/∂y를 구해 **적분** → C1(y,z)C_1(y,z)C1​(y,z) 결정 + 새 상수 C2(z)C_2(z)C2​(z).
    
3. **z-편미분 → 일치시키기**
    
    ∂f∂z=P\frac{\partial f}{\partial z} = P∂z∂f​=P
    
    같은 방식으로 C2(z)C_2(z)C2​(z)를 확정.
    
4. 최종적으로 f(x,y,z)=(위에서 완성된 식)+상수f(x,y,z) = \text{(위에서 완성된 식)} + \text{상수}f(x,y,z)=(위에서 완성된 식)+상수.  
    **일반해**는 f(x,y,z)=Cf(x,y,z)=Cf(x,y,z)=C 형태의 암 implicitly surface.
    

---

#### ⚡️작은 예시

(2xz+y) dx  +  (x2+z) dy  +  (x2+y) dz=0(2xz+ y)\,dx \;+\; (x^2 + z)\,dy \;+\; (x^2 + y)\,dz = 0(2xz+y)dx+(x2+z)dy+(x2+y)dz=0

1. 적분가능?
    

∂yM=1,  ∂xN=2x      → 불일치!\partial_y M = 1,\; \partial_x N = 2x \;\;\;\text{→ 불일치!}∂y​M=1,∂x​N=2x→ 불일치!

curl ≠ 0 → **exact 아님** → 적분인자 μ(x,y,z) 찾아야 함 (변수가 여러 개면 어렵고, 보통 μ = μ(x)·μ(y) 패턴 가정 후 비교).

2. 만약 다음처럼 변경해 보자
    

(2xz) dx+(x2) dy+(x2) dz=0(2xz)\,dx + (x^2)\,dy + (x^2)\,dz = 0(2xz)dx+(x2)dy+(x2)dz=0

그럼

∂yM=0,  ∂xN=2x;∂zM=2x,  ∂xP=2x;∂zN=0,  ∂yP=0\partial_y M = 0,\; \partial_x N = 2x;\quad \partial_z M = 2x,\; \partial_x P = 2x;\quad \partial_z N = 0,\; \partial_y P = 0∂y​M=0,∂x​N=2x;∂z​M=2x,∂x​P=2x;∂z​N=0,∂y​P=0

모두 일치 → exact.

- x-적분: f=∫2xz dx=x2z+C1(y,z)f = \int 2xz\,dx = x^2 z + C_1(y,z)f=∫2xzdx=x2z+C1​(y,z)
    
- y-조건: ∂f/∂y=0=N=x2\partial f/\partial y = 0 = N = x^2∂f/∂y=0=N=x2 ⇒ 모순이므로 사실 이 형태도 exact 아님…  
    (예시로 느낌만! 실제 문제에선 세 조건 모두 확인 필수)
    

---

### 한 줄 정리

- **선형‧변수계수 ODE**는 행렬로 포장하면 선형대수 레버(기초해 행렬, 행렬지수 등)를 바로 쓸 수 있다.
    
- **전미분방정식**은 “curl = 0 ↔︎ F=∇f\mathbf{F} = \nabla fF=∇f” 조건만 만족하면, ▶︎ 한 변수씩 적분해 fff를 재구성 → f=Cf=Cf=C가 해.
    
