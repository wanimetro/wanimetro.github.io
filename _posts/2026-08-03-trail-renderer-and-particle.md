---
title: "Trail Renderer와 Particle로 빛의 흔적 만들기"
date: 2026-08-03 11:00:00 +0900
categories: [Interactive Media]
tags: [unity, mouse-interaction, devlog]
---


## 📍 Goal

지난 실습에서는 어린왕자를 클릭하면 크기와 색상이 변하고, 드래그하면 마우스를 따라 움직이는 기능을 구현했다.

이번에는 여기서 한 단계 더 나아가, **움직임 자체를 시각적으로 표현하는 방법**을 공부해 보기로 했다.

오늘 할 건 이후 제작할 **Luminous Trace** 프로젝트의 기반이 되는 작업이다.

그래서 이번 실습에서는 캐릭터가 지나간 자리에 빛의 흔적을 남기고, 움직임의 속도에 따라 다양한 시각 효과가 달라지도록 구현해 보았다.

이번 실습에서 구현한 기능은 다음과 같다.

- Trail Renderer를 이용한 이동 경로 시각화
- Trail의 색상과 두께 조정
- 이동 속도에 따른 Trail 길이 및 두께 변화
- Particle System을 이용한 별가루 효과 구현
- 이동 속도에 따른 Particle 개수와 크기 변화
- 단순한 드래그를 인터랙티브한 시각 효과로 확장하기

---

## 📍 Implementation

### 1. 클릭 효과 개선

기존에는 어린왕자를 클릭하면 캐릭터의 색상을 `Color.green`으로 변경하도록 구현했다.

하지만 실제로 적용해 보니 우주 배경과 어울리지 않았고, 좀비처럼 보여(...) 원하는 분위기와는 거리가 있었다.

그래서 캐릭터의 색상을 완전히 변경하는 대신, 원래 색상을 유지하면서 살짝 밝아지는 효과로 수정했다.

```csharp
spriteRenderer.color = new Color(0.75f, 0.95f, 1f, 1f);
```

또한 클릭 시 확대 비율도

```text
1.3배 → 1.12배
```

로 조정하여 보다 자연스러운 인터랙션을 만들었다.

---

### 2. Trail Renderer 추가

기존에는 캐릭터를 드래그해서 이동시키면, 위치만 변경될 뿐, 움직임을 시각적으로 표현하는 요소는 없었다.

이번에는 **Trail Renderer**를 추가하여 캐릭터가 지나간 경로에 빛의 흔적이 남도록 구현했다.

처음에는 단순한 흰색 선만 출력되었지만,

- Trail Time
- Width Curve
- Color Gradient
- Material

등 다양한 속성을 직접 조정하며 원하는 분위기가 나올 때까지 여러 값을 직접 수정해 보며 테스트했다.

이 과정에서 단순히 컴포넌트를 추가하는 것보다 **파라미터를 조정하는 과정이 훨씬 중요하다**는 것을 느낄 수 있었다.

---

### 3. 이동 속도에 따라 Trail 변화

항상 같은 길이와 두께의 Trail은 움직임의 차이를 표현하지 못했다.

이를 해결하기 위해 이전 프레임과 현재 프레임의 위치를 비교하여 이동 속도를 계산했다.

```text
현재 위치
    ↓
이전 위치
    ↓
이동 거리 계산
    ↓
현재 이동 속도 계산
```

계산된 속도를 기반으로

- Trail 유지 시간(Time)
- Trail 시작 두께(Start Width)

를 실시간으로 변경하도록 구현했다.

그 결과,

- 천천히 이동하면 얇고 짧은 빛
- 빠르게 이동하면 굵고 긴 빛

처럼 움직임의 속도를 시각적으로 표현할 수 있었다.

---

### 4. Particle System 추가

Trail만으로는 화면이 다소 단조롭게 느껴졌다.

그래서 **Particle System**을 추가하여 캐릭터가 지나간 자리에 작은 별가루가 흩날리는 효과를 구현했다.

