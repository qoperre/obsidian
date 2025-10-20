**AI 캐릭터: 레이 (Rei, レイ)**

- 실시간 대화 가능한 VTuber형 AI
    
- 감정 상태(Emotion), 말투(Style), 주제 성향(Preference), 목소리톤(VoiceTone) 보유
    
- 유저별 친밀도(Affinity) 및 기억(Memory) 시스템
    
- 감정 변화와 친밀도는 상호작용(칭찬/비난/공감 등)에 따라 실시간 변동
    
- LLM은 감정, 기억, 친밀도를 모두 참고해 “맥락 기반 응답”을 생성
    

---

## 2. 전체 시스템 아키텍처

```
        ┌────────────────────────────┐
        │        Discord Bot         │
        │ (Message + Voice Channel)  │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │     Persona Manager        │
        │  ├ Emotion Engine          │
        │  ├ Memory Store            │
        │  ├ Prompt Builder          │
        │  └ Voice Selector          │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │       LLM (Gemini)         │
        │  ↔ JSON Output Contract    │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │   TTS (ElevenLabs/Pyttsx3) │
        │   감정별 목소리 톤 변경    │
        └────────────────────────────┘
```

---

## 3. 모델 구성 요소 (계층별 설계)

### 3.1 Persona Core (핵심 정보)

- 정의 방식: `persona/configs/seoyeon.json`
    
- 내용:
    
    - 이름, 성격 요약, 말투 스타일, 금지어, 주제 선호
        
    - 감정 상태 종류(`neutral`, `happy`, `sad`, `angry`, `shy`, …)
        
    - 감정 간 전이 규칙(`praise → happy`, `insult → angry`)
        
    - 목소리 톤 매핑 (감정별 Voice ID)
        
- 역할: 모델의 “기본 정체성” 정의
    

---

### 3.2 Emotion Engine (감정 상태 관리)

- 입력: 사용자 메시지 + LLM 피드백(JSON의 mood_delta, affinity_delta)
    
- 출력: 새로운 감정 상태
    
- 구조:
    
    - `emotion_state = f(previous_state, user_sentiment, llm_signal)`
        
    - 단기 목표: 휴리스틱 기반 (감정 단순 전환)
        
    - 장기 목표: 감정 예측 미니 모델 (fine-tuned classifier)
        
- 기능:
    
    - 감정 간 weight 조정
        
    - 감정→VoiceTone 매핑
        
    - 감정 상태가 바뀌면 말투, 어휘, 발화 길이도 자동 변경
        

---

### 3.3 Memory System (기억 저장소)

- **단기기억(Short-term Memory)**
    
    - 최근 대화 N턴을 요약 저장
        
    - LLM 프롬프트에 직접 반영
        
    - 시점: 매 턴마다 갱신
        
    - 용도: 맥락 유지 (ex. "아까 말했잖아" 대응)
        
- **장기기억(Long-term Memory)**
    
    - 대화 중 중요 문장을 요약·추출
        
    - 일정 간격(예: 10턴)마다 자동 요약 → 저장
        
    - 예: “기범은 물리학에 관심이 많다.”
        
    - 용도: 지속적 캐릭터 일관성
        
- **친밀도(Affinity)**
    
    - 사용자별 정수 점수 (-100 ~ +100)
        
    - 대화 톤, 감정, 유저 반응에 따라 조정
        
    - 예: 칭찬 → +3, 비난 → -5
        
    - 값에 따라 캐릭터 반응 변화
        
        - 낮을 때: 경계심 or 냉담
            
        - 높을 때: 장난스럽고 편안한 톤
            

---

### 3.4 Prompt Builder (LLM 프롬프트 생성)

- LLM에게 전달할 전체 맥락 생성기
    
- 포함 요소:
    
    - Persona 정보 (말투, 주제, 감정 상태 등)
        
    - Memory Context (최근 대화/중요 기억)
        
    - User Message
        
    - Output Contract (JSON Schema 강제)
        
- 출력 포맷(JSON):
    
    ```json
    {
      "reply": "사용자에게 보낼 답변",
      "mood": "happy",
      "mood_delta": 1,
      "affinity_delta": 2,
      "tts_tone": "happy"
    }
    ```
    
- 장점:
    
    - LLM 1회 호출로 “답변 + 감정 변화 + 친밀도 변화” 동시 반영
        
    - LLM이 감정변화를 직접 예측할 수 있게 학습 효과
        

---

### 3.5 Voice Selector (음성 톤 제어)

- 현재 감정 상태에 따라 Voice ID 선택
    
- 예:
    
    |감정|Voice ID|특징|
    |---|---|---|
    |happy|AAAAAA|밝고 빠름|
    |sad|BBBBBB|느리고 부드러움|
    |angry|CCCCCC|낮은 톤, 짧은 문장|
    |shy|DDDDDD|작고 수줍은 톤|
    
- 장기적으로 감정별로 다른 ElevenLabs 모델을 사용
    

---

## 4. 대화 처리 흐름

 **사용자 메시지 수신**  
→ Discord `on_message()` 이벤트

 **PersonaManager 호출**

