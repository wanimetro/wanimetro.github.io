---
title: "Amazon Bedrock 시작하기 | 생성형 AI와 첫 모델 호출"
date: 2026-08-12 19:00:00 +0900
categories: [AI & Cloud, Amazon Bedrock]
tags: [aws, amazon-bedrock, generative-ai, langchain]
---

## 학습 배경

저번 학기 AWS 동아리에 들어가 여러 활동을 했지만, 활동을 하면서 오히려 AWS에 대한 지식이 아직 많이 부족하다는 것을 느꼈다.

또한 여름방학 전공연수에서 생성형 AI에 대해 깊게 배우면서, 생성형 AI 기술이 실제 클라우드 환경에서는 어떻게 활용되는지 궁금해졌고 이를 AWS와 연결해서 공부해보고 싶었다.

그러던 중 내 니즈에 딱 맞는 『Amazon Bedrock으로 시작하는 실전 생성형 AI 개발』이라는 책을 발견했다.

이 책을 읽으며 Amazon Bedrock을 중심으로 AWS의 생성형 AI 서비스를 공부하고, 책의 내용을 직접 실습해보면서 새롭게 알게 된 내용과 실습 중 발생한 문제 및 해결 과정을 함께 기록해보려고 한다.

# 00 AWS 계정 준비하기

루트 계정 대신 IAM 사용자 계정을 생성했다.

루트 계정은 AWS 계정 전체에 대한 권한을 가지고 있기 때문에, 루트 계정 대신 권한이 제한된 사용자 계정을 만들어 용도에 맞게 사용하는 것이 좋다.

이를 통해 개별 사용자의 활동을 추적할 수 있고, 잠재적인 보안 위험과 피해 규모를 최소화할 수 있다.

## AWS CLI

**AWS CLI**는 **AWS 웹사이트에서 마우스로 하던 일을 터미널 명령어로 할 수 있게 해주는 프로그램**이다.

```text
내 컴퓨터
   │
   ├─ 브라우저 ─────→ AWS Console
   │                    클릭해서 AWS 사용
   │
   └─ AWS CLI ──────→ AWS
                        명령어로 AWS 사용
```

## AWS CLI가 다른 IAM 사용자로 인식되는 문제 발생

### 문제 상황

AWS CLI 설치 및 IAM 사용자 설정 후 현재 AWS 인증 정보를 확인하기 위해 다음 명령어를 실행했다.

```bash
aws sts get-caller-identity
```

그런데 새로 설정한 `admin` IAM 사용자가 아니라 **이전에 사용했던 다른 AWS 계정의 사용자 정보가 출력되는 문제**가 발생했다.

### 원인 확인

AWS CLI가 어떤 인증 정보를 사용하고 있는지 확인했다.

```bash
aws configure list
```

확인 결과:

```text
NAME        VALUE               TYPE          LOCATION
profile     <not set>           None          None
access_key  ****************    env
secret_key  ****************    env
region      us-east-1           config-file   ~/.aws/config
```

`access_key`와 `secret_key`의 `TYPE`이 **`env`**로 표시되고 있었다.

즉, AWS CLI가 새로 설정한 credentials가 아니라 **Windows 환경변수에 기존에 저장되어 있던 AWS Access Key와 Secret Access Key를 사용하고 있었던 것**이 원인이었다.

### 해결

현재 CMD 세션에 설정되어 있던 AWS 관련 환경변수를 제거했다.

```cmd
set AWS_ACCESS_KEY_ID=
set AWS_SECRET_ACCESS_KEY=
set AWS_SESSION_TOKEN=
```

이후 다시 인증 상태를 확인했다.

```bash
aws configure list
```

그리고 실제 AWS 요청에 사용되는 IAM 사용자를 확인했다.

```bash
aws sts get-caller-identity
```

최종적으로 새로 설정한 `admin` IAM 사용자가 정상적으로 출력되는 것을 확인했다.

→ AWS CLI는 여러 위치에서 credentials를 가져올 수 있고, 우선순위에 따라 실제 사용할 인증 정보를 결정한다.

특히,

```text
aws configure
       ↓
credentials 저장
       ↓
"당연히 이걸 사용하겠지?" ❌
       ↓
환경변수에 다른 credentials 존재
       ↓
AWS CLI가 환경변수 credentials 사용
       ↓
다른 AWS 사용자로 인증
```