단순히 Particle을 생성하는 것이 아니라, 이동 속도에 따라

- 생성량(Emission)
- 크기(Start Size)
- 시작 속도(Start Speed)

가 함께 변화하도록 구현했다.

덕분에

- 천천히 움직일 때는 은은한 별빛
- 빠르게 움직일 때는 풍성한 별빛

처럼 같은 동작이라도 전혀 다른 분위기를 연출할 수 있었다.

아직은 별가루 크기가 작아서 눈에 잘 띄지는 않지만, 이후 Bloom이나 Ripple 효과와 함께 사용할 예정이라 미리 구현해 두었다.

### 지금까지 구현된 흐름

```text
클릭
    ↓
캐릭터 확대 + 색상 변화
    ↓
드래그
    ↓
Trail 생성
    ↓
속도에 따라 Trail 변화
    ↓
Particle 생성
    ↓
속도에 따라 Particle 변화
```

처음에는 단순히 드래그만 가능했던 캐릭터가 이제는 움직임 자체를 시각적으로 표현하기 시작했다.

아직은 완성 단계는 아니지만, 다음 실습에서 Ripple과 Bloom 효과를 추가하면 조금씩 작품다운 분위기가 만들어질 것 같다.

---


## 📍 What I Learned

이번 실습에서는 단순히 캐릭터를 움직이는 것에서 끝나는 것이 아니라, 움직임에 시각 효과를 더하는 방법을 처음 제대로 구현해 봤다.

Trail Renderer와 Particle System을 직접 적용해 보면서, 단순히 컴포넌트를 추가하는 것보다 각종 속성을 얼마나 세밀하게 조정하느냐가 결과에 훨씬 큰 영향을 준다는 것을 알게 됐다.

특히 이전 프레임과 현재 프레임의 위치를 비교해서 이동 속도를 계산하고, 그 값을 Trail과 Particle에 연결하는 과정이 가장 흥미로웠다.

이번 실습을 하면서 "캐릭터를 움직이게 하는 것"과 "움직임을 연출하는 것"은 완전히 다른 영역이라는 걸 느꼈다.

확실히 Trail과 Particle이 추가되니 단순히 드래그만 하던 화면보다 훨씬 인터랙티브한 느낌이 나기 시작했다.

이번에 구현한 Trail과 Particle은 이후 Ripple, Bloom, Color Shift 같은 효과와도 자연스럽게 연결될 예정이다. 하나씩 기능을 추가하면서 최종적으로는 사용자의 움직임이 하나의 작품이 되는 인터랙티브 미디어를 만들어 보고 싶다.

지금은 작은 변화처럼 보이지만, 이런 효과들이 하나씩 쌓이면서 내가 만들고 싶은 인터랙티브 미디어 작품에 조금씩 가까워지고 있다는 점이 가장 재미있었다.

---

## 📍 Problems & Solutions

### 1. Trail Material 적용 후 Trail이 사라지는 문제

Trail Renderer에 직접 만든 `TrailGlowMaterial`을 적용했는데, 기존에 잘 보이던 Trail이 갑자기 사라졌다.

처음에는 속도에 따라 Trail의 길이와 두께를 변경하는 코드가 잘못된 줄 알았다.  
그래서 `Time`, `Start Width`, `Order in Layer`, `Emitting` 값이 정상적으로 설정되어 있는지 하나씩 확인했다.

하지만 설정값을 크게 바꿔도 Trail이 보이지 않았고, Material을 제거했을 때는 기본 흰색 Trail이 다시 정상적으로 나타났다.

이를 통해 코드나 Trail Renderer 자체의 문제가 아니라, 새로 만든 Material 설정에 문제가 있다는 것을 확인할 수 있었다.

원인을 찾기 위해 Material의 Shader를 확인해 보니, 처음에는 `Particles/Standard Surface` Shader가 적용되어 있었다.  
이 Shader는 조명 계산이 포함된 형태라 현재 프로젝트의 2D Trail과 제대로 맞지 않았다.

