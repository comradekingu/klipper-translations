# 基准测试

这个文档解释了Klipper的基准测试。

## 微控制器（Micro-controller）基准测试

本节描述了用于生成 Klipper 微控制器步进率（step rate）基准的机制。

基准的主要目标是提供一个一致的机制，用于测量软件中编码更改的影响。次要目标是提供高级别指标，用于比较芯片和软件平台之间的性能。

步进率(stepping rate)基准是为了找到硬件和软件可以达到的最大步进率。这个基准步进率在日常使用中是无法实现的，因为Klipper在任何实际使用中都需要执行其他任务（例如，MCU/主机通信、温度读取、限位检查）。

一般而言，基准测试的引脚被选择用于闪烁 LED 或其他安全引脚。 ** 在运行基准测试之前，始终验证驱动配置的引脚是安全的。** 不建议在基准测试期间驱动实际的步进器。

### 步进率基准测试

测试是使用console.py工具进行的（在<Debugging.md>中描述）。为特定的硬件平台配置微控制器（见下文），然后将以下内容剪切并粘贴到console.py终端窗口:

```
SET start_clock {clock+freq}
SET ticks 1000

reset_step_clock oid=0 clock={start_clock}
set_next_step_dir oid=0 dir=0
queue_step oid=0 interval={ticks} count=60000 add=0
set_next_step_dir oid=0 dir=1
queue_step oid=0 interval=3000 count=1 add=0

reset_step_clock oid=1 clock={start_clock}
set_next_step_dir oid=1 dir=0
queue_step oid=1 interval={ticks} count=60000 add=0
set_next_step_dir oid=1 dir=1
queue_step oid=1 interval=3000 count=1 add=0

reset_step_clock oid=2 clock={start_clock}
set_next_step_dir oid=2 dir=0
queue_step oid=2 interval={ticks} count=60000 add=0
set_next_step_dir oid=2 dir=1
queue_step oid=2 interval=3000 count=1 add=0
```

以上测试三个步进器同时步进。如果运行上述程序的结果是 "重新安排定时器在过去 "或 "步进器在过去太远 "的错误，则表明`ticks`参数太低（它导致步进速度太快）。我们的目标是找到能够可靠地导致成功完成测试的最低的ticks参数设置。应该可以将ticks参数一分为二，直到找到一个稳定的值。

在失败的情况下，可以复制和粘贴以下内容来清除错误，为下一次测试做准备：

```
clear_shutdown
```

为了获得单一步进电机的基准测试，使用了相同的配置序列，但只将上述测试的第一块剪切并粘贴到 console.py 窗口。

To produce the benchmarks found in the [Features](Features.md) document, the total number of steps per second is calculated by multiplying the number of active steppers with the nominal mcu frequency and dividing by the final ticks parameter. The results are rounded to the nearest K. For example, with three active steppers:

```
ECHO Test result is: {"%.0fK" % (3. * freq / ticks / 1000.)}
```

The benchmarks are run with parameters suitable for TMC Drivers. For micro-controllers that support `STEPPER_BOTH_EDGE=1` (as reported in the `MCU config` line when console.py first starts) use `step_pulse_duration=0` and `invert_step=-1` to enable optimized stepping on both edges of the step pulse. For other micro-controllers use a `step_pulse_duration` corresponding to 100ns.

### AVR步进率基准测试

在AVR芯片上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PA5 dir_pin=PA4 invert_step=0 step_pulse_ticks=32
config_stepper oid=1 step_pin=PA3 dir_pin=PA2 invert_step=0 step_pulse_ticks=32
config_stepper oid=2 step_pin=PC7 dir_pin=PC6 invert_step=0 step_pulse_ticks=32
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `avr-gcc (GCC) 5.4.0`. Both the 16Mhz and 20Mhz tests were run using simulavr configured for an atmega644p (previous tests have confirmed simulavr results match tests on both a 16Mhz at90usb and a 16Mhz atmega2560).

| avr | ticks |
| --- | --- |
| 1个步进电机 | 102 |
| 3个步进电机 | 486 |

### Arduino Due 的步速率基准测试

