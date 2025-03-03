# 자주 묻는 질문

1. [어떻게 프로젝트에 기부할수 있나요?](#어떻게-프로젝트에-기부할수-있나요)
1. [rotation_distance는 어떻게 계산합니까?](#rotation_distance는-어떻게-계산합니까)
1. [내 시리얼 포트는 어디에 있습니까?](#내-시리얼-포트는-어디에-있습니까)
1. [마이크로 컨트롤러가 다시 시작되면 장치가 /dev/ttyUSB1 로 변경됩니다](#마이크로-컨트롤러가-다시-시작되면-장치가-devttyUSB1-로-변경됩니다)
1. ["make flash" 가 동작하지 않습니다](#make-flash-가-동작하지-않습니다)
1. [시리얼 포트 전송 속도는 어떻게 변경합니까?](#시리얼-포트-전송-속도는-어떻게-변경합니까)
1. [Raspberry Pi 3 이외의 다른 기기에서 Klipper를 실행할 수 있습니까?](#Raspberry-Pi-3-이외의-다른-기기에서-Klipper를-실행할-수-있습니까)
1. [동일한 호스트 시스템에서 여러 Klipper 인스턴스를 실행할 수 있습니까?](#동일한-호스트-시스템에서-여러-Klipper-인스턴스를-실행할-수-있습니까)
1. [OctoPrint를 사용해야 합니까?](#OctoPrint를-사용해야-합니까)
1. [프린터를 원점으로 이동하기 전에 스테퍼를 이동할 수 없는 이유는 무엇입니까?](#프린터를-원점으로-이동하기-전에-스테퍼를-이동할-수-없는-이유는-무엇입니까)
1. [기본 설정에서 Z position_endstop이 0.5로 설정된 이유는 무엇입니까?](#기본-설정에서-Z-position_endstop이-0.5로-설정된-이유는-무엇입니까)
1. [Marlin에서 설정을 가져왔는데 X/Y 축은 잘 작동하지만 Z 축을 원점 복귀할 때 삐걱거리는 소리가 납니다](#Marlin에서-설정을-가져왔는데-XY-축은-잘-작동하지만-Z-축을-원점-복귀할-때-삐걱거리는-소리가-납니다)
1. [TMC 모터 드라이버가 인쇄 도중에 꺼집니다](#TMC-모터-드라이버가-인쇄-도중에-꺼집니다)
1. [랜덤하게 "MCU와의 통신 끊김" 오류가 계속 발생합니다](#랜덤하게-MCU와의-통신-끊김-오류가-계속-발생합니다)
1. [Raspberry Pi가 인쇄 중에 계속 재부팅됩니다](#Raspberry-Pi가-인쇄-중에-계속-재부팅됩니다.)
1. ["restart_method=command"를 설정하면 AVR 장치가 다시 시작할 때 멈춥니다](#restart_method=command를-설정하면-AVR-장치가-다시-시작할-때-멈춥니다)
1. [Raspberry Pi가 비정상 종료하면 히터가 켜져 있습니까?](#Raspberry-Pi가-비정상-종료하면-히터가-켜져-있습니까)
1. [Marlin 핀 번호를 Klipper 핀 이름으로 어떻게 변환합니까?](#Marlin-핀-번호를-Klipper-핀-이름으로-어떻게-변환합니까)
1. [내 장치를 특정 유형의 마이크로 컨트롤러 핀에 연결해야 합니까?](#내-장치를-특정-유형의-마이크로-컨트롤러-핀에-연결해야-합니까)
1. [M109/M190 "wait for temperature" 요청을 어떻게 취소합니까?](#M109/M190-wait-for-temperature-요청을-어떻게-취소합니까)
1. [프린터 탈조를 어떻게 알 수 있습니까?](#프린터-탈조를-어떻게-알-수-있습니까)
1. [Klipper가 "I lost my print" 오류를 보고하는 이유는 무엇입니까?](#Klipper가-I-lost-my-print-오류를-보고하는-이유는-무엇입니까)
1. [최신 소프트웨어로 업그레이드하려면 어떻게 합니까?](#최신-소프트웨어로-업그레이드하려면-어떻게-합니까)
1. [Klipper를 어떻게 제거합니까?](#Klipper를-어떻게-제거합니까)

## 어떻게 프로젝트에 기부할수 있나요?

Thanks. Kevin has a Patreon page at: <https://www.patreon.com/koconnor>

## rotation_distance는 어떻게 계산합니까?

이 문서를 [rotation distance document](Rotation_Distance.md) 보세요.

## 내 시리얼 포트는 어디에 있습니까?

USB 직렬 포트를 찾는 일반적인 방법은 호스트 시스템의 ssh 터미널에서 다음의 명령을 실행하면 됩니다. `ls /dev/serial/by-id/*`. 그럼 다음과 같이 나옵니다:

```
/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
```

위 명령에서 찾은 이름을 사용해서 config 파일에 적을 수도 있고 마이크로 컨트롤러에 펌웨어 업로드 할때도 사용됩니다. 예를 들어 flash 명령은 다음과 같이 사용할 수 있습니다:

```
sudo service klipper stop
make flash FLASH_DEVICE=/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
sudo service klipper start
```

그리고 config 파일을 보면 아래와 같이 mcu 항목이 작성되어 있습니다:

```
[mcu]
serial: /dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
```

그곳에 ls 명령어로 찾은 시리얼 내용을 복사하여 넣으십시오. 내용은 프린터에 따라 다를 수 있습니다.

여러 개의 마이크로컨트롤러를 사용하고 있는데 마이크로컨트롤러의 고유 ID(`ls '/dev/serial/by-id/`)가 없는 경우에는 대신 `ls /dev/serial/by-path/*` 명령을 사용하여 위의 지침을 따르십시오.

## 마이크로 컨트롤러가 다시 시작되면 장치가 /dev/ttyUSB1 로 변경됩니다

링크의 지침을 따르면 "[내 시리얼 포트는 어디에 있습니까?](#내-시리얼-포트는-어디에-있습니까)" 문제 해결이 가능합니다.

## "make flash" 가 동작하지 않습니다

"make flash" 명령어는 가장 일반적인 방법을 사용하여 펌웨어 업로드를 시도합니다. 불행히도 각 플랫폼마다 펌웨어 업로드하는 방법에는 많은 차이가 있습니다. 따라서 "make flash" 명령이 모든 보드에서 작동하지 않을 수 있습니다.

만약 간헐적인 오류가 발생하거나 표준 설정을 잘 따르고 있다면, 펌웨어 업로드는 하는 동안 Klipper가 실행되고 있지 않은지 다시 확인하십시오 (sudo service klipper stop), 또한 OctoPrint가 실행되고 있는지도 확인하십시오 (Octoprint 프린터 연결 해제를 클릭합니다), 그리고 나서 FLASH_DEVICE가 보드에 대해 올바르게 설정되어 있는지 확인하십시오. (참조 [위 질문](#내-시리얼-포트는-어디에-있습니까)).

그러나 "make flash"가 보드에서 작동하지 않으면 수동으로 플래시해야 합니다. 펌웨어 업로드에 대한 특정 명령어가 포함된 구성 파일이 config 디렉토리에 있는지 확인하십시오. 또한 보드 제조업체의 설명서에 장치에 펌웨어 업로드 하는 방법이 설명되어 있는지 확인하십시오. 마지막으로 "avrdude" 또는 "bossac"과 같은 도구를 사용하여 장치에 수동으로 펌웨어 업로드 할 수 있습니다. 추가 정보는 [bootloader document](Bootloaders.md)를 참조하십시오.

## 시리얼 포트 전송 속도는 어떻게 변경합니까?

Klipper의 권장 전송 속도는 250000입니다. 이 전송 속도는 Klipper가 지원하는 모든 마이크로 컨트롤러 보드에서 잘 작동합니다. 다른 전송 속도를 권장하는 온라인 가이드를 찾은 경우 가이드의 해당 부분을 무시하고 기본값 250000을 계속 사용하십시오.

어쨌든 전송 속도를 변경하려면 마이크로 컨트롤러에서 새 속도를 구성해야 하고 (**make menuconfig**를 통해서) 업데이트된 코드를 컴파일하고 마이크로 컨트롤러에 펌웨어 업로드 해야 합니다. Klipper printer.cfg 파일도 해당 전송 속도와 일치하도록 업데이트해야 합니다. (자세한 내용은 [config reference](Config_Reference.md#mcu) 참조). 예를 들어:

```
[mcu]
baud: 250000
```

OctoPrint 웹 페이지에 표시된 전송 속도는 내부 Klipper 마이크로 컨트롤러 전송 속도에 영향을 미치지 않습니다. Klipper를 사용할 때는 항상 OctoPrint 전송 속도를 250000 으로 설정하십시오.

Klipper 마이크로 컨트롤러 전송 속도는 마이크로 컨트롤러 부트로더의 전송 속도와 관련이 없습니다. 부트로더에 대한 추가 정보는 [bootloader document](Bootloaders.md)를 참조하십시오.

## Raspberry Pi 3 이외의 다른 기기에서 Klipper를 실행할 수 있습니까?

권장 하드웨어는 Raspberry Pi 2, Raspberry Pi 3 또는 Raspberry Pi 4입니다.

Klipper는 Raspberry Pi 1 및 Raspberry Pi Zero에서 실행되지만 이러한 보드에는 OctoPrint를 제대로 실행하기에 충분한 처리 능력이 없습니다. OctoPrint에서 직접 인쇄할 때 이러한 느린 시스템에서 인쇄 중단이 발생하는 것은 일반적입니다. (프린터는 OctoPrint가 이동 명령을 보낼 수 있는 것보다 더 빠르게 움직일 수 있습니다.) 어쨌든 이러한 느린 보드 중 하나에서 실행하려면 인쇄할 때 "virtual_sdcard" 기능을 사용하는 것을 고려하십시오 (자세한 내용은 [config reference](Config_Reference.md#virtual_sdcard) 참조).

For running on the Beaglebone, see the [Beaglebone specific installation instructions](Beaglebone.md).

Klipper는 다양한 컴퓨터에서 실행될 수 있습니다. Klipper 호스트 소프트웨어에는 Linux(또는 이와 유사한) 컴퓨터에서 실행되는 Python만 있으면 됩니다. 그러나 다른 시스템에서 실행하려면 기본적으로 필요한 소프트웨어를 설치하기 위해 Linux 관리자 지식이 필요합니다. 필요한 Linux 관리자에 대한 추가 정보는 [install-octopi.sh](../scripts/install-octopi.sh) 를 참조하십시오.

저가형 칩에서 Klipper 호스트 소프트웨어를 실행하려는 경우 최소한 "double precision floating point" 하드웨어가 있는 시스템이 필요하다는 점에 유의하십시오.

만약 일반 컴퓨터 또는 서버 시스템에서 Klipper 호스트 소프트웨어를 실행하려는 경우 Klipper에는 몇 가지 'real-time scheduling' 에 대한 요구 사항이 있습니다. 인쇄 중에 호스트 컴퓨터가 계산에 부하가 걸리는 컴퓨팅 작업(예: 하드 드라이브 조각 모음, 3d 렌더링, 대량 스와핑 등)을 수행하는 경우 Klipper가 출력 중 오류를 낼 수 있습니다.

참고: OctoPi 이미지를 사용하지 않는 경우 여러 Linux 배포판에서 직렬 통신을 방해할 수 있는 "ModemManager"(또는 유사한) 패키지를 활성화한다는 점에 유의하십시오. (이로 인해 Klipper가 무작위로 보이는 "MCU와의 통신 끊김" 오류를 보고할 수 있습니다.) 이러한 배포판 중 하나에 Klipper를 설치하는 경우 해당 패키지를 비활성화해야 할 수 있습니다.

## 동일한 호스트 시스템에서 여러 Klipper 인스턴스를 실행할 수 있습니까?

Klipper 호스트 소프트웨어의 여러 인스턴스를 실행할 수 있지만 그렇게 하려면 Linux 관리자 지식이 필요합니다. Klipper 설치 스크립트는 궁극적으로 다음 Unix 명령이 실행되도록 합니다:

```
~/klippy-env/bin/python ~/klipper/klippy/klippy.py ~/printer.cfg -l /tmp/klippy.log
```

각 인스턴스에 자체 프린터 구성 파일, 자체 로그 파일 및 전용 tty가 있는 한 위 명령의 여러 인스턴스를 실행할 수 있습니다. 예를 들어:

```
~/klippy-env/bin/python ~/klipper/klippy/klippy.py ~/printer2.cfg -l /tmp/klippy2.log -I /tmp/printer2
```

만약 이런 방식으로 여러 개의 Klipper 를 사용하기를 선택하였다면 시작, 중지 및 설치 스크립트(있는 경우)를 구현해야 합니다. [install-octopi.sh](../scripts/install-octopi.sh) 스크립트와 [klipper-start.sh](../scripts/klipper-start.sh) 스크립트가 예제가 유용할 수 있습니다.

## OctoPrint를 사용해야 합니까?

Klipper 소프트웨어는 OctoPrint에 의존하지 않습니다. 대체 소프트웨어를 사용하여 Klipper에 명령을 보낼 수 있지만 그렇게 하려면 Linux 관리자 지식이 필요합니다.

Klipper는 "/tmp/printer" 파일을 통해 "가상 직렬 포트"를 생성하고 해당 파일을 통해 고전적인 3d 프린터 직렬 인터페이스를 에뮬레이션 합니다. 일반적으로 프린터 직렬 포트에 "/tmp/printer"를 사용하도록 구성할 수 있는 한 대체 소프트웨어가 Klipper와 함께 작동할 수 있습니다.

## 프린터를 원점으로 이동하기 전에 스테퍼를 이동할 수 없는 이유는 무엇입니까?

이 기능은 실수로 익스트루더를 BED 나 벽에 부딪힐 가능성을 줄이기 위함입니다. 프린터가 홈으로 이동하면 소프트웨어는 각 이동이 구성 파일에 정의된 position_min/max 내에 있는지 확인하려고 시도합니다. 모터가 비활성화된 경우 (M84 또는 M18 명령을 통해)도 모터는 이동하기 전에 다시 원점 복귀해야 합니다.

OctoPrint를 통해 인쇄를 취소한 후 헤드를 이동하려면 OctoPrint 취소 순서를 변경하는 것이 좋습니다. 설정->GCODE 스크립트 아래의 웹 브라우저를 통해 OctoPrint에서 구성됩니다

인쇄가 완료된 후 헤드를 이동하려면 슬라이서의 "custom g-code" 섹션에 원하는 이동을 추가하는 것이 좋습니다.

만일 프린터가 오토홈을 할때 추가적인 작업이 필요한 경우 (또는 근본적으로 오토홈 프로세스가 없는 경우) config 파일에서 safe_z_home 또는 homing_override 섹션 사용을 고려하십시오. 진단 또는 디버깅 목적으로 스테퍼를 이동해야 하는 경우 config 파일에 force_move 섹션을 추가하는 것을 고려하십시오. 이러한 옵션에 대한 자세한 내용은 [config reference](Config_Reference.md#customized_homing)을 참조를 참조하세요.

## 기본 설정에서 Z position_endstop이 0.5로 설정된 이유는 무엇입니까?

직교 스타일 프린터의 경우 Z position_endstop은 엔드스톱이 트리거될 때 노즐이 BED에서 얼마나 멀리 떨어져 있는지 지정합니다. 가능하면 Z-max 엔드스톱을 사용하고 BED에서 멀리 떨어져 있는 것이 좋습니다 (이렇게 하면 BED에 충돌 가능성이 줄어듭니다). 그러나 BED를 향해 오토홈 해야 하는 경우 노즐이 BED에서 여전히 가까운 거리에 있을 때 트리거되도록 엔드스톱을 배치하는 것이 좋습니다. 이렇게 하면 축을 원점 복귀시킬 때 노즐이 BED에 닿기 전에 멈춥니다. 자세한 [bed level document](Bed_Level.md) 를 참조하십시오.

## Marlin에서 설정을 가져왔는데 X/Y 축은 잘 작동하지만 Z 축을 원점 복귀할 때 삐걱거리는 소리가 납니다

짧은 대답: 먼저 [config check document](Config_checks.md)에 설명된 대로 스테퍼 구성을 확인했는지 확인합니다. 문제가 지속되면 프린터 구성에서 max_z_velocity 설정을 줄여 보십시오.

긴 대답: 실제로 Marlin 은 일반적으로 초당 약 10000 step 의 속도로만 움직일 수 있습니다. 더 높은 속도로 이동하도록 요청되면 Marlin은 일반적으로 최대한 가능한 정도만 수행합니다. Klipper는 훨씬 더 높은 속도를 달성할 수 있지만 스테퍼 모터는 더 높은 속도로 이동하기에 충분한 토크가 없을 수 있습니다. 따라서 높은 기어비 또는 높은 마이크로스텝이 있는 Z축의 경우 실제 얻을 수 있는 max_z_velocity 를 설정하는 것이 Marlin에서 구성된 것보다 작을 수 있습니다.

## TMC 모터 드라이버가 인쇄 도중에 꺼집니다

"standalone mode"에서 TMC2208(또는 TMC2224) 드라이버를 사용하는 경우 최신 버전의 Klipper를 사용해야 합니다. TMC2208 "stealthchop" 드라이버 문제에 대한 해결 방법이 2020년 3월 중순에 Klipper에 추가되었습니다.

## 랜덤하게 "MCU와의 통신 끊김" 오류가 계속 발생합니다

이것은 일반적으로 호스트 시스템과 마이크로 컨트롤러 간의 USB 연결에 대한 하드웨어 오류로 인해 발생합니다. 아래 사항을 점검하세요:

- 호스트 머신과 마이크로 컨트롤러 사이에 좋은 품질의 USB 케이블을 사용하십시오. 플러그가 안전한지 확인하십시오.
- Raspberry Pi를 사용하는 경우 Raspberry Pi에 [good quality power supply](https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md) 를 사용하고 [good quality USB cable](https://www.raspberrypi.org/forums/viewtopic.php?p=589877#p589877)을 사용하여 해당 전원 공급 장치를 Pi에 연결합니다. OctoPrint에서 "전압 부족" 경고가 표시되면 이는 전원 공급 장치와 관련된 것이며 수정해야 합니다.
- 프린터의 전원 공급 장치에 과부하가 걸리지 않았는지 확인하십시오. (마이크로 컨트롤러의 USB 칩에 대한 전력 변동으로 인해 해당 칩이 재설정될 수 있습니다.)
- 스테퍼 모터, 히터 및 기타 프린터 와이어가 구겨지거나 닳지 않았는지 확인합니다. (프린터가 움직이면 결함이 있는 전선에 스트레스가 가해져 접촉이 끊어지거나 잠시 단락되거나 과도한 소음이 발생할 수 있습니다.)
- 프린터의 전원 공급 장치와 호스트의 5V 전원 공급 장치가 혼합되어 있을 때 USB 노이즈가 높다는 보고가 있었습니다. (프린터의 전원 공급 장치가 켜져 있거나 USB 케이블이 꽂혀 있을 때 마이크로 컨트롤러의 전원이 켜지면 5V 전원 공급 장치가 혼합되고 있음을 나타냅니다.) 단 하나의 소스에서 전력을 공급하는 것이 좋습니다. (또는 마이크로 컨트롤러 보드가 전원을 구성할 수 없는 경우 호스트와 마이크로 컨트롤러 간에 5V 전원을 전달하지 않도록 USB 케이블을 수정할 수 있습니다.)

## Raspberry Pi가 인쇄 중에 계속 재부팅됩니다

이것은 대부분 전압 변동이 원인일 가능성이 있습니다. 문제를 해결하려면 ["MCU와의 통신 끊김"](#랜덤하게-MCU와의-통신-끊김-오류가-계속-발생합니다)를 따르십시오.

## "restart_method=command"를 설정하면 AVR 장치가 다시 시작할 때 멈춥니다

AVR 부트로더의 일부 이전 버전에는 워치독 이벤트 처리에 알려진 버그가 있습니다. 이것은 일반적으로 printer.cfg 파일에 restart_method가 "command"로 설정되어 있을 때 나타납니다. 버그가 발생하면 전원이 제거되고 장치에 다시 공급될 때까지 AVR 장치가 응답하지 않습니다 (전원이 제거될 때까지 전원 또는 상태 LED도 반복적으로 깜박일 수 있음).

해결 방법은 "command" 이외의 restart_method를 사용하거나 업데이트된 부트로더를 AVR 장치에 플래시하는 것입니다. 새 부트로더를 플래싱하는 것은 일반적으로 외부 프로그래머가 필요한 일회성 단계입니다. 자세한 내용은 [Bootloaders](Bootloaders.md)를 참조하십시오.

## Raspberry Pi가 비정상 종료하면 히터가 켜져 있습니까?

호스트 소프트웨어는 이를 방지하도록 설계되었습니다. 호스트가 히터를 활성화하면 소프트웨어는 5초마다 활성화를 확인해야 합니다. 마이크로 컨트롤러가 5초마다 확인 메시지를 받지 못하면 모든 히터와 스테퍼 모터를 끄도록 설계된 "종료" 상태가 됩니다.

자세한 내용은 [MCU commands](MCU_Commands.md)의 "config_digital_out" 명령을 참조하십시오.

또한 마이크로 컨트롤러 소프트웨어는 시작 시 각 히터의 최소 및 최대 온도 범위로 제한됩니다 (자세한 내용은 [config reference](Config_Reference.md#extruder)의 min_temp 및 max_temp 매개변수 참조). 마이크로 컨트롤러가 온도가 해당 범위를 벗어난 것을 감지하면 "종료" 상태로 됩니다.

이와 별도로 호스트 소프트웨어는 히터와 온도 센서가 올바르게 작동하는지 확인하는 코드도 구현합니다. 자세한 내용은 [config reference](Config_Reference.md#verify_heater)를 참조하세요.

## Marlin 핀 번호를 Klipper 핀 이름으로 어떻게 변환합니까?

긴 대답: [sample-aliases.cfg](../config/sample-aliases.cfg) 파일에서 매핑을 사용할 수 있습니다. 이 파일을 실제 마이크로 컨트롤러 핀 이름을 찾는 가이드로 사용하십시오. (관련 [board_pins](Config_Reference.md#board_pins) config 섹션을 파일에 복사하고 config의 별칭을 사용할 수도 있지만 실제 마이크로 컨트롤러 핀 이름을 번역하여 사용하는 것이 좋습니다.) sample-aliases.cfg 파일에 유의하십시오. "D" 대신 접두사 "ar"로 시작하는 핀 이름을 사용하고 (예: Arduino 핀 D23은 Klipper 별칭 ar23임) "A" 대신 접두사 "analog"를 사용합니다(예: Arduino 핀 A14는 Klipper 별칭 analog14임).

짧은 대답: Klipper는 마이크로 컨트롤러에서 정의한 표준 핀 이름을 사용합니다. Atmega 칩에서 이러한 하드웨어 핀은 PA4, PC7 또는 PD2와 같은 이름을 갖습니다.

오래 전에 Arduino 프로젝트는 증가하는 숫자를 기반으로 하는 고유한 핀 이름을 위해 표준 하드웨어 이름을 사용하지 않기로 결정했습니다. 이러한 Arduino 이름은 일반적으로 D23 또는 A14처럼 보입니다. 이것은 큰 혼란을 가져온 불행한 선택이었습니다. 특히 Arduino 핀 번호는 종종 동일한 하드웨어 이름으로 변환되지 않습니다. 예를 들어, D21은 어떤 Arduino 보드에서 PD0이지만 다른 Arduino 보드에서는 PC7입니다.

이러한 혼동을 피하기 위해 Klipper 코어 코드는 마이크로 컨트롤러에서 정의한 표준 핀 이름을 사용합니다.

## 내 장치를 특정 유형의 마이크로 컨트롤러 핀에 연결해야 합니까?

장치 유형 및 핀 유형에 따라 다릅니다:

ADC 핀(또는 아날로그 핀): 서미스터 및 유사한 "아날로그" 센서의 경우 장치를 마이크로 컨트롤러의 "아날로그" 또는 "ADC" 가능 핀에 연결해야 합니다. 아날로그를 사용할 수 없는 핀을 사용하도록 Klipper를 구성하면 Klipper는 "유효하지 않은 ADC 핀" 오류를 보고합니다.

PWM 핀(또는 타이머 핀): Klipper는 기본적으로 모든 장치에 대해 하드웨어 PWM을 사용하지 않습니다. 따라서 일반적으로 히터, 팬 및 유사한 장치를 범용 IO 핀에 연결할 수 있습니다. 그러나 팬 및 output_pin 장치는 선택적으로 hardware_pwm: True 를 사용하도록 구성할 수 있습니다. 이 경우 마이크로 컨트롤러는 핀에서 하드웨어 PWM을 지원해야 합니다(그렇지 않으면 Klipper는 "유효한 PWM 핀이 아닙니다" 오류를 보고합니다).

IRQ 핀(또는 인터럽트 핀): Klipper는 IO 핀에서 하드웨어 인터럽트를 사용하지 않으므로 장치를 이러한 마이크로 컨트롤러 핀 중 하나에 연결할 필요가 없습니다.

SPI 핀: 하드웨어 SPI를 사용할 때 핀을 마이크로 컨트롤러의 SPI 가능 핀에 연결해야 합니다. 그러나 대부분의 장치는 "소프트웨어 SPI"를 사용하도록 구성할 수 있으며, 이 경우 모든 범용 IO 핀을 사용할 수 있습니다.

I2C 핀: I2C를 사용할 때 핀을 마이크로 컨트롤러의 I2C 가능 핀에 연결해야 합니다.

다른 장치는 범용 IO 핀에 연결할 수 있습니다. 예를 들어, 스테퍼, 히터, 팬, Z 프로브, 서보, LED, 일반 hd44780/st7920 LCD 디스플레이, Trinamic UART 제어 라인은 모든 범용 IO 핀에 배선될 수 있습니다.

## M109/M190 "wait for temperature" 요청을 어떻게 취소합니까?

OctoPrint 터미널 탭으로 이동하여 터미널 상자에서 M112 명령을 실행합니다. M112 명령은 Klipper를 "종료" 상태로 만들고 OctoPrint와 Klipper의 연결을 끊습니다. OctoPrint 연결 영역으로 이동하고 "연결"을 클릭하여 OctoPrint가 다시 연결되도록 합니다. 터미널 탭으로 돌아가 FIRMWARE_RESTART 명령을 실행하여 Klipper 오류 상태를 지웁니다. 이 순서를 완료한 후 이전 가열 요청이 취소되고 새 인쇄가 시작될 수 있습니다.

## 프린터 탈조를 어떻게 알 수 있습니까?

이렇게 하면 가능합니다. 프린터를 홈으로 이동하고 `GET_POSITION` 명령을 실행하고 인쇄를 실행하고 다시 홈으로 이동하고 또 다른 `GET_POSITION`을 실행합니다. 그런 다음 `mcu:` 행의 값을 비교합니다.

이것은 실제로 무언가를 인쇄하고 필라멘트를 낭비할 필요 없이 스테퍼 모터 전류, 가속도 및 속도와 같은 설정을 조정하는 데 도움이 될 수 있습니다. `GET_POSITION` 명령 사이에 고속 이동을 실행하기만 하면 됩니다.

엔드스톱 스위치 자체는 약간 다른 위치에서 트리거되는 경향이 있으므로 몇 가지 마이크로스텝의 차이는 엔드스톱 부정확성의 결과일 수 있습니다. 스테퍼 모터 자체는 4 full step 의 배수로 탈조 될 수 있습니다. (따라서 16개의 마이크로스텝을 사용하는 경우 스테퍼에서 손실된 스텝은 "mcu:" 스텝 카운터가 64개의 마이크로스텝의 배수만큼 떨어져 있는 결과를 낳습니다.)

## Klipper가 "I lost my print" 오류를 보고하는 이유는 무엇입니까!

간단한 답변: 우리는 프린터가 문제를 감지하여 근본적인 문제를 수정하고 우수한 품질의 인쇄물을 얻을 수 있는지 알고 싶습니다. 프린터가 저품질 인쇄물을 조용히 생산하는 것을 원하지 않습니다.

긴 답변: Klipper는 많은 일시적인 문제를 자동으로 해결하도록 설계되었습니다. 예를 들어, 자동으로 통신 오류를 감지하고 재전송합니다. 사전에 작업을 예약하고 여러 계층에서 명령을 버퍼링하여 간헐적인 간섭이 있는 경우에도 정확한 타이밍을 가능하게 합니다. 그러나 소프트웨어가 복구할 수 없는 오류를 감지하거나 잘못된 조치를 취하도록 명령을 받았거나 명령받은 작업을 절망적으로 수행할 수 없음을 감지한 경우Klipper는 오류를 보고합니다. 이러한 상황에서는 낮은 품질(또는 그 이상)의 인쇄물을 생성할 위험이 높습니다. 사용자에게 경고하면 근본적인 문제를 해결하고 인쇄물의 전반적인 품질을 개선할 수 있기를 바랍니다.

몇 가지 관련 질문이 있습니다. Klipper가 대신 인쇄를 일시 중지하지 않는 이유는 무엇입니까? 대신 경고를 보고하시겠습니까? 인쇄하기 전에 오류를 확인하시겠습니까? 사용자가 입력한 명령의 오류를 무시하시겠습니까? 등? 현재 Klipper는 gcode 프로토콜을 사용하여 명령을 읽지만 불행히도 gcode 명령 프로토콜은 오늘날 이러한 대안을 실용적으로 만들 만큼 유연하지 않습니다. 비정상적인 이벤트 발생시 사용자 경험을 개선하는 데 개발자의 관심이 있지만 많은 인프라 작업 (gcode에서 전환 포함)이 필요할 것으로 예상됩니다.

## 최신 소프트웨어로 업그레이드하려면 어떻게 합니까?

소프트웨어 업그레이드의 첫 번째 단계는 최신 [config changes](Config_Changes.md) 를 검토하는 것입니다. 업그레이드하기 전에 이 문서를 검토하는 것이 좋습니다.

업그레이드할 준비가 되면 일반적인 방법은 Raspberry Pi에 ssh 로 접속하고 다음을 실행하는 것입니다:

```
cd ~/klipper
git pull
~/klipper/scripts/install-octopi.sh
```

그런 다음 마이크로 컨트롤러 코드를 다시 컴파일하고 펌웨어 업로드 할 수 있습니다. 예를 들어:

```
make menuconfig
make clean
make

sudo service klipper stop
make flash FLASH_DEVICE=/dev/ttyACM0
sudo service klipper start
```

그러나 호스트 소프트웨어만 변경되는 경우가 많습니다. 이 경우 다음을 사용하여 호스트 소프트웨어만 업데이트하고 다시 시작할 수 있습니다:

```
cd ~/klipper
git pull
sudo service klipper restart
```

만약에 이렇게 했는데도 마이크로 컨트롤러를 다시 펌업 해야 한다고 경고하거나 기타 비정상적인 오류가 발생하면 위에 설명된 전체 업그레이드 단계를 따르십시오.

오류가 지속되면 프린터 config 를 수정해야 할 수 있으므로 [config changes](Config_Changes.md)를 다시 확인하십시오.

RESTART 및 FIRMWARE_RESTART gcode 명령은 새 호스트 소프트웨어를 로드하지 않습니다. 호스트 소프트웨어 변경 사항을 적용하려면 위의 "sudo service klipper restart" 및 "make flash" 명령이 필요합니다.

## Klipper를 어떻게 제거합니까?

펌웨어 쪽에서는 특별히 해 줄 일이 없습니다. 그냥 새로 사용할 펌웨어 업로드 가이드를 따라서 업로드 하세요.

라즈베리 파이 쪽에서 제거 스크립트는 [scripts/klipper-uninstall.sh](../scripts/klipper-uninstall.sh) 를 사용할 수 있습니다. 예를 들어:

```
sudo ~/klipper/scripts/klipper-uninstall.sh
rm -rf ~/klippy-env ~/klipper
```
