---
title: "ChatGPT가 만든 Snake Game 개선하기 | Pygame 첫 실습"
date: 2026-09-15 11:00:00 +0900
categories: [Coursework, Game Programming]
tags: [python, pygame, snake-game, game-programming, devlog]
---

## 📍 Goal

게임프로그래밍입문 수업의 첫 번째 과제로, ChatGPT로 생성한 Snake Game 코드를 기반으로 직접 개선할 기능과 버그를 찾아 수정했다.

처음부터 코드를 읽으며 수정할 부분을 찾기보다는, 우선 기존 Snake Game을 직접 실행하고 플레이해보았다. 실제로 게임을 사용해보면서 불편한 점이나 추가되었으면 하는 기능을 찾는 방식으로 접근했다.

그 결과 다음 네 가지 기능을 추가하거나 개선하기로 했다.

* 플레이 중 현재 점수 표시
* 게임 BGM 추가
* 게임 오버 화면에 RESTART 버튼 추가
* 게임 오버 상태에서 X 버튼이 작동하지 않는 오류 수정

이번 실습의 목표는 LLM이나 생성형 AI를 이용해 완성된 코드를 다시 생성하는 것이 아니라, **기존 코드를 직접 읽고 필요한 부분을 찾아 수정하는 과정을 경험하는 것**이었다.

pygame을 처음 사용했기 때문에 관련 함수나 이벤트 처리 방식에 대한 사전 지식도 거의 없었다. 따라서 필요한 기능을 먼저 정한 뒤, pygame 공식 문서와 튜토리얼에서 구현 방법을 찾아 기존 코드에 직접 적용하는 방식으로 진행했다.

---

## 📍 Implementation

### 1. 플레이 중 현재 점수 표시

#### 문제점 및 목표

기존 코드에서는 게임이 종료된 후에만 최종 점수를 확인할 수 있었다.

Snake Game은 음식을 먹을수록 점수가 올라가는 방식이기 때문에, 플레이 중에도 현재 점수를 바로 확인할 수 있다면 게임 진행 상황을 더 직관적으로 알 수 있을 것이라고 생각했다.

따라서 첫 번째 목표는 **게임 플레이 중 현재 점수가 화면에 계속 표시되도록 만드는 것**으로 정했다.

#### 처음 생각한 방법

기존 코드에는 다음과 같은 함수가 존재했다.

```python
def Your_score(score):
    value = pygame.font.SysFont('comicsans', 30).render(
        "Your Score: " + str(score), True, WHITE
    )
    window.blit(value, [0, 0])
```

처음에는 `Your_score()`가 현재 점수를 `value` 형태로 반환하는 함수라고 생각했다.

하지만 관련 코드를 찾아보면서 `render()`는 문자열을 화면에 표시할 수 있는 이미지 형태의 Surface로 만들고, `window.blit()`은 만들어진 Surface를 게임 화면에 그려주는 역할을 한다는 것을 알게 되었다.

즉, `Your_score()`는 점수 값을 반환하는 함수가 아니라 **현재 점수를 화면에 그려주는 함수**였다.

기존 코드에서는 `game_close == True`, 즉 게임 오버 상태에서만 `Your_score(score)`가 실행되고 있었다.

```python
while game_close == True:
    window.fill(BLACK)
    Your_score(score)
    pygame.display.update()
```

그렇다면 일반적인 게임 플레이 상태에서도 `Your_score(score)`를 호출하면 현재 점수를 계속 표시할 수 있을 것이라고 생각했다.

#### 구현

게임 화면을 그리는 부분에 다음 코드를 추가했다.

```python
# 점수를 게임 화면에 그림
Your_score(score)
pygame.display.update()
```

여기서 `Your_score(score)`를 `pygame.display.update()`보다 먼저 실행하도록 했다.

pygame에서는 먼저 화면에 필요한 요소를 그린 뒤 `pygame.display.update()`를 호출해야 실제 화면에 변경 사항이 반영되기 때문이다.

