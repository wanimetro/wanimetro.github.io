---
title: "마우스로 캐릭터를 집어 옮기기 — 드래그 인터랙션 구현"
date: 2026-07-30 11:00:00 +0900
categories: [Interactive Media]
tags: [unity, mouse-interaction, devlog]
---

# 📍 Goal

오늘부터 **Projet Émerveiller**의 첫 번째 프로젝트인 **Luminous Trace**를 시작했다.

Luminous Trace는 마우스의 움직임과 클릭을 빛과 다양한 시각 효과로 표현하는 인터랙티브 미디어 작품이다.

하지만 바로 작품을 구현하기에는 마우스 입력 처리, 드래그, Trail, Particle 등 여러 기능을 먼저 구현하고 이해할 필요가 있었다.

그래서 작품을 구현하기에 앞서 필요한 기능들을 하나씩 직접 만들어보는 **Mouse Interaction Lab**을 먼저 진행하기로 했다.

이번 실습에서는 다음 기능을 구현했다.

- 입력 감지
- 클릭에 따른 캐릭터 변화
- 드래그
- 화면 경계 제한

---

# Season 1 Project : Luminous Trace

## 프로젝트 소개

> **빛을 통해 움직임을 그리다.**

Luminous Trace는 마우스 입력을 다양한 빛과 시각 효과로 표현하는 인터랙티브 미디어 작품이다.

사용자의 움직임에 따라 빛의 흔적과 입자, 파동 효과가 실시간으로 변화하며 하나의 그림을 만들어가는 경험을 목표로 한다.

최종적으로 Mouse Interaction Lab에서 구현한 기능들을 하나의 작품으로 통합할 예정이다.

---

## 핵심 기능

### Character Interaction

캐릭터를 클릭하면 크기와 색상이 변경되며 입력에 반응한다.

마우스를 누른 채 움직이면 캐릭터를 직접 드래그할 수 있고, 손을 떼면 원래 상태로 돌아온다.

이후에는 클릭에 따라 캐릭터 주변의 빛이 강해지거나 작은 입자가 퍼지는 효과를 추가할 예정이다.

### Mouse Follow

빛과 캐릭터가 마우스를 자연스럽게 따라다닌다.

### Mouse Speed

마우스를 빠르게 움직일수록 다음과 같은 변화가 나타난다.

- 빛의 Trail이 길어진다.
- 빛의 크기가 커진다.
- 움직임이 더욱 역동적으로 표현된다.

### Trail

움직임이 지나간 자리에 빛의 흔적이 일정 시간 남았다가 서서히 사라진다.

### Particle

빛이 이동하는 경로를 따라 작은 입자가 생성된다.

빛이 지나간 자리에 작은 반짝임이나 불꽃 가루가 남는 것처럼 표현할 예정이다.

### Click Ripple

마우스를 클릭한 위치에서 원형 파동이 생성된다.

초기 버전에서는 단순한 빛의 파동을 구현하고, 이후 다음과 같은 효과로 확장할 예정이다.

- 작은 불꽃
- 별이 흩어지는 효과
- 꽃잎이나 나비가 퍼지는 효과

---

## Asset Selection

프로젝트에서 사용할 이미지 asset은 저작권 문제를 고려하여 무료로 사용할 수 있는 이미지 위주로 찾아보았다.

Google에서 다음과 같이 검색하면 OpenGameArt에 등록된 asset을 비교적 쉽게 찾을 수 있었다.

```text
character sprite site:opengameart.org
```

```text
background site:opengameart.org
```

여러 asset을 탐색하던 중 우주 배경과 어린왕자를 연상시키는 캐릭터가 눈에 들어왔다.

이를 바탕으로 작은 캐릭터가 우주 공간을 돌아다니며 빛의 흔적을 남기는 콘셉트를 구상했다.

처음에는 추상적인 빛만 표현하려고 했지만, 캐릭터가 함께 움직이면 사용자의 입력과 화면의 반응을 더욱 직관적으로 전달할 수 있을 것 같아 캐릭터를 추가하기로 했다.

---

## 왜 Mouse Interaction Lab을 먼저 진행하는가?

Luminous Trace를 구현하기 위해서는 Trail Renderer나 Particle System만 사용하는 것으로는 충분하지 않았다.

빛 효과보다 먼저 마우스 입력을 정확하게 감지하고, 클릭과 드래그를 자연스럽게 처리하며, 오브젝트를 안정적으로 제어하는 기능이 필요했다.

따라서 작품을 바로 제작하기보다, 필요한 기술을 단계적으로 구현하고 이해하기 위한 **Mouse Interaction Lab**을 먼저 진행하기로 했다.