같은 상황이 발생할 수 있다.

따라서 AWS CLI 인증 문제가 발생하면 우선

```bash
aws configure list
```

로 **현재 어떤 credentials가 어디에서 로드되고 있는지 확인**하고,

```bash
aws sts get-caller-identity
```

로 **현재 AWS가 나를 어떤 사용자로 인식하고 있는지 확인**하는 것이 좋다.

# 01 생성형 AI란 무엇인가

## 1-1 생성형 AI의 정의

생성형 AI는 방대한 데이터로부터 학습한 패턴과 구조를 이해하고, 이를 바탕으로 텍스트, 이미지, 음악, 비디오 등 다양한 형태의 결과물을 생성한다.

* **전통적 AI 시스템**: 데이터 분석, 패턴 인식, 분류 작업 등에 초점
* **생성형 AI**: 학습한 패턴을 바탕으로 새로운 콘텐츠를 생성

### 궁금증 생김 🤔

이 책에서는 생성형 AI의 역할을 설명하면서 '새로운 이미지 생성'을 예시로 들었다.

그런데 실제로 나는 이미지 생성 외에도 설명을 듣거나, 글을 작성하거나, 코드를 작성하는 등 다양한 목적으로 생성형 AI를 사용하고 있다.

→ 생성형 AI의 핵심은 **학습한 데이터의 패턴을 바탕으로 새로운 콘텐츠를 생성하는 것**이고, 그 콘텐츠의 종류가 여러 가지라고 이해했다.

* **텍스트 생성**: ChatGPT 같은 LLM이 설명, 답변, 요약, 코드 등을 생성
* **이미지 생성**: 이미지 생성 모델이 새로운 이미지를 생성
* **음성/음악 생성**: 음성이나 음악 생성
* **영상 생성**: 새로운 영상 생성
* **멀티모달 생성**: 텍스트, 이미지, 음성 등을 함께 이해하고 생성

전통적인 검색 시스템은 기본적으로...

```text
질문
 ↓
관련된 기존 정보 찾기
 ↓
정보 반환
```

LLM은...

```text
질문
 ↓
입력과 맥락 이해
 ↓
학습한 패턴을 바탕으로
 ↓
적절한 응답 생성
```

## 1-2 AWS의 생성형 AI 전략

대규모 언어 모델을 훈련시키기 위해서는 방대한 양의 데이터와 컴퓨팅 자원이 필요하다.

생성형 AI가 하나의 **기술**이라면, AWS는 그 기술을 실제 서비스에서 사용할 수 있게 해주는 **클라우드 플랫폼**이라고 이해했다.

### Amazon Bedrock

**AWS에서 생성형 AI 모델을 사용할 수 있게 해주는 서비스**이다.

개발자가 거대한 AI 모델을 직접 학습시키지 않고도 Bedrock을 통해 여러 Foundation Model을 애플리케이션에서 사용할 수 있다.

예를 들어 생성형 AI 앱을 하나 만든다고 해보자.

```text
사용자
  ↓
내가 만든 웹/앱
  ↓
생성형 AI 모델
  ↓
"답변 생성"
```

하지만 생성형 AI 모델을 직접 구축하고 운영하려면 GPU 서버, 모델 배포, 확장, 보안, 데이터 저장 등 신경 쓸 것이 많다는 문제점이 생긴다.

AWS는 이러한 것들을 클라우드에서 제공한다.

```text
               AWS ☁️
┌─────────────────────────────┐
│ 서버          → EC2         │
│ 파일 저장      → S3          │
│ 데이터베이스   → RDS 등      │
│ 권한/보안      → IAM         │
│                             │
│ 생성형 AI      → Bedrock    │
└─────────────────────────────┘
```

AWS에서 AI/ML을 다루는 대표적인 서비스로 Amazon SageMaker와 Amazon Bedrock이 있다.

**SageMaker = AI/ML 모델을 만들고 학습하고 배포하는 데 활용할 수 있는 플랫폼**

**Bedrock = 이미 만들어진 Foundation Model을 활용해 생성형 AI 애플리케이션을 만드는 서비스**

# 02 Amazon Bedrock 시작하기

Bedrock에서 제공하는 주요 모델들을 텍스트 생성, 이미지 생성, 임베딩 모델로 나누어 학습했다.

