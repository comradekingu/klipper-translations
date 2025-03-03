# 베드 메쉬

베드 메쉬 모듈은 베드 표면의 불균일함을 보상하기 위해 사용됩니다. 이 기능을 사용하면 전체 베드면에서의 첫레이어 안착을 더 좋게 할 수 있습니다. 소프트웨어 기반의 수정은 완벽한 결과를 얻을 수 없습니다. 오직 대략적인 베드 형태만을 취할 수 있을 뿐입니다. 또한 베드 메쉬는 기계적이거나 전기적인 문제에 대한 보정을 할 수 없습니다. 만일 축이 틀어져 있거나 레벨링센서의 정확도가 떨어진다면 베드 메쉬 모듈은 레벨링 측정만으로는 정확한 결과를 얻지 못할 것입니다.

Prior to Mesh Calibration you will need to be sure that your Probe's Z-Offset is calibrated. If using an endstop for Z homing it will need to be calibrated as well. See [Probe Calibrate](Probe_Calibrate.md) and Z_ENDSTOP_CALIBRATE in [Manual Level](Manual_Level.md) for more information.

## 기본 설정

### 사각 베드

이 예제는 250mm x 220mm 직사각형 베드를 가진 프린터를 가정하에 진행합니다. 그리고 레벨링센서(프로브)는 X오프셋 값 24mm, Y오프셋은 5mm 라고 가정합니다.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 35,6
mesh_max: 240, 198
probe_count: 5,3
```

- `speed: 120` *Default 값: 50* 포인트 사이를 이동하는 속도.
- `horizontal_move_z: 5` *Default 값: 5* 포인트 사이를 이동하기 전에 Z 축 방향으로 올라가는 높이.
- `mesh_min: 35,6` *필수* 원점으로 부터 가장 가까운 첫번째 측정위치. 이 위치는 레벨링센서(프로브)위치에 따라 상대적이다.
- `mesh_max: 240,198` *필수* 원점에서 가장 멀리 떨어진 측정위치. 이 지점은 가장 마지막 측정이 아닐 수도 있습니다. 왜냐하면 레벨 측정은 지그재그 경로로 되기 때문입니다. `mesh_min` 과 같이 레벨링센서위치에 따라 상대적이다.
- `probe_count: 5,3` *Default 값: 3,3* x, y 각 축별로 측정되는 지점의 수. 정수값. 이 예제에서는 X 축방향으로 5개 점, Y 축방향으로 3개점을 측정합니다. 따라서 전체적으로 15개 측정위치가 됩니다. 만약 3x3 과 같이 정방형으로 측정하기 원한다면 정수 하나만으로 설정이 가능합니다. 즉, `probe_count: 3` 이렇게 입력하면 됩니다. 메쉬는 각 축별로 최소 3개의 측정포인트가 필요함을 기억하십시오.

아래 그림은 `mesh_min`, `mesh_max`, and `probe_count` 옵션이 어떻게 측정위치를 생성하시지를 나타낸 것이다. 화살표는 `mesh_min` 으로 부터 시작한 레벨측정 순서를 나타낸다. 참고로, 프로브가 `mesh_min` 위치일 때 노즐은 (11,1) 위치에 있게 되고, 프로브가 `mesh_max` 위치일 때 노즐은 (206,193)에 위치한다.

![bedmesh_rect_basic](img/bedmesh_rect_basic.svg)

### 원형 베드

이 예제는 베드의 반지름이 100mm 인 원형 베드를 가진 프린터를 가정하에 진행합니다. 그리고 레벨링센서(프로브)는 직사각형 때의 예제와 마찬가지로 X오프셋 값 24mm, Y오프셋은 5mm 라고 가정합니다.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_radius: 75
mesh_origin: 0,0
round_probe_count: 5
```