수정 후 게임을 실행해보니 음식을 먹을 때마다 좌측 상단의 점수가 `0 → 1 → 2 → ...`와 같이 정상적으로 증가하는 것을 확인할 수 있었다.

---

### 2. 게임 BGM 추가

#### 문제점 및 목표

기존 Snake Game을 직접 플레이했을 때는 아무런 소리가 나지 않았다.

'무한의 계단'처럼 단순한 조작을 반복하면서 높은 점수를 목표로 하는 게임에서도 BGM이나 효과음이 게임의 분위기를 만들고, 반복적인 플레이가 지루해지는 것을 줄여준다고 생각했다.

그래서 두 번째 기능으로 **게임을 플레이하는 동안 BGM이 반복 재생되도록 하는 기능**을 추가하기로 했다.

#### 처음 생각한 방법

처음에는 저작권 문제가 없는 BGM을 찾아 게임 코드에 음원 링크를 연결하면 될 것이라고 생각했다.

하지만 pygame의 음악 기능을 조사해보니 웹 링크를 직접 연결하는 방식이 아니라, **음악 파일을 프로젝트에 저장한 뒤 `pygame.mixer.music`을 이용해 불러와 재생하는 방식**이었다.

무료 라이선스 음원을 제공하는 Pixabay Music에서 사용할 BGM을 찾아 `bgm.mp3`라는 이름으로 프로젝트에 저장했다.
(사실 저 BGM이 기묘한 이야기 BGM이랑 비슷한 것 같아서 선택했다ㅎㅎ)

#### 구현

pygame 공식 문서의 `pygame.mixer.music` 사용 방법을 참고해 다음 코드를 추가했다.

```python
# 백그라운드 음악 설정
pygame.mixer.music.load("bgm.mp3")
pygame.mixer.music.play(-1)
```

`pygame.mixer.music.load("bgm.mp3")`를 통해 음악 파일을 불러오고, `pygame.mixer.music.play(-1)`을 이용해 재생하도록 했다.

`play()`의 `loops` 값으로 `-1`을 전달하면 음악이 계속 반복 재생된다. 따라서 게임을 오래 플레이하더라도 BGM이 끊기지 않도록 만들 수 있었다.

실행 결과 게임을 시작하면 BGM이 자동으로 재생되고, 플레이하는 동안 계속 반복되는 것을 확인했다.

---

### 3. 게임 오버 후 RESTART 버튼 추가

#### 문제점 및 목표

기존 코드를 실행해보니 게임이 끝난 뒤 다시 플레이하는 과정이 직관적이지 않았다.

기존 코드에는 키보드의 `C`를 누르면 `gameLoop()`를 다시 실행하는 코드가 존재했다.

```python
if event.key == pygame.K_c:
    gameLoop()
```

하지만 게임 화면에는 이러한 조작 방법이 표시되어 있지 않았기 때문에, 코드를 모르는 사용자는 게임을 어떻게 다시 시작해야 하는지 알기 어려웠다.

그래서 게임 오버 화면만 보고도 쉽게 다시 시작할 수 있도록 **클릭 가능한 RESTART 버튼**을 추가하기로 했다.

#### 처음 생각한 방법

처음에는 게임 창을 생성하는 `pygame.display.set_mode()`와 비슷한 방식으로 `restart_button`을 정의하고 크기를 지정하면 버튼을 만들 수 있을 것이라고 생각했다.

하지만 방법을 조사하면서 게임 창과 버튼은 생성 방식이 다르다는 것을 알게 되었다.

게임 창 자체는 `pygame.display.set_mode()`로 생성하지만, 버튼처럼 특정 영역을 만들기 위해서는 `pygame.Rect()`를 사용할 수 있다.

또한 사각형을 화면에 그리는 것만으로는 버튼처럼 동작하지 않는다. **마우스 클릭 이벤트가 발생했는지 확인하고, 클릭 위치가 해당 사각형 내부인지 검사하는 과정**이 추가로 필요했다.

#### 구현

먼저 `pygame.Rect()`를 이용해 RESTART 버튼이 들어갈 영역을 만들었다.