---

## 초기 학습 계획

처음에는 필요한 기능을 다음과 같이 8개의 Lab으로 나누어 순서대로 학습할 계획이었다.

```text
Mouse Position → Mouse Follow → Mouse Speed → Mouse Click
→ Mouse Drag → Trail Renderer → Particle System → Ripple Effect
```

---

## 학습 방식 변경

하지만 기능을 지나치게 세분화하다 보니 하루마다 구현하는 내용이 너무 작았고, 서로 연결된 기능을 각각 따로 학습해야 하는 구조가 되었다.

따라서 기존의 Lab 순서를 그대로 따르기보다, 서로 관련된 기능을 하나의 실습으로 묶어 구현하는 방식으로 변경했다.

앞으로는 다음과 같은 흐름으로 프로젝트를 진행할 예정이다.

```text
구현하고 싶은 효과 정하기
        ↓
필요한 기능 나누기
        ↓
직접 구현하기
        ↓
문제 발생
        ↓
관련 문서 · 강의 · AI를 활용해 해결하기
        ↓
구현 원리와 결과 기록하기
```

이번 Day 02에서는 입력 상태를 확인하는 것부터 시작해 클릭, 드래그, 화면 경계 제한까지 구현하며 **Luminous Trace의 기반이 되는 기능**을 완성했다.

---

# 📍 Implementation

## 1. 입력 상태 확인

프로젝트의 첫 단계로 Unity의 `Input` 클래스를 사용하여 키보드와 마우스 입력이 어떤 시점에 감지되는지 확인했다.

입력은 크게 다음 세 가지 상태로 구분할 수 있다.

```text
Down : 버튼을 누른 첫 순간
Hold : 버튼을 누르고 있는 동안
Up   : 버튼에서 손을 뗀 순간
```

처음에는 Space Bar를 사용하여 각 입력 상태가 Console에 정상적으로 출력되는지 확인했다.

```csharp
if (Input.GetButtonDown("Jump"))
{
    Debug.Log("점프!");
}

if (Input.GetButton("Jump"))
{
    Debug.Log("점프 모으는 중...");
}

if (Input.GetButtonUp("Jump"))
{
    Debug.Log("슈퍼 점프!");
}
```

Unity의 기본 설정에서 `Jump` 버튼은 Space Bar와 연결되어 있다.

처음에는 Console에 굳이 문자열을 출력하는 이유가 궁금했다.
찾아보니 `Debug.Log()`는 입력이 예상한 시점에 정상적으로 감지되는지 확인하기 위한 디버깅 도구였다.
즉, Console에 출력되는 문자열 자체가 기능을 실행하는 것이 아니라, 입력이 원하는 시점에 올바르게 감지되는지 확인하기 위한 용도로 사용된다.

---

## 2. 클릭에 따른 시각적 변화

마우스를 클릭하는 동안 캐릭터의 크기와 색상이 변경되도록 구현했다.

```csharp
if (Input.GetMouseButtonDown(0))
{
    transform.localScale = clickedScale;
    spriteRenderer.color = Color.cyan;
}
```

마우스에서 손을 떼면 게임 시작 시 저장해 둔 원래 크기와 색상으로 돌아오도록 했다.

```csharp
if (Input.GetMouseButtonUp(0))
{
    transform.localScale = normalScale;
    spriteRenderer.color = normalColor;
}
```

캐릭터의 원래 상태는 `Start()`에서 변수에 저장했다.

```csharp
normalScale = transform.localScale;
normalColor = spriteRenderer.color;
```

처음에는 색상을 `Color.white`로 되돌리려고 했지만, 초기 색상이 흰색이 아닐 경우 원래 상태로 돌아가지 않는 문제가 있었다.

따라서 게임 시작 시 실제 색상을 저장하고, 해당 값을 다시 적용하도록 수정했다.

---

## 3. 클릭한 캐릭터만 반응하도록 제한하기

처음에는 화면의 어느 위치를 클릭해도 캐릭터가 반응했다.

이를 개선하기 위해 캐릭터 오브젝트에 `Collider2D`를 추가하고, 마우스 위치가 Collider 내부에 있을 때만 상호작용이 시작되도록 구현했다.

```csharp
if (objectCollider.OverlapPoint(mouseWorldPosition))
{
    isDragging = true;
}
```

`OverlapPoint()`는 특정 월드 좌표가 Collider 내부에 포함되어 있는지 확인한다.

```text
빈 공간 클릭
→ 아무 일도 발생하지 않음

캐릭터 클릭
→ 상호작용 시작
```

Collider를 충돌 판정뿐만 아니라 클릭 영역으로도 활용할 수 있다는 점을 알게 되었다.

