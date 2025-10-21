# 1) 상태 정의

감정은 보통 “라벨(기쁨/분노…)”만 쓰면 거칠어지기 때문에, **연속값 + 라벨**의 하이브리드가 좋음.

- **연속 상태(내부)**:
    
    - 정/부정(Valence) v ∈ [-1, 1]
        
    - 각성도(Arousal) a ∈ [0, 1]
        
    - 강도(Intensity) i ∈ [0, 1]
        
- **이산 라벨(외부표현용)**: mood ∈ {neutral, happy, excited, shy, angry, sad, tired …} -> 연속 상태에 따라 결정됨
    

→ 내부 연속상태가 실제 “정서 좌표”이고, 라벨은 렌더링(말투, 보이스, 표정)을 위한 스냅샷 이름

# 2) 관측치와 신호

매 틱(t)에 들어오는 신호 Oₜ는 대략 이렇게 봐.

- **텍스트 감성**: 사용자의 문장(칭찬/모욕/위로/하이프 등) → v, a 방향으로 미는 힘.
    
- **모델 보조신호**: LLM이 산출하는 `mood_delta`, `affinity_delta` 같은 메타.
    
- **관계 기억**: 사용자별 친밀도(affinity) → 장기적 긍·부정 편향.
    
- **이벤트 트리거**: 특정 단어/상황 → 라벨 전이를 강하게 힌트.
    

# 3) 전이 방정식의 뼈대

핵심 아이디어: “감정은 스르르 식고(감쇠), 자극엔 순간 반응하고(충격), 관계는 바이어스를 준다(편향)”

연속 상태 업데이트(Δt는 지난 시간, 클립은 범위 고정):

$$
\begin{aligned}
v_{t+1} &= (1-\lambda_v) \cdot v_t + \alpha_v \phi_v(O_t) + \beta \cdot \text{mood\_delta} + \eta \cdot \underbrace{\text{affinity}}_{\text{장기편향}} \\
a_{t+1} &= (1-\lambda_a) \cdot a_t + \alpha_a \phi_a(O_t) \\
i_{t+1} &= \text{clip}\Big((1-\lambda_i)\cdot i_t + \gamma \cdot (|v_{t+1}-v_t| + |a_{t+1}-a_t|)\Big)
\end{aligned}
$$

- **감쇠(λ)**: 시간이 지나면 원점(평정)으로 천천히 복귀 → 튐 방지, 잔향 표현.
    
- **자극(φᵥ, φₐ)**: 관측치에서 뽑은 “긍/부정, 흥분” 성분(키워드/이모지/감성분석 등).
    
- **보조신호(β·mood_delta)**: LLM이 상황맥락상 “조금 더 기쁘게/차분하게” 같은 힌트를 줄 때 쓰는 가중치.
    
- **친밀도(η·affinity)**: 같은 말이라도 친한 유저면 덜 화나고 더 기뻐지는 경향.
    

이후 **라벨 결정**은 ‘정서 원형(circumplex)’에 맞춰 사분면+임계로 뽑아.

- v↑ & a↑ → excited / happy
    
- v↑ & a↓ → happy / calm
    
- v↓ & a↑ → angry / anxious
    
- v↓ & a↓ → sad / tired
    
- 분류는 `argmax`(여러 후보 점수 중 최댓값) 또는 **룰 기반 힌트**(트리거가 있으면 우선).
    

여기에 두 가지 안정화 장치:

- **쿨다운**: 직전 라벨과 너무 빨리 바뀌지 않게 최소 유지시간(예: 2~4초).
    
- **히스테리시스**: 경계 근처에서 들쭉날쭉하지 않게 들어갈 때·나올 때 문턱을 다르게.
    

# 4) 왜 하이브리드가 좋은가

- **FSM(유한상태기계)/마르코프**만 쓰면: 전이표 만들기 쉬우나, 표현이 뻣뻣하고 블렌딩이 어려움.
    
- **연속형(PAD/VAD)** 만 쓰면: 부드럽지만 “말투/보이스/표정”을 고르려면 결국 구간화가 필요.  
    → **연속 내부 + 이산 외부**가 실전(보이스/표정 매핑)에 가장 잘 맞음음.
    