- `mesh_radius: 75` *필수* `mesh_origin`에 상대적인 값으로 측정메쉬의 반지름을 mm 단위로 표시한다. 프로브 오프셋이 메쉬 반지름의 크기를 제한합니다. 이 예제에서는 76보다 큰 반지름은 프린터의 가용반경을 넘어 동작하게 될 것입니다.
- `mesh_origin: 0,0` *Default 값: 0,0* 메쉬의 중심점. 이 좌표는 프로브 위치에 따라 상대적이다. 기본값이 0,0 이지만 베드의 더 넓은 부분을 측정할 수 있도록 원점을 잡는게 유용할 수 있다. 아래 나오는 그림을 참고하기 바란다.
- `round_probe_count: 5` *Default 값: 5* 이 값은 X, Y 축에 따라 레벨링 측정을 할 최대 포인트수를 정수값으로 나타낸다. "최대"값이라 함은 메쉬 원점을 따라 측정하는 포인트수를 뜻한다. 메쉬의 중심을 측정할 수 있도록 하기 위해 이 값은 홀수여야 한다.

아래 도안은 얼마나 많은 측정포인트가 만들어졌는지를 나타내고 있다. 보다시피 `mesh_origin` 을 (-10, 0) 로 설정하게 되면 측정하는 영역의 반지름이 85mm 이 된다.

![bedmesh_round_basic](img/bedmesh_round_basic.svg)

## 고급설정

보다 고급설정을 위한 자세한 옵션에 대한 설명을 아래에 이어가도록 한다. 각 예제는 위에 언급했던 직사각형 베드를 가진 프린터를 기준으로 하겠다. 각 고급옵션들은 원형베드에서도 동일한 방법으로 적용될 수 있다.

### 메쉬 인터폴레이션

측정된 포인트사이 지점의 Z값을 결정하는데 있어 간단한 bilinear 보간법을 이용해 측정매트릭스를 샘플링하는 것도 가능하지만, 메쉬밀도를 높이기 위해 보다 고급 보간알고리즘을 사용하는게 유용할 수 있다. 이 알고리즘은 메쉬에 굴곡을 추가한다. 이것은 베드의 물질특성치에 대한 시뮬레이션을 시도한다. 이것이 가능케 하기 위해 베드 메쉬는 라그랑지에와 바이큐빅 인터폴레이션을 제공한다.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 35,6
mesh_max: 240, 198
probe_count: 5,3
mesh_pps: 2,3
algorithm: bicubic
bicubic_tension: 0.2
```

- `mesh_pps: 2,3` *Default *값: 2,2 `mesh_pps` 옵션은 Mesh Points Per Segment(세그먼트 당 메쉬 포인트)의 줄임말이다. 이 옵션은 x, y 축을 따라 얼마나 많은 분할 포인트로 인터폴레이션을 할지 정해준다. 각 레벨링 측정포인트사이를 '분할'하여 공간을 나누도록 한다. `probe_count` 처럼 `mesh_pps` 는 x,y 의 정수쌍으로 입력된다. 또한 정수 하나만 입력시 x,y 양쪽 축에 대한 값으로 인정된다. 예제에서는 X축을 따라 4개(5-1)의 분할, y 축을 따라 2개(3-1)의 분할이 존재한다. 이는 곧 x 축으로 9개의 인터폴레이션 포인트와, y 축으로 6개의 인터폴레이션 포인트가 만들어진다. 이것은 총 13x8 의 메쉬를 형성하는 결과를 낳게된다. mesh_pps 를 0 으로 하면 메쉬 인터폴레이션은 꺼지고, 측정 매트릭스가 직접적인 샘플링 위치값이 될 것이다.
- `algorithm: lagrange` *Default 값: lagrange* 메쉬를 인터폴레이션 하는데 사용되는 알고리즘. `lagrange` 와 `bicubic` 를 선택해줄 수 있다. 라그랑지 인터폴레이션은 6개의 측정 포인트로 제한됩니다. 왜냐하면 진동이 더 큰수의 샘플로 발생하기 때문입니다. Bicubic 인터폴레이션은 각축을 따라 최소 4개의 측정 포인트가 필요합니다. 만약 4개 이하의 값이 주어지면 라그랑지 샘플링이 강제로 적용됩니다. 만약 `mesh_pps` 를 0 으로 설정하면 이 값은 무시되며 어떤 메쉬 인터폴레이션도 실행되지 않습니다.
- `bicubic_tension: 0.2` *Default 값: 0.2* 만약 `algorithm` 옵션이 bicubic 이면 tension 값 설정이 가능합니다. 더 높은 텐션값을 적용할 수록 더 많은 경사가 인터폴레이션될 것입니다. 이 값을 설정할 때 주의하십시오. 값이 커질 수록 또한 더 많은 오버슛이 발생합니다. 이 오버슛은 당신이 측정한 포인트보다 더 높거나 낮은 인터폴레이션 값을 내놓을 것입니다.

아래 그림은 위에 사용된 옵션들이 어떻게 인터폴레이션 메쉬를 구성하는지를 나타낸다.

![bedmesh_interpolated](img/bedmesh_interpolated.svg)

### 이동 스플리팅

Bed Mesh works by intercepting gcode move commands and applying a transform to their Z coordinate. Long moves must be split into smaller moves to correctly follow the shape of the bed. The options below control the splitting behavior.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 35,6
mesh_max: 240, 198
probe_count: 5,3
move_check_distance: 5
split_delta_z: .025
```

