# Configuration Changes

本文档涵盖了软件更新中对配置文件不向后兼容的部分。在升级 Klipper 时，最好也查看一下这份文档。

本文档中的所有日期都是不精确的。

## 变更

20211110: The "NTC 100K beta 3950" temperature sensor is deprecated. This sensor will be removed in the near future. Most users will find the "Generic 3950" temperature sensor more accurate. To continue to use the older (typically less accurate) definition, define a custom [thermistor](Config_Reference.md#thermistor) with `temperature1: 25`, `resistance1: 100000`, and `beta: 3950`.

20211104: The "step pulse duration" option in "make menuconfig" has been removed. A new `step_pulse_duration` setting in the [stepper config section](Config_Reference.md#stepper) should be set for all steppers that need a custom pulse duration.

20211102: Several deprecated features have been removed. The stepper `step_distance` option has been removed (deprecated on 20201222). The `rpi_temperature` sensor alias has been removed (deprecated on 20210219). The mcu `pin_map` option has been removed (deprecated on 20210325). The gcode_macro `default_parameter_<name>` and macro access to command parameters other than via the `params` pseudo-variable has been removed (deprecated on 20210503). The heater `pid_integral_max` option has been removed (deprecated on 20210612).

20210929: Klipper v0.10.0 released.

20210903: The default [`smooth_time`](Config_Reference.md#extruder) for heaters has changed to 1 second (from 2 seconds). For most printers this will result in more stable temperature control.

20210830: The default adxl345 name is now "adxl345". The default CHIP parameter for the `ACCELEROMETER_MEASURE` and `ACCELEROMETER_QUERY` is now also "adxl345".

20210830: The adxl345 ACCELEROMETER_MEASURE command no longer supports a RATE parameter. To alter the query rate, update the printer.cfg file and issue a RESTART command.

20210821: Several config settings in `printer.configfile.settings` will now be reported as lists instead of raw strings. If the actual raw string is desired, use `printer.configfile.config` instead.

20210819: In some cases, a `G28` homing move may end in a position that is nominally outside the valid movement range. In rare situations this may result in confusing "Move out of range" errors after homing. If this occurs, change your start scripts to move the toolhead to a valid position immediately after homing.

20210814: The analog only pseudo-pins on the atmega168 and atmega328 have been renamed from PE0/PE1 to PE2/PE3.

20210720: A controller_fan section now monitors all stepper motors by default (not just the kinematic stepper motors). If the previous behavior is desired, see the `stepper` config option in the [config reference](Config_Reference.md#controller_fan).

20210703: A `samd_sercom` config section must now specify the sercom bus it is configuring via the `sercom` option.

20210612: 加热器(heater)和温度控制风扇(temperature_fan)配置中的`pid_integral_max`选项已被弃用。该选项将在未来被移除。

20210503: The gcode_macro `default_parameter_<name>` config option is deprecated. Use the `params` pseudo-variable to access macro parameters. Other methods for accessing macro parameters will be removed in the near future. Most users can replace a `default_parameter_NAME: VALUE` config option with a line like the following in the start of the macro: ` {% set NAME = params.NAME|default(VALUE)|float %}`. See the [Command Templates
document](Command_Templates.md#macro-parameters) for examples.

20210430: SET_VELOCITY_LIMIT（和M204）命令现在可以设置大于配置文件中指定值的速度(velocity)、加速度(acceleration)和转角离心速度(square_corner_velocity)。

20210325:配置中的`pin_map`选项已被废弃。使用[sample-aliases.cfg](../config/sample-aliases.cfg)文件将现有的引脚名翻译成实际的微控制器引脚名称。配置中的`pin_map` 选项将在未来被移除。

20210313：Klipper对CAN总线通信的微控制器的支持发生了变化。如果使用CAN总线，全部微控制器必须被重新刷写并更新[Klipper CAN 总线配置](CANBUS.md)。

20210310：TMC2660 默认 driver_SFILT 从1 改为 0。

20210227：现在每当启用 UART 或 SPI 模式的 TMC 步进电机驱动活跃时，每秒会查询一次 - 如果无法联系到驱动或如果驱动报告错误，则 Klipper 将过渡到关闭状态。

20210219：`rpi_temperature` 模块已被重新命名为 `temperature_host`。用 `sensor_type: temperature_host` 替换全部的 `sensor_type: rpi_temperature`。温度文件的路径可以在 `sensor_path` 配置变量中指定。名称“`rpi_temperature`”已被废弃，在不久的将来会被删除。

20210201: `TEST_RESONANCES` 命令现在将禁用之前启用的输入整形（并在测试后重新启用）。如果需要覆盖这一行为并保持输入整形在测试时启用，可以在命令中传递一个附加参数 `INPUT_SHAPING=1`。

20210201：如果一个加速度计芯片在printer.cfg的相应 adxl345 分段被赋予了一个名称，`ACCELEROMETER_MEASURE` 命令的输出文件现在会包含它的名称。

20201222：步进配置分段中的`step_distance`设置已被废弃。建议更新配置以使用[`rotrot_distance`](Rotation_Distance.md)设置。对`step_distance`的支持将在不久的将来被移除。

20201218：endstop_phase 模块中的 `endstop_phase` 设置已被 `trigger_phase` 取代。如果使用相位限位模块，则需要转换为 [`rotation_distance`](Rotation_Distance.md)，并通过运行 ENDSTOP_PHASE_CALIBRATE 命令重新校准任何相位限位。

20201218：旋转三角洲和极点打印机现在必须为旋转步进电机指定 `gear_ratio ` ，并且它们可以不再指定 `step_distance ` 参数。有关新 gear_ratio 参数的格式，请参阅 [配置参考](Config_Reference.md#stepper)。

20201213: It is not valid to specify a Z "position_endstop" when using "probe:z_virtual_endstop". An error will now be raised if a Z "position_endstop" is specified with "probe:z_virtual_endstop". Remove the Z "position_endstop" definition to fix the error.

20201120: The `[board_pins]` config section now specifies the mcu name in an explicit `mcu:` parameter. If using board_pins for a secondary mcu, then the config must be updated to specify that name. See the [config reference](Config_Reference.md#board_pins) for further details.

20201112: The time reported by `print_stats.print_duration` has changed. The duration prior to the first detected extrusion is now excluded.

20201029: neopixel `color_order_GRB` 配置已从配置中移除。如果需要，请更新配置文件，将新的`color_order`选项设置为RGB、GRB、RGBW或GRBW。

20201029: mcu配置部分的串行选项不再默认为 /dev/ttyS0 。在极其罕见的情况下，如果 /dev/ttyS0 是所需的串行端口，必须明确指定它。

20201020: Klipper v0.9.0发布。

20200902: The RTD resistance-to-temperature calculation for MAX31865 converters has been corrected to not read low. If you are using such a device, you should recalibrate your print temperature and PID settings.

20200816: G代码宏`printer.gcode`对象已被重命名为`printer.gcode_move`。删除了 `printer.toolhead` 和 `printer.gcode` 中几个没有文档的变量。参见 docs/Command_Templates.md 以获得可用的模板变量列表。

20200816: The gcode macro "action_" system has changed. Replace any calls to `printer.gcode.action_emergency_stop()` with `action_emergency_stop()`, `printer.gcode.action_respond_info()` with `action_respond_info()`, and `printer.gcode.action_respond_error()` with `action_raise_error()`.

20200809: The menu system has been rewritten. If the menu has been customized then it will be necessary to update to the new configuration. See config/example-menu.cfg for configuration details and see klippy/extras/display/menu.cfg for examples.

20200731: The behavior of the `progress` attribute reported by the `virtual_sdcard` printer object has changed. Progress is no longer reset to 0 when a print is paused. It will now always report progress based on the internal file position, or 0 if no file is currently loaded.

20200725: The servo `enable` config parameter and the SET_SERVO `ENABLE` parameter have been removed. Update any macros to use `SET_SERVO SERVO=my_servo WIDTH=0` to disable a servo.

20200608: The LCD display support has changed the name of some internal "glyphs". If a custom display layout was implemented it may be necessary to update to the latest glyph names (see klippy/extras/display/display.cfg for a list of available glyphs).

20200606: The pin names on linux mcu have changed. Pins now have names of the form `gpiochip<chipid>/gpio<gpio>`. For gpiochip0 you can also use a short `gpio<gpio>`. For example, what was previously referred to as `P20` now becomes `gpio20` or `gpiochip0/gpio20`.

20200603: The default 16x4 LCD layout will no longer show the estimated time remaining in a print. (Only the elapsed time will be shown.) If the old behavior is desired one can customize the menu display with that information (see the description of display_data in config/example-extras.cfg for details).

20200531: The default USB vendor/product id is now 0x1d50/0x614e. These new ids are reserved for Klipper (thanks to the openmoko project). This change should not require any config changes, but the new ids may appear in system logs.

20200524: The default value for the tmc5160 pwm_freq field is now zero (instead of one).

20200425: The gcode_macro command template variable `printer.heater` was renamed to `printer.heaters`.

20200313: The default lcd layout for multi-extruder printers with a 16x4 screen has changed. The single extruder screen layout is now the default and it will show the currently active extruder. To use the previous display layout set "display_group: _multiextruder_16x4" in the [display] section of the printer.cfg file.

20200308: The default `__test` menu item was removed. If the config file has a custom menu then be sure to remove all references to this `__test` menu item.

20200308: The menu "deck" and "card" options were removed. To customize the layout of an lcd screen use the new display_data config sections (see config/example-extras.cfg for the details).

20200109: The bed_mesh module now references the probe's location in for the mesh configuration. As such, some configuration options have been renamed to more accurately reflect their intended functionality. For rectangular beds, `min_point` and `max_point` have been renamed to `mesh_min` and `mesh_max` respectively. For round beds, `bed_radius` has been renamed to `mesh_radius`. A new `mesh_origin` option has also been added for round beds. Note that these changes are also incompatible with previously saved mesh profiles. If an incompatible profile is detected it will be ignored and scheduled for removal. The removal process can be completed by issuing the SAVE_CONFIG command. The user will need to re-calibrate each profile.

20191218: The display config section no longer supports "lcd_type: st7567". Use the "uc1701" display type instead - set "lcd_type: uc1701" and change the "rs_pin: some_pin" to "rst_pin: some_pin". It may also be necessary to add a "contrast: 60" config setting.

20191210: The builtin T0, T1, T2, ... commands have been removed. The extruder activate_gcode and deactivate_gcode config options have been removed. If these commands (and scripts) are needed then define individual [gcode_macro T0] style macros that call the ACTIVATE_EXTRUDER command. See the config/sample-idex.cfg and sample-multi-extruder.cfg files for examples.

20191210: Support for the M206 command has been removed. Replace with calls to SET_GCODE_OFFSET. If support for M206 is needed, add a [gcode_macro M206] config section that calls SET_GCODE_OFFSET. (For example "SET_GCODE_OFFSET Z=-{params.Z}".)

20191202: Support for the undocumented "S" parameter of the "G4" command has been removed. Replace any occurrences of S with the standard "P" parameter (the delay specified in milliseconds).

20191126: The USB names have changed on micro-controllers with native USB support. They now use a unique chip id by default (where available). If an "mcu" config section uses a "serial" setting that starts with "/dev/serial/by-id/" then it may be necessary to update the config. Run "ls /dev/serial/by-id/*" in an ssh terminal to determine the new id.

20191121: The pressure_advance_lookahead_time parameter has been removed. See example.cfg for alternate configuration settings.

20191112: The tmc stepper driver virtual enable capability is now automatically enabled if the stepper does not have a dedicated stepper enable pin. Remove references to tmcXXXX:virtual_enable from the config. The ability to control multiple pins in the stepper enable_pin config has been removed. If multiple pins are needed then use a multi_pin config section.

20191107: The primary extruder config section must be specified as "extruder" and may no longer be specified as "extruder0". Gcode command templates that query the extruder status are now accessed via "{printer.extruder}".

20191021: Klipper v0.8.0 released

20191003: The move_to_previous option in [safe_z_homing] now defaults to False. (It was effectively False prior to 20190918.)

20190918: The zhop option in [safe_z_homing] is always re-applied after Z axis homing completed. This might need users to update custom scripts based on this module.

20190806: The SET_NEOPIXEL command has been renamed to SET_LED.

20190726: The mcp4728 digital-to-analog code has changed. The default i2c_address is now 0x60 and the voltage reference is now relative to the mcp4728's internal 2.048 volt reference.

20190710: The z_hop option was removed from the [firmware_retract] config section. The z_hop support was incomplete and could cause incorrect behavior with several common slicers.

20190710: The optional parameters of the PROBE_ACCURACY command have changed. It may be necessary to update any macros or scripts that use that command.

20190628: All configuration options have been removed from the [skew_correction] section. Configuration for skew_correction is now done via the SET_SKEW gcode. See [Skew Correction](Skew_Correction.md) for recommended usage.

20190607: The "variable_X" parameters of gcode_macro (along with the VALUE parameter of SET_GCODE_VARIABLE) are now parsed as Python literals. If a value needs to be assigned a string then wrap the value in quotes so that it is evaluated as a string.

20190606: The "samples", "samples_result", and "sample_retract_dist" config options have been moved to the "probe" config section. These options are no longer supported in the "delta_calibrate", "bed_tilt", "bed_mesh", "screws_tilt_adjust", "z_tilt", or "quad_gantry_level" config sections.

20190528: The magic "status" variable in gcode_macro template evaluation has been renamed to "printer".

20190520: The SET_GCODE_OFFSET command has changed; update any g-code macros accordingly. The command will no longer apply the requested offset to the next G1 command. The old behavior may be approximated by using the new "MOVE=1" parameter.

20190404: The Python host software packages were updated. Users will need to rerun the ~/klipper/scripts/install-octopi.sh script (or otherwise upgrade the python dependencies if not using a standard OctoPi installation).

20190404: The i2c_bus and spi_bus parameters (in various config sections) now take a bus name instead of a number.

20190404: The sx1509 config parameters have changed. The 'address' parameter is now 'i2c_address' and it must be specified as a decimal number. Where 0x3E was previously used, specify 62.

20190328: The min_speed value in [temperature_fan] config will now be respected and the fan will always run at this speed or higher in PID mode.

20190322: The default value for "driver_HEND" in [tmc2660] config sections was changed from 6 to 3. The "driver_VSENSE" field was removed (it is now automatically calculated from run_current).

20190310: The [controller_fan] config section now always takes a name (such as [controller_fan my_controller_fan]).

20190308: The "driver_BLANK_TIME_SELECT" field in [tmc2130] and [tmc2208] config sections has been renamed to "driver_TBL".

20190308: The [tmc2660] config section has changed. A new sense_resistor config parameter must now be provided. The meaning of several of the driver_XXX parameters has changed.

20190228: Users of SPI or I2C on SAMD21 boards must now specify the bus pins via a [samd_sercom] config section.

20190224: The bed_shape option has been removed from bed_mesh. The radius option has been renamed to bed_radius. Users with round beds should supply the bed_radius and round_probe_count options.

20190107: The i2c_address parameter in the mcp4451 config section changed. This is a common setting on Smoothieboards. The new value is half the old value (88 should be changed to 44, and 90 should be changed to 45).

20181220: Klipper v0.7.0 released