그래서 Shader를 `Particles/Standard Unlit`으로 변경하고, Rendering Mode를 `Additive`로 설정했다.

이후 Trail이 다시 정상적으로 출력되었고, 밝은 영역이 배경 위에 겹쳐 보이면서 기존보다 빛에 가까운 효과를 만들 수 있었다.

문제를 해결하는 과정은 다음과 같았다.

```text
Trail이 보이지 않음
        ↓
Trail Renderer 설정 확인
        ↓
Time, Width, Emitting, Sorting Order 테스트
        ↓
Material 제거 후 기본 Trail 확인
        ↓
기본 Trail은 정상 출력
        ↓
Material 또는 Shader 문제로 범위 축소
        ↓
Shader를 Unlit으로 변경
        ↓
Trail 정상 출력
```

이 과정을 통해 문제가 발생했을 때 설정을 하나씩 제거하거나 기본 상태로 되돌려 비교하면 문제의 범위를 좁힐 수 있다는 것을 배웠다.


### 2. Trail Material을 잘못 적용한 문제

Material을 적용하는 과정에서 TrailGlowMaterial을 Trail Renderer가 아니라 Sprite Renderer의 Material 항목에 넣는 실수를 했다.

그 결과 어린왕자 이미지가 보이지 않거나 비정상적으로 렌더링되었고, 처음에는 캐릭터 오브젝트 자체가 사라진 줄 알았다.

Hierarchy에는 Hero 오브젝트가 그대로 있었고 Collider의 테두리도 확인할 수 있었기 때문에, 오브젝트가 삭제된 것이 아니라 Sprite Renderer 쪽에 문제가 생겼다고 판단했다.

Inspector를 확인해 보니 Sprite Renderer의 Material에 TrailGlowMaterial이 들어가 있었다.

Trail용 Material은 파티클과 빛줄기를 표현하기 위한 Shader를 사용하고 있었기 때문에, 캐릭터 스프라이트에 적용하면 정상적으로 표시되지 않았다.

그래서 Sprite Renderer의 Material은 다시 기본값으로 돌리고, TrailGlowMaterial은 Trail Renderer의 Materials > Element 0에만 적용했다.

잘못된 구조

```text
Hero
├─ Sprite Renderer
│  └─ Material: TrailGlowMaterial
└─ Trail Renderer
   └─ Material: None
```

수정한 구조

```text
Hero
├─ Sprite Renderer
│  └─ Material: Default
└─ Trail Renderer
   └─ Materials
      └─ Element 0: TrailGlowMaterial
```

이 문제를 해결하면서 Unity에서는 같은 오브젝트 안에서도 각 Renderer가 서로 다른 역할과 Material을 가진다는 점을 알게 되었다.

또 문제가 생겼을 때 오브젝트가 사라졌다고 바로 판단하기보다, Hierarchy에 오브젝트가 남아 있는지, Sprite Renderer가 활성화되어 있는지, Sprite와 Material이 제대로 연결되어 있는지를 순서대로 확인해야 한다는 것도 배웠다.

## 📍 Demo

### Trail + Particle 결과

![Day03 Demo](/assets/img/posts/season1/day03.gif)
> Trail과 Particle을 적용한 현재 결과. 앞으로 Ripple과 Bloom 효과를 추가해 더욱 풍부한 빛 연출을 구현할 예정이다.

---

## 📍 Next Step

다음 실습에서는 단순한 빛의 흔적을 넘어 **미디어아트 작품에 가까운 연출**을 구현할 예정이다.

구현 예정 기능은 다음과 같다.

- 클릭 시 Ripple(빛의 파동) 효과
- Bloom을 활용한 빛 번짐 효과
- 움직임에 따른 Color Shift
- Trail과 Particle의 색감 개선
- 우주 배경 및 전체 연출 보완

아직은 어린왕자와 Trail만 있는 단순한 장면이지만,

앞으로 Ripple, Bloom, Color Shift가 추가되면 조금씩 내가 상상했던 **Luminous Trace**에 가까워질 것 같다🌌