- `move_check_distance: 5` *Default 값: 5* 이동분할을 하기 전 Z 방향의 필요변화를 체크하기 위한 최소한의 거리 예를 들어, 5mm 이상의 이동은 알고리즘에 의해 이동하게 될 것이다. 5mm 마다 메쉬에 대한 Z lookup 이 발생하고, 이전 이동에서의 Z 값과 비교할 것이다. 만약 델타 프린터가 `split_delta_z`에 의해 쓰레시홀드를 직면하게 되면, 이동은 분할되고 이어질 것이다. 이 과정은 이동의 끝에 이르기까지 계속반복될 것이다. 그리고 그 끝점에서 마지막 값이 적용될 것이다. `move_check_distance` 보다 짧은 이동은 분할없이 이전이동의 최종 Z 값이 곧바로 적용되어 이동하게 된다.
- `split_delta_z: .025` *Default 값: .025* 위에 언급된바와 같이 이것은 이동 분할 여부를 판단할 때 요구되는 최소한의 편차값이다. 이 예제에서는, +/- 0.025mm 의 편차를 가지는 Z 값은 분할이 될 것이다.

일반적으로 이 옵션들에 대한 기본 값들을 적용하면 충분하다. 사실 `move_check_distance` 값으로 5mm 는 너무 과할 수도 있다. 하지만 고급사용자들은 최적화된 첫레이어를 형성하기 위해 이 옵션을 이용해 다양한 실험을 하려할지 모른다.

### 메쉬 페이드