```python
restart_button = pygame.Rect(220, 250, 200, 50)
pygame.draw.rect(window, GREEN, restart_button)
```

그 위에 `RESTART`라는 글자를 표시했다.

```python
button_font = pygame.font.SysFont('comicsans', 30)
button_text = button_font.render("RESTART", True, WHITE)
window.blit(button_text, [260, 260])
```

하지만 여기까지는 버튼처럼 보이는 사각형을 화면에 그린 것뿐이다.

실제로 클릭했을 때 동작하도록 다음과 같이 마우스 이벤트를 추가했다.

```python
if event.type == pygame.MOUSEBUTTONDOWN:
    if restart_button.collidepoint(event.pos):
        # 게임 초기화
```

`pygame.MOUSEBUTTONDOWN`으로 마우스 클릭 이벤트가 발생했는지 확인하고, `restart_button.collidepoint(event.pos)`를 이용해 클릭한 위치가 RESTART 버튼 영역 내부인지 검사했다.

버튼을 클릭하면 새로운 게임을 시작해야 하므로 게임 오버 상태만 해제하는 것이 아니라 점수, 속도, 방향 등 게임에 필요한 값도 다시 초기화했다.

```python
score = 0
framerate = 15
direction = 'RIGHT'
change_to = direction
food_spawn = True
game_close = False
```

#### RESTART 기능에서 발견한 또 다른 개선점

RESTART 기능을 테스트하면서 한 가지 개선점을 추가로 발견했다.

기존 코드에서는 게임을 다시 시작할 때마다 뱀이 항상 같은 위치에서 등장했다. 여러 번 플레이해보니 매번 동일한 위치에서 시작하는 것이 조금 단조롭게 느껴졌다.

그래서 RESTART 버튼을 누를 때 **뱀의 시작 위치도 랜덤하게 설정**하도록 변경했다.

```python
start_x = random.randrange(2, (WIDTH//10) - 2) * 10
start_y = random.randrange(2, (HEIGHT//10) - 2) * 10

snake_pos = [start_x, start_y]

snake_body = [
    [start_x, start_y],
    [start_x - 10, start_y],
    [start_x - 20, start_y]
]
```

게임을 여러 번 재시작해본 결과, RESTART 버튼을 클릭하면 점수와 속도가 초기화되고 뱀 역시 매번 다른 위치에서 시작하는 것을 확인할 수 있었다.

하나의 기능을 구현하고 테스트하는 과정에서 또 다른 개선점을 발견할 수 있다는 점도 흥미로웠다.

---

### 4. 게임 오버 화면에서 X 버튼이 작동하지 않는 오류 수정

#### 오류 발견

게임을 직접 플레이하면서 예상하지 못했던 오류 하나를 발견했다.

게임이 진행 중일 때는 윈도우 창의 X 버튼을 눌러 정상적으로 게임을 종료할 수 있었다. 하지만 **게임 오버 화면에서는 X 버튼을 아무리 눌러도 창이 종료되지 않았다.**

프로그램을 종료하기 위한 기본적인 UI가 특정 상태에서 작동하지 않는 것은 사용성 측면에서도 문제가 있다고 생각해 직접 원인을 찾아보기로 했다.

#### 처음 예상한 원인

처음에는 게임 오버 상태에서 윈도우 창을 직접 닫는 코드가 없기 때문이라고 예상했다.

하지만 기존 코드를 다시 살펴보니 게임 루프가 완전히 끝난 뒤에는 이미 다음 코드가 존재했다.

```python
pygame.quit()
quit()
```

따라서 문제는 `pygame.quit()`이 없어서 발생한 것이 아니었다.

코드를 다시 따라가면서 확인해보니 실제 원인은 **게임 상태에 따라 서로 다른 이벤트 루프가 실행되고 있다는 점**에 있었다.

#### 원인 분석

일반적인 게임 플레이 상태의 이벤트 처리에는 이미 다음 코드가 존재했다.

```python
if event.type == pygame.QUIT:
    game_over = True
```

