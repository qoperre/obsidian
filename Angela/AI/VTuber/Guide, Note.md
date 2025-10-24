이 가이드는 단순히 내 프로그램의 원리가 궁금해서 찾아 온 사람뿐만이 아닌, 코딩을 배웠지만 실제 프로젝트를 설계하는 방법을 고민하는 사람들, 프로젝트 설계 시 배워야 할 것들이 너무 많고 깃허브에 있는 코드는 복잡하기만 하고 이해를 못하겠는 사람들, 프로젝트 진행 시 관련 api 공부법은 어떻게 가져가야 하는지 궁금한 사람들을 위해 쓰는 글이다. 따라서 이 글에서는 내가 이 프로젝트를 시작하게 된 동기, 프로젝트를 진행하면서 거쳤던 사고과정들과 계획들, 내가 찾아보면서 공부했던 것들까지 전부 상세히 적어놓을 예정이다. **단순히 코드 설명이 필요한 독자들은 깃허브에 올라올 README.md**를 참고해 주길 바란다.

+글을 쓰는 동안 사용하는 용어가 약간씩 바뀔 수 있다. 양해 부탁드린다(ex-discord/디스코드).
++이 글에서 나온 코드들 중 GPT가 작성한 코드는 표시 해 둘 것이다. 일부 코드를 제외한 텍스트는 GPT가 사용되지 않았다.
# 프로젝트 요약
https://github.com/qoperre/AI_VTuber
![[Pasted image 20251024003051.png]]
[디스코드에서 AI와 채팅을 하는 모습]![[Pasted image 20251024003324.png]]
[AI와 연동되어 스스로 움직이는 live2D 모델]

Discord의 채팅 서버에서 텍스트로 메세지를 보내면, LLM과 연동된 Discord Bot이 채팅 서버에서 답장을 준 후, TTS를 이용해 음성 채팅에서 자신의 답장을 읽어 준다. 그와 동시에 현재 Discord Bot의 감정 상태에 맞춰 Vtube Studio 모델의 표정/행동이 실시간으로 변화한다.

Discord Bot과 연동된 LLM은 현재 자신의 감정 상태, 사용자와의 대환를 통한 단기/장기기억을 가지고 있으며, 이를 통해 사용자와의 친밀도가 결정된다.


# 프로젝트 동기

'주제는 어떠한 이유로 잡게 되었나요? 특별한 동기가 있나요?' 에 대한 글이다. 아래와 같이 평소 자신이 좋아하는 것은 그 어떠한 것이든 프로젝트 주제가 될 수 있음을 인지해야 한다.(프로젝트, 말이 거창하지 실은 별 거 아니다. 나도 내 학교 사람들에게는 'AI 여친 만들기' 라는 이름으로 말하고 다녔다. <- 이것을 보면 '? 이런 이상한 주제에도 프로젝트란 이름을 붙인다고?' 라는 생각이 들지 않는가?)
### 사전 설명
 - 나는 프로그래밍으로 무언가를 구현하는 것을 좋아한다.
 - 나의 python 수준은 '기초 문법을 익힘(함수, 클래스 등)', 그러나 '라이브러리의 활용은 미숙함(GPT가 대신 해주는 것들이 많았음)'
 - 나는 ai-vtuber인 neuro-sama, 그녀의 개발자인 vedal987의 팬이다. https://www.youtube.com/@Neurosama https://vedal.ai/

나는 평소 neuro-sama가 타 스트리머들과 대화하는  영상, vedal987이 neuro-sama를 개발하는 영상을 자주 본다. 그와 동시에 프로그래머로서 "나도 언젠가 저런 ai를 만들 수 있을까?" 라는 생각을 자주 했다.
그러나 나에게는 불가능해 보이는 난이도와, 그래도 어떻게든 시도하려고 GPT를 돌렸을 때의 처참한 결과(바닥에 붙어있는 완성도)로 인해 제대로 시도해 볼 생각은 하지 못하고 있었다.

그러나 중간고사 직후, (원래는 시간이 없어야 하지만) 거의 2달의 시간이 생긴 나는 "그래. 2달이라면 뭘 어떻게든 해 볼 수는 있겠지. 그 긴 기간동안 시도했는데 실패했더라도 얻는 경험이 많을 테고." 라는 생각으로 프로젝트 계획을 시작하였다. (그래도 경험이 있어야 하니, GPT 사용은 최소화하기로 했다.)

# 첫 접근
### 나는 내가 생각한 주제와 관련한 아무런 지식이 없었다.
따라서 나는 구글에 다음과 같이 검색했다.
![[Pasted image 20251024010429.png]]
그리고 다음 한 행동은
![[Pasted image 20251024010507.png]]
이렇게 검색된 결과의 2페이지까지 나오는 '모든' 사이트들을 들어가 봤다(지금 생각해보면 1페이지까지만 전부 들어가 봤어도 크케 문제는 없었을 것 같다). 위에서 말했다시피, 나는 아무런 종류의 지식도 없으니 ai-vtuber과 관련된 '전체적인 지식'이 필요했고, 그 다음으로 이어진 사고(思考)를 시행한 것이다.