"fade" 가 실행되면 Z 보정은 설정에서 정해놓은 거리 이상으로 올라가면 서서히 사라지게 된다. 이는 베드 모양에 따라 증가 혹은 감소하면서 레이어 높이가 올라가며 작은 보정이 적용된다. 페이드가 끝마치면, Z 보정은 더이상 진행되지 않는다. 그리고 프린팅의 최종 Top면은 베드의 모양보다 훨씬 더 평평하게 될 것이다. 페이드는 좀 별로인 특징도 가지고 있다. 만약 너무 빨리 페이드하게 되면 프린팅에 눈에 띄는 불량이 형성될 수 있다. 또한, 베드가 심각하게 휘어 있는 상태였다면 페이드는 출력물의 Z 높이를 줄이거나 늘리게 할 수도 있다. 그런 이유로 페이드는 꺼져 있는게 기본설정이다.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 35,6
mesh_max: 240, 198
probe_count: 5,3
fade_start: 1
fade_end: 10
fade_target: 0
```

- `fade_start: 1` *Default 값: 1* 보정 효과가 서서히 없어지는 시작높이. 서너개의 레이어를 출력하고 페이드 과정을 시작하는 것이 좋다.
- `fade_end: 10` *Default 값: 0* 페이드 효과를 마치는 높이. 만약 이 값이 `fade_start`보다 작다면 페이드는 꺼지게 된다. 이 값은 프린터 베드표면이 얼마나 휘어있느냐에 따라 값을 정해주면 된다. 아주 심각하게 휘어 있는 베드라면 좀더 두껍게 페이드 아웃을 진행해줘야 한다. 거의 평면에 가까운 베드라면 보다 빨리 보정이 완료되도록 이 값을 줄이면 되겠다. 만약 `fade_start`로 1을 지정했다면 10mm 값은 적당한 값이라 하겠다.
- `fade_target: 0` *Default 값: 메쉬의 평균 Z 값* `fade_target` 은 페이드가 끝났을때 전체 베드에 적용되는 추가 Z 오프셋으로 이해될 수 있다. 일반적으로 말해 우리는 이값을 0으로 하고 싶었다. 하지만, 0일 수 없는 상황이 발생하기도 한다. 예를 들어, 호밍 위치가 베드의 평균 레벨측정 높이보다 0.2mm 가 낮았다고 가정해보자. 만일 `fade_target`이 0 이라면, 페이드는 출력물을 베드 전체적으로 평균 0.2mm 만큼의 두께가 줄어들게 만들 것이다. `fade_target` 을 0.2로 셋팅을 하면 호밍 영역은 0.2mm 늘어날 것이다. 하지만, 베드의 나머지는 정확한 사이즈를 갖게 될 것이다. 일반적으로는 `fade_target`을 설정에서는 다루지 않고 메쉬의 평균높이를 사용하는 것이 더 좋다. 하지만, 베드의 특정 위치에서 프린트를 정확히 하고자 한다면 fade target 을 수동으로 최적화시키는 게 더 좋을 수도 있다.

### 상대적 참조 인덱스

대다수의 프로브는 주변상황에 쉽게 영향을 받는다. 예를 들면 열이나 충돌등에 의해 발생하는 부정확함이 발생할 수 있다. 이런 현상은 Probe Z-offset 을 결정하는걸 어렵게 만들 수 있다. 특히, 베드온도가 달라짐에 따라 레벨링이 틀어진다거나. 그런 이유로, 몇몇 프린터들은 Z 호밍을 위해선 Endstop을, 메쉬 캘리브레이션을 위해선 프로브를 사용한다. 이 프린터들은 상대적 참조 인덱스 설정을 해두면 득을 볼 수 있다.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 35,6
mesh_max: 240, 198
probe_count: 5,3
relative_reference_index: 7
```

- `relative_reference_index: 7` *Default 값: None (꺼짐)* 레벨측정 포인트가 생성되면 그것들은 인텍스로 할당된다. 당신이 klippy.log 에서나 BED_MESH_OUTPUT을 사용하여 인덱스를 살펴볼 수 있다. (보다 자세한 내용은 아래의 Bed Mesh GCodes에 있는 섹션을 참고하기 바란다) 만일 인덱스값에 `relative_reference_index` 옵션을 할당하게 되면, 이 좌표계에서 측정된 값은 프로브의 Z 오프셋을 대체할 것이다. 이것은 효과적으로 이 좌표계가 메쉬에 대한 "zero" 참조를 만든다.

상대적 참조 인덱스를 사용할 때는 Z endstop 캘리브레이션이 진행된 베드상의 지점에서 가장 가까운 인덱스를 골라야 한다. log 나 BED_MESH_OUTPUT을 사용하여 인덱스를 살펴본다면, 정확한 인덱스를 찾기 위해 "Probe" 헤더아래 리스트된 좌표계를 사용해야함을 기억하라.