以下是在Due上使用的配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PB27 dir_pin=PA21 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PB26 dir_pin=PC30 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PA21 dir_pin=PC30 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0`.

| sam3x8e | ticks |
| --- | --- |
| 1个步进电机 | 66 |
| 3个步进电机 | 257 |

### Duet Maestro 步进率基准测试

在Duet Maestro上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PC26 dir_pin=PC18 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PC26 dir_pin=PA8 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PC26 dir_pin=PB4 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0`.

| sam4s8c | ticks |
| --- | --- |
| 1个步进电机 | 71 |
| 3个步进电机 | 260 |

### Duet Wifi 步进率基准测试

在Duet Wifi上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PD6 dir_pin=PD11 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PD7 dir_pin=PD12 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PD8 dir_pin=PD13 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `gcc version 10.3.1 20210621 (release) (GNU Arm Embedded Toolchain 10.3-2021.07)`.

| sam4e8e | ticks |
| --- | --- |
| 1个步进电机 | 48 |
| 3个步进电机 | 215 |

### Beaglebone PRU 步进率基准测试

在PRU上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=gpio0_23 dir_pin=gpio1_12 invert_step=0 step_pulse_ticks=20
config_stepper oid=1 step_pin=gpio1_15 dir_pin=gpio0_26 invert_step=0 step_pulse_ticks=20
config_stepper oid=2 step_pin=gpio0_22 dir_pin=gpio2_1 invert_step=0 step_pulse_ticks=20
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `pru-gcc (GCC) 8.0.0 20170530 (experimental)`.

| 可编程实时单元 | ticks |
| --- | --- |
| 1个步进电机 | 231 |
| 3个步进电机 | 847 |

### STM32F042 步进率基准测试

在STM32F042上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PA1 dir_pin=PA2 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PA3 dir_pin=PA2 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PB8 dir_pin=PA2 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0`.

| stm32f042 | ticks |
| --- | --- |
| 1个步进电机 | 59 |
| 3个步进电机 | 249 |

### STM32F103 步速率基准测试

在STM32F103上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PC13 dir_pin=PB5 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PB3 dir_pin=PB6 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PA4 dir_pin=PB7 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0`.

| stm32f103 | ticks |
| --- | --- |
| 1个步进电机 | 61 |
| 3个步进电机 | 264 |

### STM32F4 步进率基准测试

在STM32F4上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PA5 dir_pin=PB5 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PB2 dir_pin=PB6 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PB3 dir_pin=PB7 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0`. The STM32F407 results were obtained by running an STM32F407 binary on an STM32F446 (and thus using a 168Mhz clock).

| stm32f446 | ticks |
| --- | --- |
| 1个步进电机 | 46 |
| 3个步进电机 | 205 |

| stm32f407 | ticks |
| --- | --- |
| 1个步进电机 | 46 |
| 3个步进电机 | 205 |

### LPC176x 步进率基准测试

在LPC176x上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=P1.20 dir_pin=P1.18 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=P1.21 dir_pin=P1.18 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=P1.23 dir_pin=P1.18 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0`. The 120Mhz LPC1769 results were obtained by overclocking an LPC1768 to 120Mhz.

| lpc1768 | ticks |
| --- | --- |
| 1个步进电机 | 52 |
| 3个步进电机 | 222 |

| lpc1769 | ticks |
| --- | --- |
| 1个步进电机 | 51 |
| 3个步进电机 | 222 |

### SAMD21 步速率基准测试

在SAMD21上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PA27 dir_pin=PA20 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PB3 dir_pin=PA21 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PA17 dir_pin=PA21 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0` on a SAMD21G18 micro-controller.

| SAMD21 | ticks |
| --- | --- |
| 1个步进电机 | 70 |
| 3个步进电机 | 306 |

### SAMD51 步速率基准测试

在SAMD51上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=PA22 dir_pin=PA20 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=PA22 dir_pin=PA21 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=PA22 dir_pin=PA19 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0` on a SAMD51J19A micro-controller.

| SAMD51 | ticks |
| --- | --- |
| 1个步进电机 | 39 |
| 3个步进电机 | 191 |
| 1 stepper (200Mhz) | 39 |
| 3 stepper (200Mhz) | 181 |