당연히 그 모든 사이트들을 정독해보지는 않았다. 사실 2/3 이상의 사이트들은 대충 둘러보기만 했다. 그리고 내가 가장 열심히 읽은 페이지는 '위키피디아'와 '프로젝트 가이드'였다(내가 만들려는 것이 어떤 것이고, 어떻게 만들어야 하는지에 대한 기반).
https://en.wikipedia.org/wiki/Neuro-sama
https://www.youtube.com/watch?v=roL_CRH4QME
https://www.youtube.com/watch?v=1MrDnLBc-wQ
* *영어여도 한 번 읽기를 시도해보자. 사이트 -> 페이지 번역, 유튜브 -> 자막 생성 -> 자동 번역

이 사이트들은 자신의 프로그램이 어떻게 작동하는지 잘 보여주었고, 이를 통해 나도 초기 모델을 구상할 수 있게 되었다.(매우 간단하다)

#### 디스코드 채팅 치기 -> 채팅을 LLM이 받아서 답장 -> 그 답장을 원래 채널에 전송

# 이후 공부
### 구상까지는 좋았으나, 나는 저 3개의 과정들 중 단 1개도 할 수 있는 지식이 없는 상태였다.

그래서 나는 저 과정들을 3개의 작은 문제들로 쪼갰다.
#### 디스코드 입력 -> 출력 /  디스코드 메세지 python 콘솔로 출력하기 / LLM으로 텍스트 생성

이렇게 쪼갠 이유는 2가지가 있었다.
1. 3개의 단계를 공통분모끼리 묶었더니 2개/1개/1개로 나뉜다.
2. 'LLM으로 텍스트 생성' 자체는 내가 할 줄 안다(공부 할 필요가 없다)

이렇게 보니 '현재 내가 모르는 것'이 '디스코드의 채팅 처리'였고, 나는 '아주 간단한, 기본만 있는 디스코드 봇 만들기'를 처음 공부 목표로 잡았다.

또 나는 아무것도 모르니
![[Pasted image 20251024020544.png]]
이렇게 친 후 밑에 나오는 AI가 
![[Pasted image 20251024020647.png]]
라길래 내가 그나마 할 수 있는 python을 이용해서 봇 만들기를 시도해보았다(또 1페이지의 글을 전부 읽업보고 가장 끌리는 것으로 공부했다).
https://kante-kante.tistory.com/34
* *끌린다의 기준: 설명이 친절한가? 글이 단발성이 아닌 여러 개의 글들이 있는가? 외부 링크들이 있는가?

이후로는 그냥 이 글을 보면서 디스코드 봇의 기본 세팅, 입력/출력 등을 공부했다.

