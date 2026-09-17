너는 숙련된 **Warcraft III World Editor / JASS 맵 제작 전문가**다.

나는 Warcraft III World Editor로 **아포칼립스 좀비 군사기지 디펜스 유즈맵**을 만들려고 한다.

게임의 핵심 컨셉은 "영웅 능력으로 싸우는 게임"이 아니라 **중앙 군사기지를 실제 군대가 방어하는 느낌의 생존 디펜스**다.

플레이어가 직접 초능력이나 스킬을 사용하는 영웅 중심 게임은 만들지 않는다.

병사, 기관총, 저격병, 장갑차, 탱크, 포병, 방어벽 등을 배치하고 운용하면서 좀비 웨이브를 막는 게임으로 제작한다.

==================================================

1. 게임 기본 컨셉
   ==================================================

게임 시간:

* 한 판 약 20~30분
* 기본 목표는 30분 생존
* 30분을 버티면 승리
* 중앙기지가 파괴되면 즉시 패배

플레이어:

* 기본적으로 1인 플레이 기준으로 개발
* 추후 멀티플레이 확장 가능하도록 구조를 최대한 분리
* Player 1이 지휘관 역할

맵:

* 중앙에 군사기지
* 외곽에 좀비 생성 지역
* 여러 단계의 방어선
* 최종적으로 중앙기지를 보호

기본 구조:

```
             ZOMBIE SPAWN
          🧟 🧟 🧟 🧟 🧟
               ↓
               ↓
    =========================
          1차 방어선
    =========================
      🪖   🔫   🪖   🔫
               ↓
    =========================
          2차 방어선
    =========================
          🪖   🚜   🪖
               ↓
             [ HQ ]
          중앙 군사기지
```

플레이어는 좀비를 막기 위해 병력을 생산하고 방어선을 관리한다.

==================================================
2. 플레이 방식
=========

게임에는 다음 시스템이 존재해야 한다.

1. 좀비 자동 생성
2. 웨이브 시스템
3. 좀비 AI
4. 중앙기지 HP
5. 방어벽 HP
6. 병사 생산
7. 병사 자동 전투
8. 탱크
9. 장갑차
10. 기관총 진지
11. 보급품 시스템
12. 좀비 처치 보상
13. 30분 생존 타이머
14. 승리
15. 패배
16. 플레이 기록
17. 계급 시스템
18. 세이브/로드 시스템
19. 최장 생존 기록
20. 총 좀비 처치 수

==================================================
3. 군대 시스템
=========

처음에는 다음 병력부터 구현한다.

Rifleman

* 기본 보병
* 저렴함
* 기본 공격

MachineGunner

* 높은 공격속도
* 다수의 좀비 상대

Sniper

* 긴 사거리
* 높은 단일 공격력
* 공격속도 느림

Heavy

* 높은 체력
* 강한 공격

Medic

* 주변 아군 회복

Engineer

* 방어벽 수리

Vehicle:

APC

* 높은 체력
* 기관총
* 이동 가능

Tank

* 매우 높은 체력
* 강력한 주포
* 높은 비용

Artillery

* 장거리 공격
* 쿨다운
* 넓은 범위 공격

처음 개발 버전에서는 모든 유닛을 전부 구현하지 말고 다음 순서로 구현한다.

1. Rifleman
2. MachineGunner
3. Tank

이 세 종류로 먼저 플레이 가능한 버전을 만든다.

==================================================
4. 좀비 시스템
=========

초기에는 다음 좀비만 구현한다.

Normal Zombie

* 느림
* 낮은 체력
* 숫자가 많음

Runner

* 빠름
* 낮은 체력

Brute

* 느림
* 매우 높은 체력
* 방어벽 파괴에 강함

추후:

Infected Soldier
Spitter
Exploder
Giant
Boss

등을 추가할 수 있도록 구조를 확장 가능하게 설계한다.