### 텍스트 생성 모델

**1. Amazon Titan Text 모델**

Amazon이 자체 개발하여 Bedrock에서 제공하는 모델이다.

**2. Anthropic Claude 모델**

Claude 3 시리즈는 텍스트와 이미지 입력을 모두 처리할 수 있는 멀티모달 모델이다.

**3. Meta Llama 모델**

Meta에서 개발한 Llama 모델로, 복잡한 자연어 처리 작업 등에 활용할 수 있다.

### 이미지 생성 모델

**1. Amazon Titan Image Generator**

텍스트-이미지 변환 모델이다.

텍스트 프롬프트를 기반으로 이미지를 생성하고 편집하는 등의 작업에 사용할 수 있다.

**2. Stability AI**

다양한 아트 스타일의 이미지를 생성할 수 있다.

### 임베딩 모델

구조화되지 않은 방대한 양의 데이터를 효과적으로 분석하고 활용할 수 있게 해주는 모델이다.

임베딩은 문서의 전체 맥락과 의미를 포착하여 텍스트나 이미지 등의 데이터를 고차원의 숫자 벡터로 변환한다.

→ 유사한 의미를 가진 데이터들이 벡터 공간에서 가까이 위치하게 되어 의미적 유사성을 비교하거나 관련 정보를 검색하는 데 활용할 수 있다.

## 직접 실습

AWS 환경에서 Bedrock을 가장 빠르고 쉽게 사용해보는 방법은 AWS Management Console에 로그인하여 직접 사용하는 것이다.

이를 위해 Playground를 사용했다.

```text
                 Amazon Bedrock
                      │
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
    Anthropic       Amazon        Meta ...
        │
   ┌────┴────┐
   ↓         ↓
 Haiku     Sonnet
빠름/저렴    강력함
```

**Bedrock Playground = 코드를 작성하지 않고 Bedrock의 생성형 AI 모델을 직접 선택하고 테스트해보는 공간**

### 토큰

언어 모델에서 토큰은 텍스트를 처리하는 기본 단위이다.

토큰화는 텍스트를 모델이 처리할 수 있는 작은 단위로 나누는 과정을 말한다.

Bedrock에서 모델을 사용할 때는 모델별 입력 및 출력 토큰 사용량에 따라 비용이 달라질 수 있다.

Playground에서는 코드를 작성하지 않고 직접 모델을 선택해 프롬프트를 입력하고 답변을 얻어낼 수 있다.

하지만 실제 애플리케이션에서는 사용자가 Playground에 직접 들어가 질문하는 것이 아니라, **내가 작성한 프로그램이 Bedrock의 모델을 호출하고 응답을 받아야 한다.**

따라서 Python과 LangChain을 이용해 이 과정을 구현해보기로 했다.

## LangChain으로 Bedrock 사용하기

```text
사용자 입력
    ↓
Python 프로그램
    ↓
LangChain
    ↓
Amazon Bedrock
    ↓
Foundation Model
    ↓
생성된 응답
    ↓
Python 프로그램
```

즉, LangChain은 AI 모델 자체가 아니라 **Python 프로그램과 Bedrock의 모델을 연결하고 모델을 쉽게 사용할 수 있도록 도와주는 프레임워크**이다.

처음에는 Claude Sonnet 5 모델을 선택해서 아래와 같은 코드를 작성했다.

```python
from langchain_aws import ChatBedrockConverse

llm = ChatBedrockConverse(
    model_id="anthropic.claude-sonnet-5",
    region_name="eu-north-1"
)

response = llm.invoke(
    "Amazon Bedrock이 무엇인지 한 문장으로 설명해줘."
)

print(response.content)
```

단순히 보면 몇 줄밖에 되지 않는 코드였지만, 실제 실행 과정에서는 여러 문제가 발생했다. 🫠

---

## 문제 1. `bedrock:InvokeModel` 권한 오류

코드를 실행하자 다음과 같은 오류가 발생했다.

```text
AccessDeniedException:

User ... is not authorized to perform:
bedrock:InvokeModel

because no identity-based policy allows
the bedrock:InvokeModel action
```

처음에는 코드가 잘못된 줄 알았지만 에러 메시지를 자세히 보니 핵심은

```text
bedrock:InvokeModel
```

이 부분이었다.

