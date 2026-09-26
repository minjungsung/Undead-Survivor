# Setup Guide

## 요구 사항

| 구분 | 요구사항 |
|------|----------|
| Unity | 2021.3 LTS 이상 (Input System 패키지 필요) |
| 플랫폼 | Windows / macOS |
| 패키지 | Input System, 2D Tilemap |

## 프로젝트 열기

### 1. 리포지토리 클론

```bash
git clone https://github.com/minjungsung/Undead-Survivor.git
cd Undead-Survivor
```

### 2. Unity Hub에서 열기

1. Unity Hub → **Open** → `Undead-Survivor` 디렉토리 선택
2. Unity 에디터 버전: 2021.3 LTS 이상 선택
3. 프로젝트 열기

### 3. 필수 패키지 확인

**Window → Package Manager**에서 다음 패키지가 설치되어 있는지 확인:

- **Input System**: 새로운 입력 시스템 (`Player.inputactions` 사용)
- **2D Tilemap Editor**: 타일맵 기반 지형

### 4. 데모 씬 열기

`Demo/Demo.unity` 씬을 열어 게임을 테스트합니다.

## 프로젝트 구조 이해

### 디렉토리 구성

```
Undead-Survivor/
├── Codes/           # 모든 C# 스크립트 (19개)
├── Data/            # ScriptableObject 에셋 (Item 0~4)
├── Prefabs/         # Enemy, Bullet 프리팹
├── Sprites/         # 캐릭터, 적, UI 스프라이트
├── Animations/      # Animator Controller, Override
├── Audio/           # BGM + 10종 SFX
├── Tiles/           # 타일맵 에셋 (RanTile, Pal)
├── Fonts/           # neodgm 폰트
├── Demo/            # 데모 씬
└── Player.inputactions  # Input System 매핑
```

### 씬 계층 구조 (예상)

```
Demo Scene
├── Main Camera
│   └── AudioHighPassFilter
├── Grid
│   └── Tilemap (Reposition 적용)
├── Player
│   ├── Scanner
│   ├── Hand (Left) — Melee
│   ├── Hand (Right) — Range
│   └── Gear (자식으로 장착)
├── GameManager
│   ├── pool (PoolManager)
│   ├── uiLevelUp (LevelUp)
│   └── uiResult (Result)
├── AudioManager
├── AchieveManager
├── Spawner
│   └── SpawnPoints[]
├── Canvas (HUD)
│   ├── Exp Slider
│   ├── Level Text
│   ├── Kill Text
│   ├── Time Slider
│   ├── Health Slider
│   └── Follow (플레이어 추적 UI)
├── EnemyCleaner (Trigger)
└── UI Joy (가상 조이스틱)
```

### Inspector 설정 가이드

#### GameManager

```
[Game Control]
  isLive: false (런타임에 변경)
  maxGameTime: 20 (20초, 테스트용 / 실제: 더 길게)

[Player Info]
  maxHealth: 100
  nextExp: [3, 5, 10, 100, 150, 210, 280, 360, 450, 600]

[Game Object]
  pool: PoolManager 연결
  player: Player 연결
  uiLevelUp: LevelUp UI 연결
  uiResult: Result UI 연결
  enemyCleaner: 적 제거용 트리거
  uiJoy: 가상 조이스틱 Transform
```

#### AudioManager

```
[BGM]
  bgmClip: Audio/BGM.wav
  bgmVolume: 0.5

[SFX]
  sfxClips[]: [Dead, Hit0, Hit1, LevelUp, Lose, Melee0, Melee1, Range, Select, Win]
  sfxVolume: 1.0
  channels: 16
```

#### Spawner

```
spawnData[]:
  [0] spawnTime: 1.0, health: 5, speed: 1.0
  [1] spawnTime: 0.8, health: 10, speed: 1.2
  [2] spawnTime: 0.5, health: 20, speed: 1.5
  ...
```

## 빌드

### PC 빌드

```
File → Build Settings
  Platform: PC, Mac & Linux Standalone
  Scenes in Build: Demo/Demo.unity
  Build
```

### 모바일 빌드

```
File → Build Settings
  Platform: Android / iOS
  Switch Platform
  Player Settings:
    - Active Input Handling: Both (또는 Input System Package)
    - Resolution: Portrait / Landscape
  Build
```

> **주의**: 모바일 빌드 시 Input System이 터치 입력과 호환되도록 `Player.inputactions`에 터치 바인딩이 포함되어야 합니다.

## 커스터마이징

### 게임 시간 조정

```csharp
// GameManager.cs
public float maxGameTime = 2 * 10f;  // 기본 20초
// 실제 게임용으로 300f (5분) 등으로 변경
```

### 새 아이템 추가

1. `Assets → Create → Scriptable Object → ItemData`
2. 아이템 정보 설정 (타입, 이름, 설명, 아이콘)
3. 레벨별 damage/count 배열 설정
4. LevelUp UI의 Item 리스트에 추가

### 새 적 타입 추가

1. 새 RuntimeAnimatorController 생성
2. Enemy 프리팹의 `animCon[]` 배열에 추가
3. Spawner의 SpawnData에 새 레벨 추가