```python
result = persona_manager.step(user_id, user_message, llm_call)
```

 **PromptBuilder**가 LLM용 프롬프트 생성  
→ 현재 감정/기억/친밀도 포함

 **LLM 호출 (Gemini)**  
→ JSON 형태 응답

 **PersonaManager가 상태 갱신**

- `emotion_state`, `affinity`, `memory` 업데이트
    
- 다음 턴에 이 상태를 반영
    

 **TTS 출력**

- 감정 상태에 맞는 Voice ID로 음성 생성
    
- Discord 음성 채널로 재생
    

---

## 5. 학습/강화 로직 설계 (추후 확장)

| 단계      | 내용                                     | 구현 수준 |
| ------- | -------------------------------------- | ----- |
| Phase 1 | Persona JSON + Emotion Engine + Memory | 단기 목표 |
| Phase 2 | 감정 예측 미니 모델 (BERT or LSTM fine-tune)   | 중기 목표 |
| Phase 3 | Affinity 기반 문체 변화 자동화                  | 중기 목표 |
| Phase 4 | 장기기억 요약 모델 (LLM summarization)         | 중기 목표 |
| Phase 5 | Voice Emotion Model (감정별 음성 미세조정)      | 장기 목표 |
| Phase 6 | 실시간 감정 피드백 루프 (표정/모션)                  | 최종 목표 |

---

## 6. 데이터 설계

### (1) Persona Config

- JSON 기반 → 외부 수정 가능
    
- 버전 관리 가능 (서연 v1, v2, v3…)
    

### (2) Memory Store

- `memory.json` 구조 예시:
    
    ```json
    {
      "users": {
        "1234567890": {
          "affinity": 12,
          "short_term": [
            {"t": 1730000000, "content": "U: 오늘 뭐해 / A: 공부중이야~"},
            {"t": 1730000100, "content": "U: 피곤하지? / A: 좀 피곤하긴 하다ㅎㅎ"}
          ],
          "long_term": [
            {"t": 1729000000, "content": "기범은 프로젝트 문 세계관을 좋아한다."}
          ]
        }
      }
    }
    ```
    

### (3) Emotion Transition Table

- JSON or dict 형태로 정의  
    (AI와 감정 모듈이 공유)
    

---

## 7. 기술적 포인트

|영역|기술|
|---|---|
|LLM|Google Gemini API (`generate_content()`)|
|Memory|JSON File (→ 향후 Vector DB 확장)|
|Emotion|Rule-based + Sentiment heuristic|
|Voice|ElevenLabs API (`text-to-speech`)|
|Orchestration|Async I/O (Discord.py, aiohttp)|
|Persona Config|JSON Schema (확장 가능)|

---

## 8. 개발 단계별 구현 순서

|단계|구현 항목|설명|
|---|---|---|
|1단계|PersonaManager 기본 골격|JSON 로드 + 감정/기억/프롬프트 생성 통합|
|2단계|EmotionEngine 간단 구현|긍정/부정 키워드 기반 감정 변화|
|3단계|MemoryStore 구축|user별 short/long term memory|
|4단계|PromptBuilder 작성|JSON Schema 기반 프롬프트 생성|
|5단계|run.py 연동|PersonaManager와 LLM 연결|
|6단계|TTS VoiceTone 제어|감정별 Voice ID 자동선택|
|7단계|감정 + 친밀도 실시간 반영 테스트|Discord 채팅에서 변화 확인|
|8단계|장기기억 요약 로직 추가|n턴마다 대화 요약 저장|
|9단계|감정 예측 미니모델 도입|rule-based → ML 기반으로 전환|
|10단계|실시간 대화 최적화|latency 줄이기, voice preloading 등|

---

## 9. 최종 모델 목표 상태

|기능|상태|
|---|---|
|감정 상태 유지|✅ (mood + transition)|
|말투 자동 조정|✅ (prompt에 감정 반영)|
|친밀도 변화|✅ (affinity_delta 반영)|
|장/단기 기억|🔄 (파일 기반 저장 + 요약)|
|감정별 목소리|🔄 (ElevenLabs tone mapping)|
|실시간 반응|🔄 (TTS latency 최적화 예정)|

---

## 10. 다음 단계 (지금 해야 할 일)

1️⃣ **이 로드맵을 기준으로 폴더 구조만 먼저 세팅**

```
persona/
├─ configs/
│  └─ seoyeon.json
├─ persona_manager.py
├─ emotion_engine.py
├─ memory_store.py
└─ prompt_builder.py
```

2️⃣ `seoyeon.json` 작성  
→ 캐릭터의 말투/성격/감정 목록 정의

3️⃣ `run.py`와 `PersonaManager`를 연결할 최소 통합 코드 작성  
→ 이후 감정 변화, 메모리 반영 확인

---

원하면 이 로드맵을 **시각화한 구조 다이어그램**으로도 만들어줄 수 있어.  
그림으로 보면 각 모듈이 어떻게 연결되는지가 더 명확하게 보여.

다음으로 어떤 단계부터 세부 설계를 들어가고 싶어?  
예를 들어 “2단계 EmotionEngine 설계부터 구체적으로”, “3단계 Memory 구조 먼저”처럼 정해줘.