# 5) rei.json

`rei.json`은 **코드가 아니라 정책**이야. 즉, 파라미터/맵/룰을 **데이터로 선언**해서 엔진이 참조하게 하는 것.

담는 것:

1. **상태공간 정의**: 사용할 라벨 목록(emotions)
    
2. **엔진 하이퍼파라미터**: 감쇠율(λ), 쿨다운, 친밀도 바이어스(η), 강도 계산의 γ
    
3. **감성 사전**: praise/insult/comfort/hype… → φᵥ, φₐ에 들어갈 키워드 집합
    
4. **전이 트리거**: `"any->angry": ["바보", ...]` 같은 강한 힌트 룰
    
5. **렌더링 맵**:
    
    - `voice_map[mood]` → 보이스 ID, 말속도 등
        
    - `vts_parameters[mood]` → 표정 파라미터 세트
        
6. **프롬프트 힌트**: mood별 말투 지침(문장 길이, 이모티콘, 화법)
    

즉, **이론(상태-관측-전이-출력)** 을 코드가 계산하고, **값/룰**은 JSON이 들고 있는 구조.

# 6) 숫자를 어떻게 잡나? (by GPT)

- **감쇠(λ)** 는 “반감기”로 생각하면 편함.
    
    - 반감기 $T_{half}$이면, 1틱(1초라 치면) 감쇠율은 $\lambda = 1 - 2^{-1/T_{\text{half}}}$.
        
    - 예: $T_{half}$=10초 ⇒ λ≈0.066 → 10초마다 영향이 절반으로.
        
- **자극 게인(αᵥ, αₐ)**: 보통 0.1~0.4 범위에서 시작. 키워드가 여러 개 맞으면 누적되게.
    
- **보조 게인(β)**: 모델 힌트는 “미세 조정” 용도 → 0.05~0.15 권장.
    
- **강도 계수(γ)**: v/a가 급히 바뀌면 i가 올라야 자연스러움 → 0.3~0.6 정도.
    
- **쿨다운**: 2~5초. 너무 길면 무딤, 너무 짧으면 깜빡임.
    
- **친밀 바이어스(η)**: affinity를 -100~+100으로 두면, η는 0.001~0.003 정도(느리게 누적).
    

# 7) 출력(말/보이스/표정)과의 이론적 연결

- **라벨**은 **선택**(voice preset, 표정 preset).
    
- **강도 i**는 **스케일링**에 씀. 예:
    
    - 말속도/피치(보이스): arousal↑면 약간 빠르고 높게, sad/tired면 느리고 낮게.
        
    - 입모양/눈매(VTS): intensity·arousal로 MouthOpen/EyeWide 등을 보간.
        
- **valence**는 말투 단어 선택(긍정/부정 어휘, 감탄사 빈도)과 표정(웃음/찡그림)을 좌우.
    

# 8) 안정성 체크 포인트

- **진동**: 라벨이 자꾸 왕복하면 쿨다운↑, 히스테리시스 도입, λ↑.
    
- **포화**: i가 금방 1.0 근처면 γ↓ 또는 자극 누적 제한.
    
- **라벨 혼동**: sad↔tired 구분이 애매하면 경계값/우선순위 조정(예: a<0.25면 tired 우선).
    
- **사용자별 편향**: affinity가 너무 세면 금방 “항상 행복”이 되니 η 낮추기.
    

# 9) 확장 로드맵(선택)

- **감성 추정 고도화**: φᵥ, φₐ를 키워드 대신 문장 단위 감성모델(다국어, 이모티콘, 구어체)로 교체.
    
- **확률 모델**: HMM/칼만류 필터로 v,a를 추정(노이즈에 강함).
    
- **상황 기억**: 최근 N턴 요약(feeling context)로 자극을 시간평균.
    
- **개인화**: 유저별 α/λ/η를 점진 학습.
    