공부하는데 팁을 좀 주자면
1. 어차피 공부하는 입장이다. 아무것도 모르면 코드는 꼭 한 번씩은 따라 쳐보자.
2. 프로그래밍 언어 자체가 처음이 아니라면, 코드를 그냥 치는 것 보다는 자신의 입맛대로 바꿔 쳐보는 것도 좋다.
3. 길게 에러 메세지가 난다면 GPT를ㄹ 이용해보는 것도 좋다. 그러나 다음에 같은 에러가 한 번 더 나왔을 때, 그때도 GPT를 다시 쓰는 것은 좋지 못하다. 그러니 에러 해결법은 잘 익혀주자.
내 예시로 보여주면
```python
import discord

from discord.ext import commands

from dico_token import Token

  

# client = discord.Client()  

# 사용자가 명령어 입력 시 ./를 입력하고 명령어 입력
# client = commands.Bot(command_prefix='./')

  
# 신버전에서는 intents 인자 필수
# ================== 신버전 설정 =====================


# Intents 설정
intents = discord.Intents.default()
intents.message_content = True # 메세지 내용을 읽을 수 있도록

  
# Bot 생섬 시 intents를 전달해야 함
client = commands.Bot(command_prefix='./', intents=intents)
  

# ===================================================

  

# on_ready는 시작할 때 한 번만 실행

@client.event
async def on_ready():
    print('Login...')
    print(f'{client.user}에 로그인하셨습니다.')
    print(f'ID: {client.user.name}')
    await client.change_presence(status=discord.Status.online, activity=discord.Game('VS Code로 개발'))
    # Status 대문자, online: 온라인, idle: 자리 비움, dnd: 방해 금지

  

# @client.event
# async def on_message(message):
#     if message.author == client.user:
#         return  # 봇 자신의 메시지 무시

  

#     # message.content.startswith()는 해당 문자로 시작하는 단어에 대해서
#     # 인식하여 메세지 전송, ==로 비교 시 해당 문자만 인식
#     if message.content.startswith('테스트'):
#         await message.channel.send("{} | {}, 안녕!".format(message.author, message.author.mention))

  

#     if message.content == '테스트':
#         # 채널에 메세지 전송
#         await message.channel.send("{} | {}, 어서오세요!".format(message.author, message.author.mention))

  

#     # DM 보내기 시도
#     # await message.author.send("{} | {} 유저님, 환영합니다.".format(message.author, message.author.mention))
#     try:
#         await message.author.send(f"{message.author} | {message.author.mention} 유저님, 환영합니다.")
#     except discord.Forbidden:
#         print(f"[DM 불가] {message.author} 님이 DM을 받을 수 없습니다.")
#     except discord.HTTPException as e:
#         print(f"[DM 에러] {message.author} : {e}")

  

# 아래 코드들은 client.event의 on_message를 주석 처리하고 실행

  
@client.command(aliases=['hi'])
async def hello(ctx):
    await ctx.send("안녕하세요!")

  
@client.command(aliases=['로그인', '접속하기'])
async def login(ctx):
    await ctx.channel.send("{} | {}님, 어서오세요!".format(ctx.author, ctx.author.mention))

  
client.run(Token)

  
"""
실행 시 오류:
discord.errors.PrivilegedIntentsRequired: ... requesting privileged intents that have not been explicitly enabled ...
------> 권한이 필요한 intent(privileged intents)를 요청했는데,
Discord 개발자 페이지에서 해당 intent를 활성화하지 않을 것!
  
해결법: Discord 개발자 포털로 이동 -> 사용하는 봇 이름 클릭
-> "Bot"탭 클릭 -> “Privileged Gateway Intents”에서 필요한 것 켜기
- PRESENCE INTENT (상태 정보 감지)
- SERVER MEMBERS INTENT (서버 멤버 감지)
- MESSAGE CONTENT INTENT (메시지 내용 읽기 — 명령어 인식용)
"""

"""
실행 시 오류:
discord.errors.HTTPException: 400 Bad Request (error code: 50007): Cannot send messages to this user
------> 해당 유저가 DM을 비활성화했거나, 봇이 유저와 친구가 아니거나, DM을 차단한 유저에게 메세지를 보낼 때

해결법: try/except 넣기
    try:
        await message.author.send(f"{message.author} | {message.author.mention} 유저님, 환영합니다.")
    except discord.Forbidden:
        print(f"[DM 불가] {message.author} 님이 DM을 받을 수 없습니다.")
    except discord.HTTPException as e:
        print(f"[DM 에러] {message.author} : {e}")
"""

"""
실행 시 오류: 400 Bad Request (error code: 50007): Cannot send messages to this user
------> 위에 경우가 아닌데도 뜸 -> 봇이 보내는 메세지도 함수에 들어가는 게 문제!(봇이 자기자신에게 메세지 보내는 건 좀 이상하지)

  
해결법: 봇 자신의 메세지만 무시처리
    if message.author == client.user:
        return  # 봇 자신의 메시지 무시
"""
```
이런 식으로 공부했다. 보다시피 진짜 실행코드는 20줄이 채 안되지만 총 코드길이는 100줄이 넘는다.

아무튼 이런 식으로 얻개 된 기초 디스코드 봇 개발 능력 + 원래 알고 있던 LLM 프롬프트 생성으로 1단계를 완성시켰다.

* *의외로 LLM api를 초보자도 쉽게 설명해주는 곳이 많이 없었다.
* https://velog.io/@dyd1308/Gemini-api-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0
* https://ai.google.dev/gemini-api/docs/quickstart?hl=ko
* *다음 정도가 가장 쉬우면서도 잘 설명해준 것 같지만, 분명히 모르는 부분이 생길 것이다. 그 때는 GPT의 도움을 받아 이해해보고, 이해한 내용들을 글로 한 번 정리해보자

![[Pasted image 20251024023249.png]]

![[Pasted image 20251024023331.png]]
[AI의 성격은 무시하도록 하자]

# 1단계 완성, 이후 계획
여기까지 해서 프로토타입을 완성했다. 
사실 이것까지만 진행해도 하나의 '프로젝트'가 완성이 된 것이다. 이것을 통해 얻은 것도 충분히 많았고, GPT 또한 쓰지 않고 목표를 완수했으니 여기서 끝내도 괜찮다.

* *하나의 '프로젝트'의 과정은 이 위까지가 끝이다. 아래부터는 '완성된 프로젝트를 보강하는 방법/기능을 추가하는 방법'에 대한 내용들이다.

그러나 나는 욕심이 많았고, 이후 계획들(내가 추가하고 싶은 것들)을 여럿 생각했다.

### 두 번째 모델 계획(첫 번째 수정)