### 결함 영역

베드 특정위치에 결함이 있는 상태에서 레벨 측정이 되었을때 어떤 위치가 부정확한 결과를 내는 곳인지 레포팅 하는것이 가능하다. 이에 대한 가장 좋은 예는 쉽게 제거할 수 있는 자석스틸시트를 사용한 베드에서 찾아볼 수 있다. 이 자석들 주변에 형성된 자기장은 인덕티브 방식의 프로브로 하여금 높이를 더 높거나 낮게 측정되게 만들 수 있다. 결과적으로 자석이 있는 표면에서는 부정확한 측정 결과를 얻게 된다. **주의: 이것을 전체 베드상에서 부정확한결과를 초래하는 프로브의 지역별 편차와 헷갈리지 말아야 한다.**

`faulty_region` 옵션은 이 영향을 보상하기 위해 설정할 수 있다. 형성된 지점이 결함 영역안에 놓인다면 베드 메쉬는 이 지역경계에 4 지점을 측정하게 될 것이다. 이렇게 측정된 값의 평균치를 얻고, 생성된 X,Y 좌표계에 메쉬의 Z 값으로 입력되게 된다.

```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 35,6
mesh_max: 240, 198
probe_count: 5,3
faulty_region_1_min: 130.0, 0.0
faulty_region_1_max: 145.0, 40.0
faulty_region_2_min: 225.0, 0.0
faulty_region_2_max: 250.0, 25.0
faulty_region_3_min: 165.0, 95.0
faulty_region_3_max: 205.0, 110.0
faulty_region_4_min: 30.0, 170.0
faulty_region_4_max: 45.0, 210.0
```

- `faulty_region_{1...99}_min` `faulty_region_{1..99}_max` *Default 값: None (해제됨)* 결함영역은 메쉬위치와 같은 방식으로 정의된다. 즉, 최소/최대 (X, Y) 좌표가 각 영역별로 설정된다. 결함영역은 메쉬 바깥으로 확장될 수도 있다. 그러나, 생성된 대체포인트 들은 항상 메쉬 경계 안에 있게 될 것이다. 어떤 두개의 영역도 겹칠 수는 없다.

아래 그림은 측정해야할 메쉬 좌표가 결함영역안에 들어갈 때 어떤 대체포인트를 이용해 측정되는지를 나타낸 그림이다. 아래 그림에 나타난 좌표들은 위 예시에 정의된 위치좌표를 사용했다. 대체 포인트와 좌표값은 초록색으로 표시해두었다.

![bedmesh_interpolated](img/bedmesh_faulty_regions.svg)

## 베드 메쉬 Gcode

### 캘리브레이션

`BED_MESH_CALIBRATE PROFILE=<name> METHOD=[manual | automatic] [<probe_parameter>=<value>] [<mesh_parameter>=<value>]` *Default Profile: default* *Default Method: automatic if a probe is detected, otherwise manual*

메드 메쉬 캘리브레이션을 위해 프로빙 과정을 초기화하라.