# 구현
```python emotion_engine.py
# persona/emotion_engine.py
from __future__ import annotations
from dataclasses import dataclass
from typing import Dict, List, Tuple
import math
import time
import re

@dataclass
class EmotionState:
    mood: str = "neutral"
    intensity: float = 0.3   # 0~1
    valence: float = 0.0     # -1~1
    arousal: float = 0.3     # 0~1
    last_update_ts: float = 0.0

def clamp(v, lo, hi): return max(lo, min(hi, v))

class EmotionEngine:
    """
    휴리스틱 기반 v1:
    state_{t+1} = decay(state_t) + sentiment(user) + llm_signal + transition(trigger)
    - decay: 시간 경과로 강도·각성 감소 (히스테리시스 완화)
    - sentiment(user): 키워드/이모지 기반 점수 → 감정별 가중 반영
    - llm_signal: LLM JSON의 mood_delta/affinity_delta를 보조 신호로 사용
    - transition: rei.json에 정의된 전이 테이블(트리거→목표감정) 우선 적용
    """
    def __init__(self, config: Dict):
        self.cfg = config
        self.state = EmotionState()
        self.cooldown_sec = self.cfg.get("engine", {}).get("cooldown_sec", 3.0)
        self.decay = self.cfg.get("engine", {}).get("decay", {"intensity":0.08,"arousal":0.05})
        self.keyword_map = self._compile_keywords(self.cfg.get("sentiment_keywords", {}))
        self.transition_rules = self.cfg.get("transitions", {})
        self.bias_by_affinity = self.cfg.get("engine", {}).get("affinity_bias", 0.002)

    def _compile_keywords(self, kw_cfg: Dict[str, List[str]]):
        compiled = {}
        for k, words in kw_cfg.items():
            compiled[k] = [re.compile(re.escape(w)) for w in words]
        return compiled

    def _score_sentiment(self, text: str) -> Tuple[float, float]:
        """
        text → (valence_delta, arousal_delta)
        간단 규칙:
        - 긍정 키워드 = +valence, 약간 +arousal
        - 부정 키워드 = -valence, +arousal (분노/공포는 각성↑)
        - 위로/공감 키워드 = +valence, -arousal(진정)
        """
        v, a = 0.0, 0.0
        def hit(key): 
            return any(p.search(text) for p in self.keyword_map.get(key, []))
        if hit("praise"): v += 0.25; a += 0.05
        if hit("insult"): v -= 0.35; a += 0.15
        if hit("comfort"): v += 0.15; a -= 0.05
        if hit("hype"):    v += 0.10; a += 0.20
        if hit("sadness"): v -= 0.20; a -= 0.05
        return v, a

    def _apply_decay(self, st: EmotionState, dt: float):
        st.intensity = clamp(st.intensity - self.decay["intensity"]*dt, 0.0, 1.0)
        st.arousal   = clamp(st.arousal   - self.decay["arousal"]  *dt, 0.0, 1.0)
        # valence는 천천히 0으로 회귀
        st.valence   = clamp(st.valence - 0.03*dt*math.copysign(1, st.valence), -1.0, 1.0) if abs(st.valence)>0.01 else 0.0

    def _pick_mood_from_pad(self, v: float, a: float, hint: str|None=None) -> str:
        """
        간단 매핑:
        - v↑, a↑ → happy/excited
        - v↑, a↓ → calm/happy
        - v↓, a↑ → angry/anxious
        - v↓, a↓ → sad/tired
        - hint가 있으면 우선 반영
        """
        if hint: 
            return hint
        if v >= 0.15 and a >= 0.55: return "excited"
        if v >= 0.10 and a <  0.55: return "happy"
        if v <= -0.15 and a >= 0.55: return "angry"
        if v <= -0.10 and a <  0.55: return "sad"
        if a < 0.25: return "tired"
        return "neutral"

    def _apply_transition_triggers(self, text: str) -> str|None:
        # rei.json의 transitions: {"insult->angry":[...키워드...] , ...}
        for rule, words in self.transition_rules.items():
            src, to = rule.split("->")
            for w in words:
                if w in text:
                    return to
        return None

    def update(self, user_text: str, llm_signal: Dict, affinity: int) -> EmotionState:
        now = time.time()
        st = self.state
        dt = max(0.0, now - (st.last_update_ts or now))
        st.last_update_ts = now

        # 1) decay
        self._apply_decay(st, dt)

        # 2) 사용자 발화 기반 감성
        dv, da = self._score_sentiment(user_text.lower())
        st.valence = clamp(st.valence + dv, -1.0, 1.0)
        st.arousal = clamp(st.arousal + da,  0.0, 1.0)
        st.intensity = clamp(st.intensity + (abs(dv)+abs(da))*0.4, 0.0, 1.0)

        # 3) LLM 신호 보조 (mood_delta: -2~+2 가정)
        md = float(llm_signal.get("mood_delta", 0.0))
        st.intensity = clamp(st.intensity + 0.15*md, 0.0, 1.0)
        st.valence   = clamp(st.valence   + 0.10*md, -1.0, 1.0)

        # 4) Affinity 편향 (친밀 높으면 긍정 바이어스)
        st.valence = clamp(st.valence + self.bias_by_affinity*affinity, -1.0, 1.0)

        # 5) 전이 트리거(강제 힌트)
        force_to = self._apply_transition_triggers(user_text.lower())
        new_mood = self._pick_mood_from_pad(st.valence, st.arousal, hint=force_to)

        # 6) 쿨다운: 너무 잦은 변화 방지 (같은 계열이면 교체 허용)
        if new_mood != st.mood and dt < self.cooldown_sec:
            same_family = {
                "happy":{"excited"}, "excited":{"happy"},
                "sad":{"tired"}, "tired":{"sad"}
            }
            if new_mood not in same_family.get(st.mood, set()):
                new_mood = st.mood

        st.mood = new_mood
        return st

```