---

## 4. 화면 좌표를 월드 좌표로 변환하기

`Input.mousePosition`으로 얻은 마우스 위치는 화면을 기준으로 한 좌표이다.

하지만 캐릭터의 `transform.position`은 Unity 월드 좌표를 사용하기 때문에, 마우스 위치를 월드 좌표로 변환해야 했다.

```csharp
private Vector3 GetMouseWorldPosition()
{
    Vector3 mousePosition = Input.mousePosition;

    mousePosition.z =
        transform.position.z - Camera.main.transform.position.z;

    return Camera.main.ScreenToWorldPoint(mousePosition);
}
```

`Camera.main.ScreenToWorldPoint()`를 사용하면 화면 좌표를 Unity Scene에서 사용하는 월드 좌표로 변환할 수 있다.

---

## 5. 캐릭터 드래그하기

현재 드래그 중인지를 저장하기 위해 `bool` 변수를 사용했다.

```csharp
private bool isDragging = false;
```

캐릭터를 클릭하면 `isDragging`을 `true`로 변경한다.

마우스 버튼을 누르고 있으며 드래그 중일 때만 캐릭터의 위치가 계속 변경된다.

```csharp
if (Input.GetMouseButton(0) && isDragging)
{
    Vector3 mouseWorldPosition = GetMouseWorldPosition();
    transform.position = mouseWorldPosition + dragOffset;
}
```

마우스를 누르고 있다는 조건만 사용하면 빈 공간을 클릭해도 캐릭터가 움직일 수 있다.

따라서 마우스 입력 상태와 드래그 상태를 함께 확인했다.

---

## 6. 클릭한 위치를 유지하며 이동하기

처음에는 캐릭터의 가장자리나 머리 부분을 클릭해도 캐릭터 중심이 마우스 위치로 순간적으로 이동했다.

이를 해결하기 위해 클릭한 위치와 캐릭터 중심 사이의 거리를 `dragOffset`에 저장했다.

```csharp
dragOffset = transform.position - mouseWorldPosition;
```

이후 캐릭터를 이동할 때 저장한 값을 더해 주었다.

```csharp
Vector3 targetPosition =
    mouseWorldPosition + dragOffset;
```

이를 통해 사용자가 처음 클릭한 지점을 유지하면서 자연스럽게 드래그할 수 있었다.

---

## 7. 캐릭터의 이동 범위 제한하기

드래그 기능을 구현한 뒤 캐릭터가 화면 밖으로 이동할 수 있다는 문제가 발생했다.

먼저 카메라 화면의 왼쪽 아래와 오른쪽 위 위치를 월드 좌표로 변환했다.

```csharp
Vector3 minScreenBounds =
    Camera.main.ViewportToWorldPoint(new Vector3(0f, 0f, 0f));

Vector3 maxScreenBounds =
    Camera.main.ViewportToWorldPoint(new Vector3(1f, 1f, 0f));
```

Viewport 좌표는 다음과 같이 구성된다.

```text
(0, 0) : 화면 왼쪽 아래
(1, 1) : 화면 오른쪽 위
```

이후 `Mathf.Clamp()`를 사용하여 캐릭터의 이동 범위를 화면 안으로 제한했다.

```csharp
targetPosition.x = Mathf.Clamp(
    targetPosition.x,
    minScreenBounds.x + halfWidth,
    maxScreenBounds.x - halfWidth
);

targetPosition.y = Mathf.Clamp(
    targetPosition.y,
    minScreenBounds.y + halfHeight,
    maxScreenBounds.y - halfHeight
);
```

캐릭터의 `transform.position`은 캐릭터의 중심점을 기준으로 한다.

따라서 중심점만 화면 안에 제한하면 캐릭터 몸의 일부가 화면 밖으로 나갈 수 있다.

```csharp
float halfWidth = objectCollider.bounds.extents.x;
float halfHeight = objectCollider.bounds.extents.y;
```

`objectCollider.bounds.extents`는 Collider의 절반 크기를 의미한다.

이 값을 화면의 최소 경계에는 더하고, 최대 경계에서는 빼서 캐릭터 전체가 화면 안에 남도록 제한했다.

---

## 전체 코드

이번 글에서는 구현 원리를 이해하는 데 필요한 핵심 코드만 정리했다.

최신 전체 코드는 아래 GitHub 저장소에서 확인할 수 있다.

> 이후 프로젝트가 진행되면서 코드는 지속적으로 업데이트될 예정이다.

