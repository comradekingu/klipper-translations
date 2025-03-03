# RPi microcontroller

这个文档描述了在RPi上运行Klipper的过程，并使用相同的RPi作为辅助MCU。

## 为什么使用RPi作为辅助MCU？

通常情况下，专门用于控制3D打印机的MCU有有限的、预先配置好的引脚来管理主要的打印功能（热敏电阻、挤出机、步进器...）。使用安装了Klipper的RPi作为辅助MCU，可以直接使用klipper内的GPIO和RPi的总线（i2c，spi），而不需要使用Octoprint插件（如果使用的话）或外部程序，从而能够控制打印GCODE内的一切。

**警告**。如果你的平台是*Beaglebone*，并且你已经正确地按照安装步骤进行了安装，那么linux mcu已经为你的系统安装和配置了。

## 安装 rc 脚本

如果你想把主机作为一个辅助MCU，klipper_mcu进程必须在klippy进程之前运行。

安装 Klipper 后，运行以下命令来安装脚本：

```
cd ~/klipper/
sudo cp "./scripts/klipper-mcu-start.sh" /etc/init.d/klipper_mcu
sudo update-rc.d klipper_mcu defaults
```

## Enabling SPI

Make sure the Linux SPI driver is enabled by running sudo raspi-config and enabling SPI under the "Interfacing options" menu.

## 构建微控制器代码

要编译的 Klipper 微控制器代码，需要先将编译配置设置为“Linux Process”：

```
cd ~/klipper/
make menuconfig
```

在菜单中，设置“Microcontroller Archetecture”（微控制器架构）为“Linux Process”（Linux 进程），然后保存(save)并退出(exit)。

要构建和安装新的微控制器代码，请运行：

```
sudo service klipper stop
make flash
sudo service klipper start
```

如果klippy.log在试图连接到`/tmp/klipper_host_mcu`时输出 "Permission denied" 错误，那么你需要将你的用户添加到tty用户组。下面的命令将把 "pi "用户添加到tty用户组中：

```
sudo usermod -a -G tty pi
```

## 剩余的配置

Complete the installation by configuring Klipper secondary MCU following the instructions in [RaspberryPi sample config](../config/sample-raspberry-pi.cfg) and [Multi MCU sample config](../config/sample-multi-mcu.cfg).

## Optional: Identify the correct gpiochip

On Rasperry and on many clones the pins exposed on the GPIO belong to the first gpiochip. They can therefore be used on klipper simply by referring them with the name `gpio0..n`. However, there are cases in which the exposed pins belong to gpiochips other than the first. For example in the case of some OrangePi models or if a Port Expander is used. In these cases it is useful to use the commands to access the *Linux GPIO character device* to verify the configuration.

To install the *Linux GPIO character device - binary* on a debian based distro like octopi run:

```
sudo apt-get install gpiod
```

To check available gpiochip run:

```
gpiodetect
```

To check the pin number and the pin availability tun:

```
gpioinfo
```

The chosen pin can thus be used within the configuration as `gpiochip<n>/gpio<o>` where **n** is the chip number as seen by the `gpiodetect` command and **o** is the line number seen by the` gpioinfo` command.

***Warning:*** only gpio marked as `unused` can be used. It is not possible for a *line* to be used by multiple processes simultaneously.

For example on a RPi 3B+ where klipper use the GPIO20 for a switch:

```
$ gpiodetect
gpiochip0 [pinctrl-bcm2835] (54 lines)
gpiochip1 [raspberrypi-exp-gpio] (8 lines)

$ gpioinfo
gpiochip0 - 54 lines:
        line   0:      unnamed       unused   input  active-high
        line   1:      unnamed       unused   input  active-high
        line   2:      unnamed       unused   input  active-high
        line   3:      unnamed       unused   input  active-high
        line   4:      unnamed       unused   input  active-high
        line   5:      unnamed       unused   input  active-high
        line   6:      unnamed       unused   input  active-high
        line   7:      unnamed       unused   input  active-high
        line   8:      unnamed       unused   input  active-high
        line   9:      unnamed       unused   input  active-high
        line  10:      unnamed       unused   input  active-high
        line  11:      unnamed       unused   input  active-high
        line  12:      unnamed       unused   input  active-high
        line  13:      unnamed       unused   input  active-high
        line  14:      unnamed       unused   input  active-high
        line  15:      unnamed       unused   input  active-high
        line  16:      unnamed       unused   input  active-high
        line  17:      unnamed       unused   input  active-high
        line  18:      unnamed       unused   input  active-high
        line  19:      unnamed       unused   input  active-high
        line  20:      unnamed    "klipper"  output  active-high [used]
        line  21:      unnamed       unused   input  active-high
        line  22:      unnamed       unused   input  active-high
        line  23:      unnamed       unused   input  active-high
        line  24:      unnamed       unused   input  active-high
        line  25:      unnamed       unused   input  active-high
        line  26:      unnamed       unused   input  active-high
        line  27:      unnamed       unused   input  active-high
        line  28:      unnamed       unused   input  active-high
        line  29:      unnamed       "led0"  output  active-high [used]
        line  30:      unnamed       unused   input  active-high
        line  31:      unnamed       unused   input  active-high
        line  32:      unnamed       unused   input  active-high
        line  33:      unnamed       unused   input  active-high
        line  34:      unnamed       unused   input  active-high
        line  35:      unnamed       unused   input  active-high
        line  36:      unnamed       unused   input  active-high
        line  37:      unnamed       unused   input  active-high
        line  38:      unnamed       unused   input  active-high
        line  39:      unnamed       unused   input  active-high
        line  40:      unnamed       unused   input  active-high
        line  41:      unnamed       unused   input  active-high
        line  42:      unnamed       unused   input  active-high
        line  43:      unnamed       unused   input  active-high
        line  44:      unnamed       unused   input  active-high
        line  45:      unnamed       unused   input  active-high
        line  46:      unnamed       unused   input  active-high
        line  47:      unnamed       unused   input  active-high
        line  48:      unnamed       unused   input  active-high
        line  49:      unnamed       unused   input  active-high
        line  50:      unnamed       unused   input  active-high
        line  51:      unnamed       unused   input  active-high
        line  52:      unnamed       unused   input  active-high
        line  53:      unnamed       unused   input  active-high
gpiochip1 - 8 lines:
        line   0:      unnamed       unused   input  active-high
        line   1:      unnamed       unused   input  active-high
        line   2:      unnamed       "led1"  output   active-low [used]
        line   3:      unnamed       unused   input  active-high
        line   4:      unnamed       unused   input  active-high
        line   5:      unnamed       unused   input  active-high
        line   6:      unnamed       unused   input  active-high
        line   7:      unnamed       unused   input  active-high
```

## Optional: Hardware PWM

Raspberry Pi's have two PWM channels (PWM0 and PWM1) which are exposed on the header or if not, can be routed to existing gpio pins. The Linux mcu daemon uses the pwmchip sysfs interface to control hardware pwm devices on Linux hosts. The pwm sysfs interface is not exposed by default on a Raspberry and can be activated by adding a line to `/boot/config.txt`:

```
# Enable pwmchip sysfs interface
dtoverlay=pwm,pin=12,func=4
```

This example enables only PWM0 and routes it to gpio12. If both PWM channels need to be enabled you can use `pwm-2chan`.

The overlay does not expose the pwm line on sysfs on boot and needs to be exported by echo'ing the number of the pwm channel to `/sys/class/pwm/pwmchip0/export`:

```
echo 0 > /sys/class/pwm/pwmchip0/export
```

This will create device `/sys/class/pwm/pwmchip0/pwm0` in the filesystem. The easiest way to do this is by adding this to `/etc/rc.local` before the `exit 0` line.

With the sysfs in place, you can now use either the pwm channel(s) by adding the following piece of configuration to your `printer.cfg`:

```
[output_pin caselight]
pin: host:pwmchip0/pwm0
pwm: True
hardware_pwm: True
cycle_time: 0.000001
```

This will add hardware pwm control to gpio12 on the Pi (because the overlay was configured to route pwm0 to pin=12).

PWM0 can be routed to gpio12 and gpio18, PWM1 can be routed to gpio13 and gpio19:

| PWM | gpio PIN | Func |
| --- | --- | --- |
| 0 | 12 | 4 |
| 0 | 18 | 2 |
| 1 | 13 | 4 |
| 1 | 19 | 2 |
