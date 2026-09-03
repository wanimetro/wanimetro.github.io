---
title: "Unity로 2D 슈팅 게임 만들며 기본기 익히기"
date: 2026-07-29 14:00:00 +0900
categories: [Interactive Media]
tags: [unity, 2d-game]
image:                                              
  path: /assets/img/posts/trash-flight/trashflight.gif
  alt: 마우스로 조작하는 2D 슈팅 게임 플레이 화면
---

## 📍 Goal

Unity의 기본 기능을 익히고, 2D 슈팅 게임을 직접 만들어보며 게임 개발의 전체적인 흐름을 이해하는 것을 목표로 했다.

---

## 📍 Implementation

이번 프로젝트에서는 다음 기능들을 구현했다.

- 마우스 기반 플레이어 이동
- 총알 발사 시스템
- 적 생성 및 이동
- 충돌 판정
- 코인 획득
- 무기 강화 시스템
- 적 난이도 증가
- 보스 스테이지
- 게임 오버 및 재시작

자세한 구현 내용은 GitHub README를 참고하면 된다.

🔗 **GitHub Repository**  
https://github.com/wanimetro/trash-flight

---

## 📍 What I Learned

5시간 분량의 Unity 강의를 한 번에 따라가기보다는 약 일주일 동안 조금씩 나누어 학습했다🥵

처음 사용하는 Unity와 C#이라 낯설었지만, 기존에 C++을 공부했던 경험이 있어 문법 자체는 비교적 빠르게 이해할 수 있었다. 오히려 Unity의 컴포넌트 구조와 게임 오브젝트를 관리하는 방식에 익숙해지는 데 더 많은 시간을 사용했다.

강의를 그대로 따라가기보다는 일부 기능은 직접 수정하며 구현했다. 예를 들어 강의에서는 무기 강화가 30코인 단위였지만, 나는 10코인마다 단계적으로 강화되도록 변경했고, 무기의 이동 속도와 게임 난이도도 직접 조정했다.

이번 프로젝트는 완성도 높은 게임을 만드는 것이 목표가 아니라, Unity의 기본적인 게임 개발 구조를 이해하고 직접 구현해 보는 과정이었다.

또한 앞으로 진행할 **Projet Émerveiller** 프로젝트의 첫 번째 단계로, Unity 개발의 기본기를 다지는 시간이었다.

---

## 📍 Problems & Solutions

### Unity 버전 차이로 인한 오류

이번 실습은 몇 년 전에 제작된 Unity 강의를 기반으로 학습했다.

강의를 따라 했지만 현재 사용 중인 Unity 버전에서는 일부 기능의 위치와 API가 변경되어 동일한 코드가 그대로 동작하지 않는 경우가 있었다.

예를 들어,

- Inspector UI 구성이 강의와 달랐다.
- 일부 컴포넌트의 위치가 변경되어 다시 찾아야 했다.
- 최신 Unity에서는 `FindObjectOfType()` 대신 `FindFirstObjectByType()` 사용이 권장되어 경고가 발생했다.

처음에는 강의와 화면이 달라 당황했지만, Unity 공식 문서와 오류 메시지를 참고하며 현재 버전에 맞게 하나씩 수정했다.

이번 경험을 통해 **강의를 그대로 따라가는 것보다 사용하는 Unity 버전에 맞게 문제를 분석하고 해결하는 과정이 더 중요하다는 것을 배울 수 있었다.**

---

## 📍 Screenshot

![Trash Flight Gameplay](/assets/img/posts/trash-flight/trashflight.gif)

---

## 📍 Demo

🎥 **YouTube**

https://youtube.com/shorts/weyCSJzw0c4?si=XYKJfhOIKCSUMD1P

---

## 📍 Next Step

본격적으로 진행할 **Projet Émerveiller Season 1**에서는 게임이 아닌 인터랙티브 콘텐츠 제작을 시작한다.

다음 목표는 다음과 같다.

- Mouse Position
- Mouse Follow
- Trail Renderer
- Particle System
- Mouse Speed에 따른 시각 효과 구현
- Mouse Click Effect

---

🍀 Unity를 계속 배워야겠다고 생각만 하다가 이번에 드디어 제대로 시작했다. 생각보다 재미있고 할 만한 것 같다ㅎㅎ
앞으로는 마우스의 움직임과 클릭에 반응하는 인터랙티브 콘텐츠를 하나씩 만들어 보며 Unity를 더 깊게 익혀볼 예정이다.