```json rei.json
{
  "meta": {
    "name": "Rei",
    "persona_summary": "장난기 있지만 다정한 VTuber. 공감 잘하고 가끔 츤데레 농담.",
    "style": {
      "register": "반말",
      "length_hint": "짧고 리듬감 있게",
      "quirks": ["ㅎㅎ", "음", "솔직히 말하면"]
    },
    "forbidden": ["정치적 선동", "개인정보 요구"]
  },
  "emotions": ["neutral", "happy", "excited", "shy", "angry", "sad", "tired"],
  "engine": {
    "cooldown_sec": 3.0,
    "decay": { "intensity": 0.08, "arousal": 0.05 },
    "affinity_bias": 0.002
  },
  "voice_map": {
    "happy":   {"provider":"ELEVENLABS", "voice_id":"<YOUR_VOICE_ID_HAPPY>", "rate":1.05},
    "excited": {"provider":"ELEVENLABS", "voice_id":"<YOUR_VOICE_ID_HAPPY>", "rate":1.12},
    "shy":     {"provider":"ELEVENLABS", "voice_id":"<YOUR_VOICE_ID_SOFT>",  "rate":0.95},
    "angry":   {"provider":"ELEVENLABS", "voice_id":"<YOUR_VOICE_ID_LOW>",   "rate":1.00},
    "sad":     {"provider":"ELEVENLABS", "voice_id":"<YOUR_VOICE_ID_SOFT>",  "rate":0.92},
    "tired":   {"provider":"ELEVENLABS", "voice_id":"<YOUR_VOICE_ID_SOFT>",  "rate":0.90},
    "neutral": {"provider":"ELEVENLABS", "voice_id":"<DEFAULT_VOICE_ID>",    "rate":1.00}
  },
  "vts_parameters": {
    "happy":   { "MouthSmile": 1.0, "MouthOpen": 0.1, "BrowDownLeft": 0.0 },
    "excited": { "MouthSmile": 1.0, "MouthOpen": 0.3, "EyeWide": 0.8 },
    "shy":     { "MouthSmile": 0.3, "MouthOpen": 0.0, "Blush": 1.0 },
    "angry":   { "MouthSmile": 0.0, "MouthOpen": 0.2, "BrowDownLeft": 0.9 },
    "sad":     { "MouthSmile": 0.0, "MouthOpen": 0.0, "EyeClosed": 0.4 },
    "tired":   { "MouthSmile": 0.1, "MouthOpen": 0.0, "EyeClosed": 0.6 },
    "neutral": { "MouthSmile": 0.4, "MouthOpen": 0.0 }
  },
  "transitions": {
    "any->angry":  ["바보", "멍청", "싫어해", "꺼져"],
    "any->happy":  ["고마워", "최고", "대단", "멋져"],
    "any->sad":    ["힘들", "슬프", "우울", "눈물"],
    "any->excited":["레전드", "대박", "미쳤다", "개좋"]
  },
  "sentiment_keywords": {
    "praise":  ["고마워", "좋았", "최고", "멋져", "대단"],
    "insult":  ["바보", "멍청", "꺼져", "애매하다", "별로"],
    "comfort": ["괜찮아", "토닥", "응원", "걱정마"],
    "hype":    ["레전드", "대박", "미쳤", "쩐다"],
    "sadness": ["슬프", "우울", "힘들", "아파"]
  },
  "prompt_prefs": {
    "max_reply_chars": 500,
    "style_hints": {
      "happy":   "밝고 장난스럽게, 말끝 상승, 이모티콘 조금",
      "excited": "속도감 있게, 간결한 감탄사 섞기",
      "shy":     "짧은 문장, 말머뭇, 직설 피하기",
      "angry":   "단문 위주, 직설적, 살짝 까칠",
      "sad":     "부드럽고 느리게, 위로 포함",
      "tired":   "짧고 힘抜인 톤",
      "neutral": "친근하고 담백"
    }
  }
}

```