즉, 현재 AWS CLI에서 `Admin` 사용자로 정상적으로 인증은 되었지만 **해당 IAM 사용자가 Bedrock의 모델을 호출할 권한은 가지고 있지 않았다.**

여기서 AWS의 **인증**과 **권한**이 별개라는 것을 알게 되었다.

IAM에서 현재 사용자의 권한을 확인해 보니 처음에는 다음 정책만 직접 연결되어 있었다.

```text
IAMUserChangePassword
```

따라서 Bedrock을 사용할 수 있도록 IAM 그룹을 통해 필요한 정책을 연결했다.

```text
AdministratorAccess
AmazonBedrockFullAccess
```

이후 IAM 화면에서 현재 사용자에게 해당 권한이 적용된 것을 확인했다.

## 문제 2. 권한을 설정했는데 Claude Sonnet 5가 실행되지 않음

IAM 권한을 추가했으니 이제 실행될 것이라고 생각했지만, 이번에는 Bedrock Playground에서도 Claude Sonnet 5 호출에 실패했다.

```text
AccessDeniedException

anthropic.claude-sonnet-5 is not available for this account.
```

이번 오류는 앞에서 발생했던 IAM 권한 오류와 조금 달랐다.

앞에서는

```text
Bedrock을 호출할 권한이 없음
```

이 문제였다면, 이번에는

```text
Claude Sonnet 5 자체를 현재 계정에서 사용할 수 없음
```

이 문제였다.

즉, **Model Catalog에서 모델이 보이는 것과 현재 계정에서 그 모델을 실제로 호출할 수 있는 것은 별개**였다.

그래서 Python 코드를 계속 수정하기 전에 Playground에서 실제 호출 가능한 모델부터 확인해 보기로 했다.

## 사용 가능한 모델 찾기

Bedrock Playground의 모델 선택 화면을 확인해 보니 OpenAI의 다음 모델을 선택할 수 있었다.

```text
gpt-oss-120b
gpt-oss-20b
```

이번 실습의 목적은 모델의 성능을 비교하는 것이 아니라 **LangChain을 이용해 Bedrock 모델을 실제 Python 코드에서 호출하는 과정을 이해하는 것**이었기 때문에 `gpt-oss-20b`를 선택했다.

Playground에서 먼저 다음 프롬프트를 입력했다.

```text
Amazon Bedrock이 무엇인지 한 문장으로 설명해줘.
```

이번에는 정상적으로 응답이 생성되었다.

```text
Amazon Bedrock은 AWS가 제공하는 관리형 서비스로,
다양한 기반 모델을 활용해 생성형 AI 애플리케이션을
구축할 수 있도록 지원한다.
```

이 과정을 통해 적어도 **현재 계정에서 gpt-oss-20b 모델을 호출할 수 있다는 것**을 확인할 수 있었다.

Playground에서 먼저 테스트한 것이 꽤 중요했다.

```text
Playground에서도 실패
→ AWS 계정 / 모델 접근 문제일 가능성

Playground에서는 성공
Python에서는 실패
→ 코드 / Model ID / 인증 / 실행 환경 등을 확인
```

이렇게 문제의 범위를 좁힐 수 있기 때문이다.

## 문제 3. `No module named 'langchain_aws'`

AWS 권한 문제를 해결한 뒤 코드를 다시 실행했다.

이번에는 AWS 오류 대신 Python에서 새로운 오류가 발생했다.

```text
ModuleNotFoundError:
No module named 'langchain_aws'
```

처음에는 `langchain_aws` 패키지가 설치되지 않은 줄 알았다.

하지만 이전에는 같은 프로젝트에서 해당 패키지를 사용하고 있었다.

문제는 **Python 실행 환경**이었다.

다음과 같이 실행했을 때,

```bash
python chapter02.py
```

현재 시스템에서 기본으로 연결된 Python으로 프로그램이 실행되었다.

하지만 필요한 패키지는 이 프로젝트에서 사용하는 **Pipenv 가상환경**에 설치되어 있었다.

구조로 보면 다음과 같았다.

```text
내 컴퓨터
│
├─ 기본 Python
│    └─ langchain_aws 없음 ❌
│
└─ bedrock-study Pipenv 가상환경
     └─ langchain_aws 설치됨 ⭕
```