### RP2040 步速率基准测试

RP2040 上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=gpio25 dir_pin=gpio3 invert_step=-1 step_pulse_ticks=0
config_stepper oid=1 step_pin=gpio26 dir_pin=gpio4 invert_step=-1 step_pulse_ticks=0
config_stepper oid=2 step_pin=gpio27 dir_pin=gpio5 invert_step=-1 step_pulse_ticks=0
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0` on a Raspberry Pi Pico board.

| rp2040 | ticks |
| --- | --- |
| 1个步进电机 | 5 |
| 3个步进电机 | 22 |

### Linux MCU 步速率基准测试

树莓派上使用以下配置序列：

```
allocate_oids count=3
config_stepper oid=0 step_pin=gpio2 dir_pin=gpio3 invert_step=0 step_pulse_ticks=5
config_stepper oid=1 step_pin=gpio4 dir_pin=gpio5 invert_step=0 step_pulse_ticks=5
config_stepper oid=2 step_pin=gpio6 dir_pin=gpio17 invert_step=0 step_pulse_ticks=5
finalize_config crc=0
```

The test was last run on commit `59314d99` with gcc version `gcc (Raspbian 8.3.0-6+rpi1) 8.3.0` on a Raspberry Pi 3 (revision a02082). It was difficult to get stable results in this benchmark.

| Linux (RPi3) | ticks |
| --- | --- |
| 1个步进电机 | 160 |
| 3个步进电机 | 380 |

## Command dispatch 基准测试

命令调度基准测试微控制器可以处理多少个 "dummy"命令。它主要是对硬件通信机制的测试。该测试使用console.py工具（在<Debugging.md>中描述）运行。以下是剪切并粘贴到console.py终端窗口的内容:

```
DELAY {clock + 2*freq} get_uptime
FLOOD 100000 0.0 debug_nop
get_uptime
```

当测试完成后，确定两个 "正常运行时间 "响应信息中报告的时钟之间的差异。然后，每秒钟的总命令数是`100000 * mcu_frequency / clock_diff`。

注意，这个测试可能会使Raspberry Pi的USB/CPU容量达到饱和。如果在Raspberry Pi、Beaglebone或类似的主机上运行，那么要增加延迟（例如，`DELAY {clock + 20*freq} get_uptime`）。在适用的情况下，下面的基准是用控制台.py在桌面类机器上运行，设备通过高速集线器连接。

| MCU | Rate | Build | Build compiler |
| --- | --- | --- | --- |
| stm32f042 (CAN) | 18K | c105adc8 | arm-none-eabi-gcc (GNU Tools 7-2018-q3-update) 7.3.1 |
| atmega2560 (串行总线) | 23K | b161a69e | avr-gcc (GCC) 4.8.1 |
| sam3x8e (串行总线) | 23K | b161a69e | arm-none-eabi-gcc (Fedora 7.1.0-5.fc27) 7.1.0 |
| at90usb1286 (USB) | 75K | 01d2183f | avr-gcc (GCC) 5.4.0 |
| samd21 (USB) | 223K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| PRU（共享内存） | 260K | c5968a08 | pru-gcc (GCC) 8.0.0 20170530 (experimental) |
| stm32f103 (USB) | 355K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| sam3x8e (USB) | 418K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| lpc1768 (USB) | 534K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| lpc1769 (USB) | 628K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| sam4s8c (USB) | 650K | 8d4a5c16 | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| samd51 (USB) | 864K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| stm32f446 (USB) | 870K | 01d2183f | arm-none-eabi-gcc (Fedora 7.4.0-1.fc30) 7.4.0 |
| rp2040 (USB) | 873K | c5667193 | arm-none-eabi-gcc (Fedora 10.2.0-4.fc34) 10.2.0 |

## Host 基准测试

可以使用“批处理模式”处理机制（在 <Debugging.md> 中描述）在主机软件上运行时序测试。这通常是通过选择一个大而复杂的 G 代码文件并计时主机软件处理它所需的时间来完成的。例如：

```
time ~/klippy-env/bin/python ./klippy/klippy.py config/example-cartesian.cfg -i something_complex.gcode -o /dev/null -d out/klipper.dict
```