==================================================
5. 웨이브 시스템
==========

20초마다 새로운 웨이브가 시작된다.

초기 웨이브:

Wave 1
5 Zombies

Wave 2
8 Zombies

Wave 3
11 Zombies

이런 식으로 점점 증가한다.

기본 공식은:

ZombieCount = 5 + ((Wave - 1) * 3)

단, 5분 단위로 난이도를 크게 증가시킨다.

0~5분:
Normal Zombie

5~10분:
Normal + Runner

10~15분:
Normal + Runner + Brute

15~20분:
대규모 웨이브

20~25분:
특수 좀비

25~30분:
Final Wave

25분부터 최종 웨이브 준비.

30분까지 계속 대규모 공격.

==================================================
6. 좀비 이동
========

좀비는 단순히 랜덤하게 돌아다니는 것이 아니라 중앙기지를 목표로 이동해야 한다.

Region:

gg_rct_ZombieSpawn_North
gg_rct_ZombieGoal
gg_rct_HQ

추후:

gg_rct_ZombieSpawn_West
gg_rct_ZombieSpawn_East
gg_rct_ZombieSpawn_South

등을 추가할 수 있도록 설계한다.

좀비가 ZombieGoal에 도착하면 중앙기지를 공격한다.

==================================================
7. 방어선 시스템
==========

최소 2개의 방어선을 만든다.

Defense Line 1
Defense Line 2

각 방어선에는 방어벽을 배치한다.

방어벽이 파괴되면:

"1차 방어선 붕괴!"

같은 경고 메시지를 출력한다.

좀비가 1차 방어선을 돌파하면 2차 방어선으로 진입한다.

최종적으로 HQ까지 도달하면 HQ HP를 감소시킨다.

==================================================
8. 중앙기지
=======

HQ 기본 HP:

5000

좀비 한 마리가 HQ에 도달하면:

HQ HP -100

HQ HP <= 0:

즉시 패배.

HQ 상태를 플레이어에게 계속 표시한다.

예:

HQ
████████░░ 80%

가능하면 Warcraft III의 Multiboard 또는 UI를 이용해서 보기 좋게 표시한다.

==================================================
9. 보급품 시스템
==========

게임 시작:

Supply = 100

좀비 처치:

+5 Supply

병력 비용:

Rifleman = 20
MachineGunner = 50
Tank = 200

보급품이 부족하면 해당 병력을 생산할 수 없다.

추후:

* 탄약
* 연료
* 인력
* 수리 자원

등을 추가할 수 있도록 확장 가능한 구조로 만든다.

==================================================
10. 처치 기록
=========

플레이어의 총 처치 수:

Kills

좀비 1마리 처치:

Kills + 1

Supply + 5

게임 종료 화면:

DEFENSE RESULT

생존 시간:
27:31

처치:
4821

웨이브:
28

==================================================
11. 게임 타이머
==========

게임 시작부터 1초 단위로 시간을 계산한다.

GameTime

1800초 = 30분

GameTime >= 1800:

Victory

게임 화면에는 현재 생존 시간을 표시한다.

예:

DAY 1
TIME 17:42
WAVE 18

==================================================
12. 계급 시스템
==========

플레이어가 게임을 성공적으로 생존할 때마다 SurvivalCount를 증가시킨다.

기본 계급:

0회 이상:
이병

3회:
일병

7회:
상병

15회:
병장

20회:
하사

30회:
중사

50회:
상사

80회:
원사

계급 계산은 별도의 함수로 만든다.

예:

GetRankName(level)

UpdateRank()

등.

추후 계급별 해금 시스템을 추가할 수 있도록 RankLevel을 별도의 변수로 관리한다.

==================================================
13. 기록 시스템
==========

저장해야 할 기본 데이터:

SurvivalCount
BestTime
TotalKills
RankLevel

추후:

HighestWave
TotalGames
TotalSupplyEarned
UnlockedUnits

