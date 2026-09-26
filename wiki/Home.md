# Undead Survivor

> Unity 탑다운 서바이벌 게임 (뱀파이어 서바이버 스타일)

## 게임 개요

Undead Survivor는 뱀파이어 서바이버(Vampire Survivors) 스타일의 탑다운 서바이벌 게임입니다. 플레이어는 끊임없이 몰려오는 적(언데드)을 물리치며 제한 시간 동안 생존해야 합니다.

### 게임 메카닉

#### 핵심 게임플레이

1. **이동**: 조이스틱/키보드로 8방향 이동
2. **자동 공격**: 무기가 자동으로 가장 가까운 적을 공격
3. **경험치 & 레벨업**: 적 처치 → 경험치 획득 → 레벨업 시 아이템 선택
4. **시간 제한**: `maxGameTime` 내 생존하면 승리

#### 캐릭터 시스템

3종의 플레이어 캐릭터:

| ID | 캐릭터 | 특성 |
|----|--------|------|
| 0 | 캐릭터 A | 이동 속도 +10% (Speed 1.1) |
| 1 | 캐릭터 B | 무기 속도 +10%, 연사 -10% |
| 2 | 캐릭터 C | 공격력 +20% (Damage 1.2) |

캐릭터별 특성은 `Character.cs`의 static 프로퍼티로 계산됩니다.

#### 무기 시스템

| 타입 | ID | 설명 |
|------|----|------|
| Melee | 0 | 근접 무기 — 플레이어 주위 회전 |
| Range | 1 | 원거리 무기 — 가장 가까운 적에게 발사 |

#### 아이템 시스템 (ScriptableObject)

```
ItemData (ScriptableObject):
  itemType: Melee / Range / Glove / Shoe / Heal
  itemId, itemName, itemDesc, itemIcon
  baseDamage, baseCount
  damages[] — 레벨별 대미지
  counts[]  — 레벨별 투사체 수
  projectile — 투사체 프리팹
  hand — 손 스프라이트
```

| 아이템 타입 | 효과 |
|-------------|------|
| Melee | 근접 무기 (회전 공격) |
| Range | 원거리 무기 (발사체) |
| Glove | 공격 속도 증가 |
| Shoe | 이동 속도 증가 |
| Heal | 체력 회복 |

#### 레벨업 시스템

```
경험치 필요량: [3, 5, 10, 100, 150, 210, 280, 360, 450, 600]
레벨업 시:
  → 게임 일시정지
  → 3개 랜덤 아이템 표시
  → 플레이어가 1개 선택
  → 선택한 아이템 레벨업 또는 획득
  → 게임 재개
```

### 프로젝트 구조

```
Undead-Survivor/
├── Codes/                    # C# 스크립트
│   ├── GameManager.cs        # 게임 상태, 시간, 레벨 관리
│   ├── Player.cs             # 플레이어 이동, 입력
│   ├── Enemy.cs              # 적 AI, 추적, 피격
│   ├── Weapon.cs             # 무기 로직 (근접/원거리)
│   ├── Bullet.cs             # 투사체 (대미지, 관통)
│   ├── Spawner.cs            # 적 스폰 시스템
│   ├── Scanner.cs            # 최근접 적 탐색
│   ├── PoolManager.cs        # 오브젝트 풀링
│   ├── AudioManager.cs       # BGM/SFX 관리
│   ├── AchieveManager.cs     # 업적/캐릭터 해금
│   ├── Item.cs               # UI 아이템 선택
│   ├── ItemData.cs           # 아이템 데이터 (SO)
│   ├── Character.cs          # 캐릭터 특성 계산
│   ├── Gear.cs               # 장비 효과 (장갑/신발)
│   ├── LevelUp.cs            # 레벨업 UI
│   ├── HUD.cs                # HUD 표시
│   ├── Hand.cs               # 손 스프라이트 방향
│   ├── Follow.cs             # HUD 플레이어 추적
│   ├── Reposition.cs         # 타일맵 무한 스크롤
│   └── Result.cs             # 결과 화면 (승/패)
├── Data/                     # ScriptableObject 에셋
│   ├── Item 0~4.asset
├── Prefabs/                  # 프리팹
│   ├── Enemy.prefab
│   ├── Bullet 0.prefab
│   └── Bullet 1.prefab
├── Sprites/                  # 스프라이트 에셋
├── Animations/               # 애니메이션
├── Audio/                    # 사운드 파일
├── Tiles/                    # 타일맵 에셋
├── Fonts/                    # 폰트 (neodgm)
├── Demo/                     # 데모 씬
└── Player.inputactions       # Input System 매핑
```

## 관련 페이지

- [[Architecture]] — 프로젝트 구조 및 코드 조직
- [[Game-Systems]] — 전투, 스폰, 아이템, 레벨 시스템
- [[Setup-Guide]] — Unity 프로젝트 설정 가이드
