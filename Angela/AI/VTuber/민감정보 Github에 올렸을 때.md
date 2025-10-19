
## 📖 목차

1. [🚨 문제 인식: 어떤 상황인가?](https://chatgpt.com/?temporary-chat=true#-%EB%AC%B8%EC%A0%9C-%EC%9D%B8%EC%8B%9D-%EC%96%B4%EB%96%A4-%EC%83%81%ED%99%A9%EC%9D%B8%EA%B0%80)
    
2. [🧯 1단계 — 유출된 토큰 즉시 폐기](https://chatgpt.com/?temporary-chat=true#-1%EB%8B%A8%EA%B3%84--%EC%9C%A0%EC%B6%9C%EB%90%9C-%ED%86%A0%ED%81%B0-%EC%A6%89%EC%8B%9C-%ED%8F%90%EA%B8%B0)
    
3. [🧹 2단계 — 깃 히스토리에서 완전 제거](https://chatgpt.com/?temporary-chat=true#-2%EB%8B%A8%EA%B3%84--%EA%B9%83-%ED%9E%88%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%97%90%EC%84%9C-%EC%99%84%EC%A0%84-%EC%A0%9C%EA%B1%B0)
    
4. [📦 3단계 — 새로 클론 후 정리](https://chatgpt.com/?temporary-chat=true#-3%EB%8B%A8%EA%B3%84--%EC%83%88%EB%A1%9C-%ED%81%B4%EB%A1%A0-%ED%9B%84-%EC%A0%95%EB%A6%AC)
    
5. [🔐 4단계 — .gitignore 정리 및 .env 사용 전환](https://chatgpt.com/?temporary-chat=true#-4%EB%8B%A8%EA%B3%84--gitignore-%EC%A0%95%EB%A6%AC-%EB%B0%8F-env-%EC%82%AC%EC%9A%A9-%EC%A0%84%ED%99%98)
    
6. [🚀 5단계 — GitHub에 안전하게 푸시](https://chatgpt.com/?temporary-chat=true#-5%EB%8B%A8%EA%B3%84--github%EC%97%90-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%ED%91%B8%EC%8B%9C)
    
7. [🧠 추가 팁 — 사전 예방 및 운영 노하우](https://chatgpt.com/?temporary-chat=true#-%EC%B6%94%EA%B0%80-%ED%8C%81--%EC%82%AC%EC%A0%84-%EC%98%88%EB%B0%A9-%EB%B0%8F-%EC%9A%B4%EC%98%81-%EB%85%B8%ED%95%98%EC%9A%B0)
    
8. [📋 부록 — 명령어 모음](https://chatgpt.com/?temporary-chat=true#-%EB%B6%80%EB%A1%9D--%EB%AA%85%EB%A0%B9%EC%96%B4-%EB%AA%A8%EC%9D%8C)
    

---

## 🚨 문제 인식: 어떤 상황인가?

### ❗ 대표적인 증상

```
! [remote rejected] main -> main (push declined due to repository rule violations)
error: failed to push some refs to 'https://github.com/username/repo'
```

또는

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: Push cannot contain secrets
```

### 💡 원인

GitHub의 **Secret Scanning / Push Protection** 기능이  
커밋 내에서 **API Key / Discord Token / AWS Key 등 비밀정보**를 감지했기 때문입니다.

즉, **GitHub이 자동으로 푸시를 차단한 상황**입니다.  
이는 GitHub이 "비밀이 포함된 커밋은 절대 올라가지 못하게" 막은 것입니다.

---

## 🧯 1단계 — 유출된 토큰 즉시 폐기

### ✅ Discord 토큰 예시

1. [Discord Developer Portal](https://discord.com/developers/applications) 접속
    
2. 봇 선택 → **Bot 탭** → **Reset Token** 클릭
    
3. 새 토큰 복사 (이제 이걸 `.env`에서 사용)
    

### ✅ 기타 서비스별 경로

|서비스|토큰 폐기 경로|
|:--|:--|
|**GitHub PAT**|Settings → Developer Settings → Personal Access Tokens|
|**Google Gemini**|[AI Studio API Keys](https://aistudio.google.com/app/apikey)|
|**ElevenLabs**|[Profile Settings → API Keys](https://beta.elevenlabs.io/)|
|**AWS**|IAM → Users → Security Credentials → Access Keys|

> ⚠️ 이미 GitHub에 올라간 토큰은 “자동으로 폐기”될 수 있습니다.  
> 그래도 반드시 수동으로 재발급하세요.

---

## 🧹 2단계 — 깃 히스토리에서 완전 제거

> 민감한 파일을 단순히 “삭제 후 커밋”해도  
> **과거 커밋 기록에는 여전히 남아 있습니다.**

### 🧰 사용 도구: `git filter-repo`

> 설치
> 
> ```bash
> pip install git-filter-repo
> ```

### 🔧 실행 예시

```bash
# 저장소로 이동
cd AI-Vtuber

# 민감 파일(config.json, dico_token.py) 기록 제거
git filter-repo --path discordbot-test/dico_token.py --path config.json --invert-paths
```

> ✅ 이 명령은 과거 모든 커밋에서 해당 파일을 완전히 제거합니다.

---

## 📦 3단계 — 새로 클론 후 정리

> `git filter-repo`는 **새 클론(fresh clone)** 에서만 안전하게 작동합니다.

### 🔁 새 클론 받기

```bash
git clone https://github.com/qoperre/AI-Vtuber.git clean-ai-vtuber
cd clean-ai-vtuber
```

### 🚮 기존 로컬 폴더 삭제

```bash
rm -rf ../AI-Vtuber   # macOS/Linux
# 또는
Remove-Item ../AI-Vtuber -Recurse -Force  # PowerShell
```

---

## 🔐 4단계 — .gitignore 정리 및 .env 사용 전환

### 📄 .gitignore 예시

```
.env
config.json
discordbot-test/dico_token.py
*.key
*.pem
```

### 📄 .env 예시

```
DISCORD_TOKEN=your_new_token
DISCORD_CHANNEL_ID=123456789012345678
GEMINI_KEY=your_gemini_api_key
EL_KEY=your_elevenlabs_api_key
```

### 💡 코드에서 불러오기

```python
from dotenv import load_dotenv
import os

load_dotenv()

DISCORD_TOKEN = os.getenv("DISCORD_TOKEN")
GEMINI_KEY = os.getenv("GEMINI_KEY")
```

---

## 🚀 5단계 — GitHub에 안전하게 푸시

1. 모든 비밀 제거 확인
    
    ```bash
    git grep -i "token"
    git grep -i "key"
    ```
    
2. 강제 푸시 (히스토리 덮어쓰기)
    
    ```bash
    git push origin main --force
    ```
    
3. GitHub Security Alerts 확인
    
    - Repository → **Security → Secret Scanning Alerts**
        
    - 알림이 없다면 이제 안전한 상태입니다 ✅
        

---

## 🧠 추가 팁 — 사전 예방 및 운영 노하우

|구분|방법|설명|
|---|---|---|
|🧩 **환경변수 사용**|`.env` + `dotenv`|API 키를 코드/커밋에서 분리|
|🚫 **Git 추적 방지**|`.gitignore`|비밀 파일 커밋 방지|
|🧹 **정기 점검**|`git log -p -G 'token'`|과거 커밋 내 민감정보 확인|
|🔒 **권한 최소화**|Discord, AWS, Google 등 최소 권한 키 사용||
|🤝 **협업 시 주의**|`.env.example` 제공|템플릿만 공유하고 실제 키는 개인별 관리|
|🧾 **자동 보호**|GitHub Push Protection 활성화|비밀 포함 커밋 자동 차단|

---

## 📋 부록 — 명령어 모음

|목적|명령어|
|---|---|
|민감 파일 삭제 후 히스토리 재작성|`git filter-repo --path <파일경로> --invert-paths`|
|새 클론 받기|`git clone <repo> clean-repo`|
|비밀 문자열 검색|`git grep -i "token"`|
|푸시 강제 덮어쓰기|`git push origin main --force`|
|`.env` 라이브러리 설치|`pip install python-dotenv`|
|`.env` 예시 파일 생성|`cp .env .env.example`|

---

## ✅ 최종 체크리스트

|항목|완료 여부|
|---|---|
|🚫 유출된 토큰 폐기 및 재발급|☐|
|🧹 git filter-repo로 히스토리 정리|☐|
|🧾 .gitignore 수정 완료|☐|
|🌱 .env로 환경변수 이전|☐|
|🧠 GitHub Secret Scanning 경고 해제|☐|
|🧩 새 토큰으로 테스트 성공|☐|

---

### 🧩 결론

이제 clean-ai-vtuber는

- 민감정보가 완전히 제거된 **안전한 히스토리 기반 저장소**
    
- GitHub Push Protection에도 막히지 않는 **깨끗한 레포**
    
- 환경변수 기반으로 보안과 유지보수가 강화된 **프로덕션 구조**
    

---

> 🧷 **추가로 추천:**
> 
> - `.env.example` 만들어서 키 이름만 남기기
>     
> - 서버 배포 시 `.env`는 **비공개 환경변수**로 등록 (Heroku, GitHub Actions 등에서 지원)
>     
> - 커밋 전 `pre-commit` 훅으로 민감정보 자동 탐지 툴(예: `detect-secrets`) 사용
>     

---

원하신다면 이 가이드북을 `SECURITY_GUIDE.md` 파일로 만들어 GitHub 저장소 루트에 추가하는 명령어도 만들어드릴 수 있어요.  
그럴까요?