등을 추가할 수 있도록 한다.

게임에서 승리하면:

SurvivalCount + 1

현재 생존 시간이 BestTime보다 높으면:

BestTime = GameTime

게임 종료 화면에:

현재 계급
생존 횟수
최고 생존 시간
이번 판 처치 수
총 처치 수

를 표시한다.

==================================================
14. 세이브 / 로드
============

Warcraft III에서 사용할 수 있는 현실적인 방식으로 세이브 시스템을 설계한다.

장기 플레이어 데이터:

RankLevel
SurvivalCount
BestTime
TotalKills
UnlockedUnits

를 저장할 수 있어야 한다.

가능하면 Save Code 방식으로 설계한다.

예:

SAVE CODE

ZD-03-27-1842-4821

로드:

-load ZD-03-27-1842-4821

단순 문자열만 사용하는 것이 아니라 가능하면:

* 인코딩
* 체크섬
* 잘못된 코드 검증
* 버전 번호

를 넣어서 잘못된 세이브 코드나 임의 조작을 어느 정도 방지한다.

단, Warcraft III 버전에 따라 실제 저장 방식이 달라질 수 있으므로 먼저 내가 사용하는 Warcraft III 버전을 확인하거나, 버전별 차이를 설명한다.

==================================================
15. JASS 구조
===========

코드를 하나의 거대한 함수로 만들지 않는다.

가능하면 다음과 같이 시스템별 함수로 분리한다.

Game System

InitGame()
StartGame()
GameSecond()
VictoryGame()
DefeatGame()

Zombie System

InitZombieSystem()
SpawnZombie()
SpawnWave()
ZombieReachedHQ()
ZombieDeath()

Military System

CreateRifleman()
CreateMachineGunner()
CreateTank()

Base System

InitBaseSystem()
DamageHQ()
DamageDefenseWall()
DefenseLineDestroyed()

Supply System

AddSupply()
SpendSupply()

Rank System

GetRankName()
UpdateRank()

Record System

UpdateRecord()
ShowResult()

Save System

SavePlayerData()
LoadPlayerData()
GenerateSaveCode()
LoadSaveCode()
ValidateSaveCode()

==================================================
16. Rawcode
===========

코드 상단에 Rawcode를 한 곳에서 관리할 수 있도록 만든다.

예:

globals

constant integer UNIT_ZOMBIE = 'nzom'
constant integer UNIT_RIFLEMAN = 'hfoo'
constant integer UNIT_TANK = 'htnk'

endglobals

실제 Rawcode는 내가 Object Editor에서 만든 유닛의 Rawcode로 교체할 수 있도록 한다.

Region도:

gg_rct_ZombieSpawn_North
gg_rct_ZombieGoal
gg_rct_HQ

등을 사용한다.

==================================================
17. 중요한 개발 원칙
=============

절대로 처음부터 모든 시스템을 한꺼번에 구현하지 않는다.

다음 순서로 개발한다.

PHASE 1
맵 지형
Region
HQ
Zombie Spawn

PHASE 2
Zombie 생성
Zombie 이동
Zombie 사망

PHASE 3
Rifleman
전투
Kills

PHASE 4
Defense Wall
Defense Line

PHASE 5
Wave System

PHASE 6
Supply System

PHASE 7
MachineGunner
Tank

PHASE 8
20~30분 난이도 증가

PHASE 9
Victory / Defeat

PHASE 10
Rank

PHASE 11
Record

PHASE 12
Save / Load

==================================================
18. 매우 중요한 요구사항
===============

내가 실제 Warcraft III World Editor에서 따라 할 수 있어야 한다.

따라서 코드를 제공할 때 단순히 JASS 코드만 던지지 말고 반드시:

1. World Editor에서 무엇을 만들어야 하는지
2. Object Editor에서 어떤 유닛을 복사해야 하는지
3. 각 유닛의 Rawcode를 어디에서 확인하는지
4. Region을 어떤 이름으로 만들어야 하는지
5. Trigger Editor에서 어디에 코드를 넣는지
6. Custom Text로 넣어야 하는지
7. Map Initialization에서 무엇을 호출해야 하는지
8. 테스트할 때 무엇을 확인해야 하는지
9. 오류가 발생했을 때 원인이 무엇인지

를 단계별로 설명한다.

==================================================
19. 코드 호환성
==========

JASS 문법은 Warcraft III World Editor에서 실제로 컴파일 가능한 문법을 사용한다.

가상의 API나 존재하지 않는 함수를 만들지 않는다.

특히:

* 실제 Warcraft III JASS API 사용
* 실제 native/function 확인
* GUI에서 자동 생성되는 변수명과 충돌하지 않도록 주의
* Handle Leak 최소화
* Timer 관리
* Group 관리
* Location 사용 최소화
* null 처리
* DestroyTimer
* DestroyGroup
* RemoveLocation

등을 적절히 고려한다.

내가 사용하는 Warcraft III 버전을 모르면 먼저 버전을 질문한다.

==================================================
20. 첫 번째 답변의 범위
===============

처음 답변에서는 최종 30분 게임 전체 코드를 한꺼번에 만들지 않는다.

먼저 PHASE 1만 완성한다.

PHASE 1:

1. 맵 크기 추천
2. 지형 배치
3. 중앙기지 위치
4. ZombieSpawn_North Region
5. ZombieGoal Region
6. HQ Region
7. 필요한 유닛
8. Zombie Rawcode
9. HQ Rawcode
10. JASS 초기화
11. 게임 시작
12. 5마리 좀비 생성
13. 좀비가 HQ를 향해 이동

까지 실제로 작동하는 코드를 만든다.

그리고 마지막에:

"PHASE 1 테스트 체크리스트"

를 만들어 내가 월드 에디터에서 테스트할 수 있도록 한다.

PHASE 1이 정상적으로 작동하면 다음 답변에서 PHASE 2로 넘어간다.

==================================================
21. 답변 스타일
==========

한국어로 설명한다.

초보자도 Warcraft III World Editor에서 그대로 따라 할 수 있게 설명한다.

메뉴 위치를 정확하게 적는다.

예:

World Editor
→ Module
→ Trigger Editor
→ New Trigger
→ Edit
→ Convert to Custom Text

같은 식으로 설명한다.

코드는 항상 전체 코드를 제공한다.

부분 코드만 주고 "여기에 추가하세요"라고 하지 않는다.

코드에 주석을 충분히 작성한다.

각 Phase가 끝날 때마다:

* 완성된 기능
* 추가된 변수
* 추가된 Region
* 추가된 Unit
* 테스트 방법
* 다음 Phase에서 추가할 기능

을 정리한다.

내가 오류 메시지나 스크린샷을 보내면 기존 구조를 유지하면서 오류 원인을 찾아 수정한다.

==================================================
최종 목표
=====

최종적으로 다음과 같은 게임을 완성한다.

"중앙기지에서 벽을 지키며 살아남기"

* 20~30분 플레이
* 군사기지
* 좀비 대규모 웨이브
* 방어선
* 소총병
* 기관총병
* 저격병
* 중화기병
* 의무병
* 공병
* 장갑차
* 탱크
* 포병
* 보급품
* 좀비 처치 보상
* 방어선 붕괴
* 중앙기지 방어
* 30분 최종 웨이브
* 승리 / 패배
* 생존 횟수
* 최고 생존 기록
* 총 처치 수
* 이병 → 일병 → 상병 → 병장 → 하사 → 중사 → 상사 → 원사
* 세이브 코드
* 로드 시스템
* 향후 멀티플레이 확장 가능 구조

이 목표를 기준으로 **PHASE 1부터 실제로 컴파일 가능한 JASS 코드와 World Editor 작업 방법을 시작해라.**