```python persona_manager.py
# persona/persona_manager.py
import json, os
from .emotion_engine import EmotionEngine, EmotionState

class PersonaManager:
    def __init__(self, rei_config_path: str, memory_store):
        with open(rei_config_path, "r", encoding="utf-8") as f:
            self.cfg = json.load(f)
        self.engine = EmotionEngine(self.cfg)
        self.memory = memory_store  # {affinity_by_user, short/long term 등}

    def select_voice(self, mood: str) -> str:
        vm = self.cfg["voice_map"].get(mood) or self.cfg["voice_map"]["neutral"]
        return vm["voice_id"]

    def vts_params(self, mood: str) -> dict:
        return self.cfg["vts_parameters"].get(mood, {})

    def build_prompt(self, user_id: str, user_text: str) -> str:
        st = self.engine.state
        style_hint = self.cfg["prompt_prefs"]["style_hints"].get(st.mood, "")
        base = f"""You are Rei, a friendly Korean VTuber.
- Tone: {style_hint}
- Current mood: {st.mood} (intensity {st.intensity:.2f})
- Register: casual Korean
Reply under {self.cfg["prompt_prefs"]["max_reply_chars"]} chars.
Return JSON with keys: reply, mood_delta, affinity_delta.
"""
        return base + f"\nUSER: {user_text}\n"

    def step(self, user_id: str, user_text: str, llm_call):
        # 1) LLM 호출
        prompt = self.build_prompt(user_id, user_text)
        llm_json = llm_call(prompt)  # dict: {reply, mood_delta, affinity_delta}
        reply = llm_json.get("reply", "")
        mood_delta = llm_json.get("mood_delta", 0)
        affinity_delta = int(llm_json.get("affinity_delta", 0))

        # 2) Affinity 갱신
        affinity = self.memory.add_affinity(user_id, affinity_delta)

        # 3) Emotion 업데이트
        new_state: EmotionState = self.engine.update(user_text, {"mood_delta": mood_delta}, affinity)

        # 4) 출력(보이스/표정)
        voice_id = self.select_voice(new_state.mood)
        vts = self.vts_params(new_state.mood)

        return {
            "reply": reply,
            "mood": new_state.mood,
            "intensity": new_state.intensity,
            "voice_id": voice_id,
            "vts_params": vts
        }

```


```python memory_store.py
# persona/memory_store.py
from collections import defaultdict

class MemoryStore:
    def __init__(self):
        self.affinity = defaultdict(int)

    def add_affinity(self, user_id: str, delta: int) -> int:
        self.affinity[user_id] += delta
        self.affinity[user_id] = max(-100, min(100, self.affinity[user_id]))
        return self.affinity[user_id]

```