메쉬는 `PROFILE 파라메타로 특정된 프로필이나 특정되지 않았다면 `default`에 저장된다. 만일`METHOD=manual`이 선택되있다면 수동 측정이 될 것이다. 자동과 수동 측정사이의 변환이 될 때에는 메쉬 포인트가 자동으로 적용될 것이다.

측정된 영역을 수정하기 위해 메쉬 파라메터를 설정하는것이 가능하다. 아래 방법을 사용하면 된다. :

- 사각형 베드 (카테시안):
   - `MESH_MIN`
   - `MESH_MAX`
   - `PROBE_COUNT`
- 원형 베드 (델타):
   - `MESH_RADIUS`
   - `MESH_ORIGIN`
   - `ROUND_PROBE_COUNT`
- 모든 베드:
   - `RELATIVE_REFERNCE_INDEX`
   - `ALGORITHM`

See the configuration documentation above for details on how each parameter applies to the mesh.

### 프로필

`BED_MESH_PROFILE SAVE=<name> LOAD=<name> REMOVE=<name>`

After a BED_MESH_CALIBRATE has been performed, it is possible to save the current mesh state into a named profile. This makes it possible to load a mesh without re-probing the bed. After a profile has been saved using `BED_MESH_PROFILE SAVE=<name>` the `SAVE_CONFIG` gcode may be executed to write the profile to printer.cfg.

Profiles can be loaded by executing `BED_MESH_PROFILE LOAD=<name>`.

BED_MESH_CALIBRATE가 발생할 때마다 현재 상태가 *default*프로필에 자동으로 저장됩니다. 만일 이 프로필이 존재한다면 클리퍼가 시작될때 자동으로 불러와진다. 만일 이렇게 자동불러와지는것을 원치 않는다면 *default*프로필은 아래와 같은 방법으로 삭제할 수 있다:

`BED_MESH_PROFILE REMOVE=default`

어떤 다른 저장된 프로필도 같은 방식으로 삭제될 수 있다. 그저 제거하기 원하는 프로필 이름을 *default* 대신에 써넣으면 된다.

### 결과출력

`BED_MESH_OUTPUT PGP=[0 | 1]`

현재 메쉬 측정상태를 터미널에 출력한다. 메쉬 그자체가 출력됨을 기억하라

PGP 파라메터는 "Print Generated Points"의 줄임말이다. 만약 `PGP=1` 이렇게 셋팅을 하면, 생성된 측정 포인트들은 터미널에 위치결과를 내보낼 것이다.:

```
// bed_mesh: generated points
// Index | Tool Adjusted | Probe
// 0 | (11.0, 1.0) | (35.0, 6.0)
// 1 | (62.2, 1.0) | (86.2, 6.0)
// 2 | (113.5, 1.0) | (137.5, 6.0)
// 3 | (164.8, 1.0) | (188.8, 6.0)
// 4 | (216.0, 1.0) | (240.0, 6.0)
// 5 | (216.0, 97.0) | (240.0, 102.0)
// 6 | (164.8, 97.0) | (188.8, 102.0)
// 7 | (113.5, 97.0) | (137.5, 102.0)
// 8 | (62.2, 97.0) | (86.2, 102.0)
// 9 | (11.0, 97.0) | (35.0, 102.0)
// 10 | (11.0, 193.0) | (35.0, 198.0)
// 11 | (62.2, 193.0) | (86.2, 198.0)
// 12 | (113.5, 193.0) | (137.5, 198.0)
// 13 | (164.8, 193.0) | (188.8, 198.0)
// 14 | (216.0, 193.0) | (240.0, 198.0)
```

"Tool Adjusted" 포인트는 각 포인트에 대한 노즐의 위치를 말한다. 그리고 "Probe" 포인트는 프로브의 위치를 말한다. 수동으로 측정을 한다면, "Probe" 포인트는 노즐위치를 뜻함을 기억하라.

### 메쉬 상태 제거

`BED_MESH_CLEAR`

이 gcode 는 내부 메쉬 상태를 삭제하기 위해 사용된다.

### X/Y 오프셋을 적용

`BED_MESH_OFFSET [X=<value>] [Y=<value>]`

이것은 멀티 독립 익스트루더를 가진 프린터를 사용할 때 유용하다. 툴체인지 이후 정확한 Z 값을 보정하기 위해 offset 은 필수적이다. 오프셋은 가장 기본되는 익스트루도에 상대적인 값으로 설정된다. 만약 두번째 익스트루더가 첫번째 기본 익스트루더의 오른쪽에 마운트되어져 있다면 X 오프셋은 양의 값을 가져야 한다. 마찬가지로 두번째 익스트루더가 첫번째 익스트루더 뒤쪽에 마운트되어 있다면 Y 오프셋은 양의 값을 가져야 한다.
