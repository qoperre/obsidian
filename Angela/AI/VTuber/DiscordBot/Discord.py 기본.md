

## Discord.py란?

### 기본 개념

**Discord.py**는 Python으로 Discord 봇을 개발할 수 있게 해주는 공식적이고 강력한 라이브러리이다.  
이 라이브러리는 디스코드 API를 직접 다루는 복잡함을 감추고,  
개발자가 Python 코드로 **이벤트**, **메시지**, **명령어**, **상태 표시** 등을 쉽게 구현할 수 있게 해준다.

Discord.py는 크게 두 가지 계층으로 구성된다:

|계층|클래스|역할|
|---|---|---|
|저수준 (Low-level)|`discord.Client`|디스코드 API에 직접 연결하고 이벤트를 처리하는 기본 객체|
|고수준 (High-level)|`discord.ext.commands.Bot`|`Client`를 상속하고 명령어(Command) 시스템을 추가한 객체|

요즘 대부분의 개발자는 `commands.Bot`을 사용한다.  
이유는 명령어 시스템이 내장되어 있고, 유지보수가 편하며, 봇의 확장성이 뛰어나기 때문이다.

---

## 기본 구조 및 인텐트(Intents)

### Intents란?

Discord의 API 설계는 “데이터 최소 접근” 원칙을 따른다.  
즉, 봇이 수신할 데이터의 종류를 명시적으로 지정해야 한다.  
이걸 **Intents(인텐트)** 라고 한다.

인텐트를 통해 “봇이 어떤 이벤트를 받아들일 수 있는가”를 설정하며,  
명령어 인식, 멤버 정보, 상태 변경 등을 처리할 수 있게 된다.

---

### Intents 설정 예제

```python
import discord
from discord.ext import commands

# Intents 객체 생성
intents = discord.Intents.default()
intents.message_content = True  # 메시지 내용을 읽을 수 있도록 설정 (명령어 인식에 필수)
intents.members = True          # 멤버 관련 이벤트 처리 (선택)

# 봇 인스턴스 생성
bot = commands.Bot(command_prefix='./', intents=intents)
```

- `discord.Intents.default()` : 기본적인 이벤트만 허용 (예: 메시지, 서버 연결 등)
    
- `message_content = True` : 사용자의 메시지 내용을 읽을 수 있게 함  
    → 최신 Discord.py에서는 기본적으로 False로 되어 있어서,  
    이걸 켜지 않으면 봇이 메시지 내용을 인식하지 못한다.
    

---

###  Privileged Intents (권한이 필요한 인텐트)

3가지 인텐트는 Discord 개발자 포털에서 직접 활성화해야 한다.  
그렇지 않으면 다음과 같은 오류가 발생한다:

```
discord.errors.PrivilegedIntentsRequired:
  Shard ID None is requesting privileged intents that have not been explicitly enabled.
```

#### 설정 방법:

1. [Discord Developer Portal](https://discord.com/developers/applications) 접속
    
2. 자신의 봇 선택
    
3. 왼쪽 메뉴에서 **Bot** 탭 클릭
    
4. 아래로 스크롤 → “Privileged Gateway Intents” 항목 찾기
    
5. 다음 세 가지를 켬:
    
    - PRESENCE INTENT (상태 감지)
        
    - SERVER MEMBERS INTENT (서버 멤버 접근)
        
    - MESSAGE CONTENT INTENT (메시지 내용 읽기)
        
6. **Save Changes** 클릭 후 저장
    

---

## 봇 시작 이벤트 — `on_ready`

봇이 Discord 서버에 정상적으로 로그인되었을 때 단 한 번 호출되는 이벤트.  
이곳에 초기화 코드, 상태 변경 코드 등을 작성한다.

```python
@bot.event
async def on_ready():
    print('봇 로그인 완료!')
    print(f'봇 이름: {bot.user}')
    await bot.change_presence(
        status=discord.Status.online,
        activity=discord.Game('VS Code로 개발 중')
    )
```

### 상태 설정 옵션

|속성|의미|
|---|---|
|`discord.Status.online`|온라인|
|`discord.Status.idle`|자리 비움|
|`discord.Status.dnd`|방해 금지 (Do Not Disturb)|
|`discord.Status.offline`|오프라인 표시|

### 활동(Activity) 설정

```python
discord.Game("게임 이름")  # 게임 중
discord.Streaming(name="방송 제목", url="https://twitch.tv/...")  # 방송 중
discord.Activity(type=discord.ActivityType.listening, name="음악")  # 듣는 중
discord.Activity(type=discord.ActivityType.watching, name="영상")   # 보는 중
```

---

## 메시지 감지 이벤트 — `on_message`

이벤트 기반으로 유저 메시지를 감지하고 반응한다.  
이 방식은 명령어 시스템보다 유연하지만, 명령어 파싱은 수동으로 해야 한다.

```python
@bot.event
async def on_message(message):
    if message.author == bot.user:
        return  # 봇 자신의 메시지는 무시 (중복 방지)

    if message.content == '테스트':
        await message.channel.send(f"{message.author.mention}, 어서오세요!")
        try:
            await message.author.send(f"{message.author} | {message.author.mention} 유저님, 환영합니다.")
        except discord.Forbidden:
            print(f"[DM 불가] {message.author} 님이 DM을 받을 수 없습니다.")
        except discord.HTTPException as e:
            print(f"[DM 에러] {message.author} : {e}")
```

### 주의사항

- `if message.author == bot.user:`  
    → 봇이 자기 자신에게 반응하는 무한 루프 방지.
    
- `discord.Forbidden`:  
    → 유저가 DM을 비활성화했거나 봇을 차단한 경우.
    
- `discord.HTTPException`:  
    → Discord API 오류, 혹은 메시지 중복 전송 시 발생.
    

---

## 명령어 시스템 — `commands.Bot`

Discord.py의 강력한 기능 중 하나는 명령어(Command) 데코레이터 시스템이다.  
이 방식은 `./명령어` 같은 접두사(prefix)를 통해 명령을 인식한다.

---

### 기본 구조

```python
@bot.command()
async def hello(ctx):
    await ctx.send("안녕하세요!")
```

|요소|설명|
|---|---|
|`@bot.command()`|명령어 등록용 데코레이터|
|`ctx`|명령어 실행 시 자동으로 전달되는 “Context 객체” (명령 정보 포함)|
|`ctx.send()`|명령어가 실행된 채널에 메시지 전송|
|`ctx.author`|명령어를 실행한 유저|
|`ctx.channel`|명령어가 입력된 채널|

---

### 별칭 (aliases)

```python
@bot.command(aliases=['hi', '안녕'])
async def hello(ctx):
    await ctx.send("안녕하세요!")
```

> 실행 가능 명령:

```
./hello
./hi
./안녕
```

aliases 중 **하나라도 일치하면 같은 함수가 실행된다.**

---

### prefix의 위치와 작동 규칙

|입력|작동 여부|이유|
|---|---|---|
|`./hello`|✅|prefix(`./`)는 항상 맨 앞에 와야 함|
|`hello`|❌|prefix 누락|
|`테스트./`|❌|prefix가 뒤에 있음|
|`./hello ./login`|⚠️|첫 번째 명령어만 인식|
|`./HELLO`|✅|대소문자 구분 안 함|

---

## `client.command()` vs `bot.command()`

둘은 사실상 동일하다.  
이유는 `commands.Bot`이 `discord.Client`를 상속받은 클래스이기 때문이다.

```python
bot = commands.Bot(command_prefix='./')
client = commands.Bot(command_prefix='./')
```

위 두 코드는 완전히 같은 동작을 한다.  
즉, 이름이 `bot`이든 `client`든 실제 타입이 `commands.Bot`이면 된다.

단, 아래처럼 진짜 `discord.Client()`를 쓴다면 작동하지 않는다

```python
client = discord.Client()
@client.command()  # AttributeError 발생
```

---

## 주요 오류와 해결법 정리

|오류 메시지|원인|해결법|
|---|---|---|
|`TypeError: Client.__init__() missing 1 required keyword-only argument: 'intents'`|intents 미지정|`intents = discord.Intents.default()` 설정|
|`PrivilegedIntentsRequired`|Discord 포털에서 인텐트 미활성화|Developer Portal → Bot 탭 → 인텐트 3개 활성화|
|`HTTPException: 400 Bad Request (error code: 50007)`|유저가 DM 비활성화 또는 차단|`try/except`로 예외 처리|
|`AttributeError: module 'discord' has no attribute 'status'`|`discord.status` → `discord.Status`로 수정|올바른 대문자 표기 사용|
|`RuntimeError: coroutine '...' was never awaited`|`await` 누락|비동기 함수 호출 시 반드시 `await` 사용|

---

## DM (Direct Message) 관련 주의사항

Discord 정책상, 봇은 다음 조건에서만 유저에게 DM을 보낼 수 있다:

1. 유저가 봇과 같은 서버에 존재
    
2. 유저의 **“서버 멤버로부터 DM 받기”** 설정이 켜져 있음
    
3. 봇이 차단되지 않음
    

만약 DM 전송이 불가능한 유저에게 메시지를 보내면 다음 예외가 발생한다:

```
discord.errors.HTTPException: 400 Bad Request (error code: 50007): Cannot send messages to this user
```

따라서 항상 다음과 같이 예외를 처리해야 한다:

```python
try:
    await message.author.send("DM 테스트입니다.")
except discord.Forbidden:
    await message.channel.send(f"{message.author.mention}, DM이 꺼져 있어서 개인 메시지를 보낼 수 없습니다")
```

---

## `discord.Client`는 언제 사용할까?

현대 Discord 봇 개발에서는 거의 `commands.Bot`이나 `discord.Bot`(슬래시 명령어 전용)을 사용하지만,  
`discord.Client`는 아래와 같은 경우에 쓰인다.

|사용 상황|이유|
|---|---|
|단순 감시용 봇|이벤트만 처리하고 명령어 기능이 필요 없을 때|
|직접 API 레벨 제어|커스텀 명령어 파서나 독자적인 이벤트 시스템 구현 시|
|테스트용/학습용|Discord의 이벤트 구조를 직접 이해하기 위한 실험 목적|
|경량화 목적|명령어 관련 오버헤드 제거|

---

## 슬래시 커맨드(`/`)로의 진화 — `discord.Bot`

Discord는 2023년 이후로 **슬래시 커맨드(/)** 를 공식 표준으로 채택했다.  
`discord.Bot` 클래스는 `commands.Bot`보다 현대적이며,  
디스코드 클라이언트 내 자동 완성, 명령어 도움말, 인자 지원 등을 기본 제공한다.

```python
import discord

bot = discord.Bot(intents=discord.Intents.all())

@bot.event
async def on_ready():
    print("봇이 준비되었습니다!")

@bot.slash_command(description="인사하기")
async def hello(ctx):
    await ctx.respond(f"안녕, {ctx.author.mention}! 👋")

bot.run("YOUR_TOKEN")
```

> `/hello` 형태로 작동하며,  
> 사용자는 명령어를 `/`로 입력하면 자동 완성 목록에서 선택 가능하다.

---

## 명령어와 이벤트의 조합

실무에서는 `on_message`와 `@bot.command()`를 동시에 쓰기도 하지만,  
둘을 함께 사용할 때는 `await bot.process_commands(message)`를 반드시 호출해야 한다.

```python
@bot.event
async def on_message(message):
    if message.author == bot.user:
        return
    await bot.process_commands(message)  # 명령어 인식 유지
```

그렇지 않으면, `on_message` 이벤트가 명령어 파싱을 가로채서  
`./hello` 같은 명령이 작동하지 않는다.

---

## 개발 시 자주 쓰는 명령어 예시 모음

```python
@bot.command()
async def ping(ctx):
    await ctx.send(f"🏓 Pong! {round(bot.latency * 1000)}ms")

@bot.command()
async def info(ctx):
    embed = discord.Embed(title="봇 정보", color=0x00ffcc)
    embed.add_field(name="이름", value=ctx.author.name)
    embed.add_field(name="아이디", value=ctx.author.id)
    embed.set_footer(text="AI-VTuber Project by 레이")
    await ctx.send(embed=embed)
```

---

## 명령어 Prefix vs Slash Command 비교

| 구분     | Prefix(`./hello`) | Slash(`/hello`)    |
| ------ | ----------------- | ------------------ |
| 입력 방식  | 직접 텍스트 입력         | Discord UI에서 자동 완성 |
| 사용 편의성 | 익숙하지만 오타 가능       | 직관적, 오타 없음         |
| 구현 클래스 | `commands.Bot`    | `discord.Bot`      |
| 인자 입력  | 수동 파싱             | 자동 파라미터 지원         |
| 공식 권장도 | 과거 방식             | 최신 표준              |

---

## 안전한 봇 설계 원칙

1. **토큰 보호**
    
    - `.env` 또는 별도의 Python 파일 (`dico_token.py`)로 분리
        
    - `.gitignore`에 추가하여 깃허브 유출 방지
        
2. **오류 핸들링**
    
    - 모든 DM, 네트워크 요청, 파일 I/O는 `try/except`로 감싸기
        
    - 로그 출력을 통해 디버깅 용이성 확보
        
3. **비동기 함수 관리**
    
    - Discord.py의 모든 함수는 `async` 기반
        
    - 함수 내부에서 `await` 누락 시 런타임 오류 발생
        
4. **명령어 이름 충돌 방지**
    
    - 동일한 이름의 명령어, alias 중복 피하기
        
5. **API 호출 제한 고려**
    
    - Discord는 초당 호출 횟수 제한(rate limit)이 존재
        
    - 루프 내 과도한 `send()` 호출은 주의
        

---

## 최종 정리

|핵심 개념|설명|
|---|---|
|`discord.Client`|가장 기본적인 봇 객체 (이벤트 전용)|
|`commands.Bot`|명령어(`./hello`) 지원용 고수준 객체|
|`discord.Bot`|최신 `/명령어` 지원 객체|
|`Intents`|봇이 접근할 수 있는 데이터 범위를 정의|
|`Privileged Intents`|Developer Portal에서 수동 활성화 필요|
|`@bot.event`|디스코드 이벤트 감지용 (on_ready, on_message 등)|
|`@bot.command`|접두사(prefix) 기반 명령어 등록용|
|`aliases`|여러 이름으로 동일 명령 실행 가능|
|`ctx.send()`|현재 채널에 메시지 전송|
|`message.author.send()`|유저 DM 전송|
|`discord.Forbidden`|DM 차단, 권한 부족 예외|
|`discord.Status.online`|봇 상태 표시용 상수|
|`discord.Game()`|활동 표시 객체|
|`try / except`|오류 안전 처리 필수|
|`on_message` + `process_commands()`|이벤트와 명령어를 함께 쓸 때 필수 구조|

---