하지만 `game_close == True`, 즉 게임 오버 화면에서 실행되는 별도의 이벤트 처리 부분에는 `KEYDOWN`만 존재하고 `pygame.QUIT`에 대한 처리가 없었다.

pygame에서 사용자가 창의 X 버튼을 클릭한다고 해서 프로그램이 자동으로 종료되는 것은 아니다.

X 버튼을 클릭하면 `pygame.QUIT` 이벤트가 발생하고, 프로그램이 해당 이벤트를 받아 게임 루프를 종료하도록 직접 처리해야 한다.

#### 구현

게임 오버 화면의 이벤트 처리 부분에도 `pygame.QUIT` 처리를 추가했다.

```python
for event in pygame.event.get():
    # 기능 4: 게임 오버 상태에서도 X 버튼으로 종료
    if event.type == pygame.QUIT:
        game_over = True
        game_close = False

    if event.type == pygame.KEYDOWN:
        if event.key == pygame.K_q:
            game_over = True
            game_close = False
```

`game_over = True`로 설정해 전체 게임 루프가 종료되도록 하고, 동시에 `game_close = False`로 변경해 게임 오버 화면을 반복하고 있던 루프에서도 빠져나오도록 했다.

수정 후 다시 테스트해보니 게임 플레이 중뿐만 아니라 게임 오버 화면에서도 X 버튼을 누르면 정상적으로 프로그램이 종료되었다.

처음에는 단순히 '창이 안 닫힌다'는 문제였지만, 원인을 찾아가는 과정에서 **pygame의 이벤트가 게임 상태에 따라 어디에서 처리되고 있는지를 확인하는 것이 중요하다는 점**을 알게 되었다.

---

## 📍 Result

최종적으로 다음 네 가지 기능을 구현했다.

* 플레이 중 실시간 점수 표시
* BGM 반복 재생
* 게임 오버 화면의 RESTART 버튼
* 게임 오버 상태의 X 버튼 종료 오류 수정

추가로 RESTART 기능을 테스트하는 과정에서 뱀의 시작 위치를 랜덤하게 설정하는 기능도 구현했다.

최종 실행 결과는 아래 영상에서 확인할 수 있다.

[▶ Snake Game 최종 실행 영상](https://youtu.be/PPr3Nx5kYBQ)

---

## 📍 Review

이번 과제를 진행하면서 가장 크게 느낀 점은 **프로그래밍에서는 코드를 처음부터 작성하는 것만큼 기존 코드를 읽고, 원하는 동작을 하는 부분을 찾아 수정하는 과정도 중요하다는 것**이다.

처음에는 pygame에 대한 지식이 거의 없었다. `Your_score()`가 점수 값을 반환하는 함수라고 생각하기도 했고, RESTART 버튼 역시 게임 창을 만드는 방식과 비슷하게 만들 수 있을 것이라고 예상했다.

하지만 기능을 하나씩 구현하면서 `render()`, `blit()`, `Rect()`, `MOUSEBUTTONDOWN`, `pygame.QUIT` 등 pygame에서 화면과 이벤트를 처리하는 기본적인 방식을 자연스럽게 익힐 수 있었다.

또한 기능을 구현한 뒤 직접 여러 번 플레이해보는 과정도 중요했다.

RESTART 기능 역시 처음에는 버튼을 클릭했을 때 게임을 다시 시작하게 만드는 것만 생각했다. 하지만 실제로 테스트하면서 점수, 속도, 방향, 뱀과 음식의 위치 등 **새로운 게임을 시작하기 위해 어떤 상태들을 초기화해야 하는지** 생각하게 되었다.

그 과정에서 뱀이 항상 같은 위치에서 시작한다는 또 다른 개선점도 발견해 랜덤 시작 위치 기능까지 추가할 수 있었다.

---

## 📍 Reference

* https://pygame.readthedocs.io/en/latest/1_intro/intro.html?utm_source=chatgpt.com
* https://www.pygame.org/docs/ref/music.html?utm_source=chatgpt.com
* Pygame `pygame.mixer.music` 공식 문서
* BGM: Pixabay Music