따라서 Pipenv 가상환경을 사용하여 실행하도록 변경했다.

```bash
py -m pipenv run python chapter02.py
```

이후 `langchain_aws`를 정상적으로 불러올 수 있었다.

Python에서 **"패키지를 설치했다"**와 **"현재 실행 중인 Python에서 그 패키지를 사용할 수 있다"**는 두 말을 구분해야 한다.

가상환경을 사용하는 프로젝트에서는 **어떤 Python 인터프리터로 코드를 실행하고 있는지** 확인하는 것이 중요하다.

앞으로 `ModuleNotFoundError`가 발생하면 바로 패키지를 다시 설치하기보다는 현재 Python 실행 환경부터 확인해야겠다.

# 최종 모델 호출 성공

여러 오류를 해결한 뒤 최종적으로 사용한 코드는 다음과 같다.

```python
from langchain_aws import ChatBedrockConverse

llm = ChatBedrockConverse(
    model_id="openai.gpt-oss-20b-1:0",
    region_name="eu-north-1"
)

response = llm.invoke(
    "Amazon Bedrock이 무엇인지 한 문장으로 설명해줘."
)

print(response.content)
```

실행은 Pipenv 환경에서 진행했다.

```bash
py -m pipenv run python chapter02.py
```

그리고 드디어 모델의 응답을 Python 터미널에서 받을 수 있었다.

```text
Amazon Bedrock은 AWS가 제공하는 관리형 서비스로,
다양한 기반 모델(LLM, VLM 등)을 손쉽게 활용해
생성형 AI 애플리케이션을 구축할 수 있게 해주는 플랫폼입니다.
```

즉, 이번에 실제로 구현한 것은 다음 구조이다.

```text
"Amazon Bedrock이 무엇인지 설명해줘."
                │
                ▼
           Python 프로그램
                │
                ▼
              LangChain
                │
                ▼
         ChatBedrockConverse
                │
                ▼
        Amazon Bedrock API
                │
                ▼
         OpenAI gpt-oss-20b
                │
            응답 생성
                │
                ▼
         response.content
                │
                ▼
              터미널 출력
```

Playground에서는 **내가 직접 Bedrock에 프롬프트를 입력했다면**, 이번에는 **Python 프로그램이 나를 대신해서 Bedrock 모델에 요청을 보내고 응답을 받아온 것**이다.

이 차이가 이번 실습에서 가장 중요한 부분이라고 생각한다.

## Bedrock API 이해하기

API와 상호작용할 때는 일반적으로 JSON 형식을 사용하여 요청과 응답을 주고받는다.

하지만 Bedrock에는 여러 AI 모델이 있기 때문에 모델마다 요청 형식에 차이가 생길 수 있다. 따라서 LLM을 프로그래밍 방식으로 활용하려면 모델마다 다른 호출 방식을 익혀야 하는 어려움이 있다.

책에 너무 많은 개념이 나왔는데, 우선 이 3개만이라도 정확히 알고 가기로 했다..

```text
bedrock-runtime
└─ 모델을 실제로 사용하는 통로
    ├─ InvokeModel → 모델별 형식에 맞춰 직접 호출
    └─ Converse    → 통일된 형식으로 대화형 모델 호출
```

### `bedrock-runtime`

Bedrock의 Foundation Model에 실제 추론 요청을 보내기 위한 런타임 서비스이다.

즉, 모델을 찾거나 설정하는 것이 아니라 **실제로 모델에 입력을 보내고 결과를 받는 역할**을 한다.

### `InvokeModel`

모델을 직접 호출하는 방식이다.

각 모델이 요구하는 입력 형식에 맞게 요청을 구성해야 하기 때문에 모델마다 요청 구조가 달라질 수 있다.

```text
Python
  ↓
InvokeModel
  ↓
Foundation Model
  ↓
응답
```

### `Converse`

여러 대화형 모델을 비슷한 형식으로 호출할 수 있도록 제공되는 방식이다.

이번 실습에서 사용한 `ChatBedrockConverse`도 이름 그대로 Bedrock의 Converse 방식을 이용한다.

```text
Python
  ↓
LangChain
  ↓
ChatBedrockConverse
  ↓
Bedrock Converse
  ↓
Foundation Model
```


다음 챕터에서는 프롬프트 엔지니어링에 대해 공부해볼 예정이다.