[GitHub 저장소에서 전체 코드 확인하기](https://github.com/wanimetro/season1-mouse-interaction-lab)

---

# 📍 What I Learned

## Input의 세 가지 상태

```csharp
Input.GetMouseButtonDown(0);
Input.GetMouseButton(0);
Input.GetMouseButtonUp(0);
```

`GetMouseButtonDown()`은 버튼을 누른 첫 프레임에 한 번만 실행된다.

`GetMouseButton()`은 버튼을 누르고 있는 동안 매 프레임 실행된다.

`GetMouseButtonUp()`은 버튼에서 손을 뗀 첫 프레임에 한 번만 실행된다.

입력의 시작, 유지, 종료 상태를 구분하면 클릭 반응뿐만 아니라 드래그, 차징, 공격 등의 기능에도 활용할 수 있다.

---

## bool을 이용한 상태 관리

```csharp
private bool isDragging = false;
```

`bool` 변수는 `true`와 `false` 두 가지 상태를 저장한다.

이번 실습에서는 현재 캐릭터를 드래그하고 있는지를 구분하기 위해 사용했다.

```text
false : 드래그 중이 아님
true  : 드래그 중
```

마우스 버튼의 상태와 `isDragging`을 함께 확인하여, 캐릭터를 직접 클릭했을 때만 드래그가 이어지도록 만들었다.

---

## 화면 좌표와 월드 좌표

`Input.mousePosition`은 화면을 기준으로 한 좌표를 반환한다.

반면 캐릭터의 위치를 나타내는 `transform.position`은 Unity 월드 좌표를 사용한다.

따라서 마우스 위치를 캐릭터 이동에 사용하려면 `ScreenToWorldPoint()`를 통해 좌표를 변환해야 한다.

---

## Collider의 활용

처음에는 Collider를 오브젝트 사이의 충돌을 처리할 때만 사용하는 것으로 생각했다.

하지만 `OverlapPoint()`를 이용하면 마우스 위치가 캐릭터의 Collider 안에 있는지도 판단할 수 있었다.

이를 통해 Collider를 클릭 가능한 영역으로도 활용할 수 있다는 점을 배웠다.

---


# 📍 Problems & Solutions

## 캐릭터가 화면 밖으로 나갔다

드래그 기능을 구현한 뒤 마우스를 화면 끝으로 움직이면 캐릭터가 화면 밖으로 사라졌다.

### 해결

카메라 화면의 경계를 월드 좌표로 구한 뒤, `Mathf.Clamp()`를 사용하여 캐릭터의 이동 범위를 제한했다.

또한 캐릭터의 중심뿐만 아니라 몸 전체가 화면 안에 남도록 Collider 크기의 절반을 경계 계산에 포함했다.

---

# 📍 Final Result

최종적으로 다음과 같은 캐릭터 상호작용을 구현했다.

```text
빈 공간 클릭
→ 아무 일도 발생하지 않음

어린왕자 클릭
→ 크기 증가
→ 색상 변경

마우스를 누른 채 이동
→ 어린왕자가 마우스를 따라 이동

화면 끝까지 이동
→ Collider 크기를 기준으로 화면 경계에서 멈춤

마우스에서 손 떼기
→ 원래 크기와 색상으로 복구
```

이번 실습에서 구현한 드래그 기능은 이후 빛의 흔적과 파티클 효과를 구현하기 위한 기반이 된다.

```text
캐릭터 드래그
      ↓
위치 변화 감지
      ↓
이동 경로에 Trail 생성
      ↓
속도에 따라 빛의 길이와 크기 변화
```

---

# 📍 Demo

![Day02 Demo](/assets/img/posts/season1/day02.gif)

---

# 📍 Next Step

다음 단계에서는 어린왕자의 이동 경로에 빛의 흔적을 추가할 예정이다.

- `Trail Renderer` 컴포넌트 추가
- Trail의 색상과 두께 설정
- 캐릭터가 이동할 때 빛의 흔적 생성
- 이동을 멈추면 흔적이 서서히 사라지도록 설정
- 어두운 우주 배경에 어울리는 빛의 색상 조정

이후에는 마우스의 이전 위치와 현재 위치를 비교하여 이동 속도를 계산하고, 속도에 따라 Trail의 길이와 크기가 달라지도록 발전시킬 예정이다.

오늘 구현한 기능만 보면 작은 캐릭터를 드래그하는 동작이지만, 캐릭터의 움직임을 안정적으로 제어할 수 있게 되면서 **Luminous Trace**의 빛 효과를 연결할 기반이 완성되었다.

다음 실습에서는 어린왕자가 지나간 경로에 빛의 흔적을 남기고, 움직임의 속도에 따라 다양한 시각 효과를 표현해볼 예정이다. 🌌
