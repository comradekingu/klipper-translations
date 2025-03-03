# 三角校正

本文将介绍在Klipper中对“三角洲”运动模式的打印机进行自动校准的操作。

三角洲校准包含确定柱限位开关位置（tower endstop positions），柱夹角（ tower angles），三角半径（delta radius）和悬臂长度四个参数（delta arm lengths）。上述参数将用于三角洲打印机的运动控制。然而，由于每个的影响并非显而易见或其影响具有非线性，因此难以通过手工校正。相对而言，软件校正在数分钟的时间后就可以达到极佳的效果。自动校正无需增加探针。

归根到底，三角洲校准依赖于各柱的限位开关精度。如果使用Trinamic 步进电机驱动，则可考虑使用[无限位功能](Endstop_Phase.md)以提高检测的准确度。

## 自动校准 vs 手动校准

Klipper支持使用手动探高或自动z探针的方法进行三角洲参数校准。

市面上众多的三角洲打印机附带了自动Z探针，但这些探针的精度不足。特别对于三角洲，臂长的细微差异会导致效应器倾斜，导致打印效果一塌糊涂。如果使用自动探针，则先进行[探针校准](Probe_Calibrate.md)，然后检查[探针位置漂移](Probe_Calibrate.md#location-bias-check)。如果探针的偏差大于25微米（0.025mm），则应使用手动探高。手动探高只需要数分钟的时间，并能摒弃探针带来的误差。

如果使用的探针是安装在喷嘴的一侧（即，探针对喷嘴存在xy偏移），需注意在进行过三角洲校准之后会使探针校准的结果无效。该类型的探针并不适用于三角洲打印机（因为效应器倾斜会差生探针位置漂移）。如果仍使用上述的探针，则需在三角洲校准校准后再进行一次探针校准。

## 简单三角洲校准

通过DELTA_CALIBRATE 命令可以在Klipper中进行简单三角洲校准。该命令会对热床上的7个不同的位置进行探高，并计算除柱夹角，柱限位位置和三角半径。

要进行校准，需要先提供柱夹角，柱限位位置和三角半径的初始值，这些值应与实际值只有几毫米的偏差。大多三角洲打印机套件会提供这些值，在配置文件中输入这些值作为初始配置，然后按照下述步骤运行DELTA_CALIBRATE 。如果无法找到初始值，则可以通过搜索 三角洲校准 教程，作为测量的依据。

校准期间，打印机需要对热床外的底板进行探高。通常可以通过修改设置中的 `minimum_z_position=-5` 以实现床外探高。（校准后可以在设置中移除该项。）

探高的方式有两种：手动探高(`DELTA_CALIBRATE METHOD=manual`)和自动探高(`DELTA_CALIBRATE`)。手动探高模式下，打印头将会移近热床，然后等候用户使用 ["纸测法"](Bed_Level.md#the-paper-test) 测量特定位置上的喷嘴和热床的之间的偏差值。

进行简单校准，请确保在配置文件中定义了[delta_calibrate]，然后运行工具：

```
G28
DELTA_CALIBRATE METHOD=manual
```

在测量了7个点，并计算得出新三角洲参数后，运行下面的命令来保存参数：

```
SAVE_CONFIG
```

简单校准获得的参数，其准确性可以满足日常打印的需求。对于新打印机，可以尝试打印几个简单模型，确定功能正常。

## 进阶三角洲校准

简单三角洲校准获得的三角洲参数可以很好地修正喷嘴距离之类问题。然而，该模式不能校准X，Y轴上的准确度。而进阶三角洲校准则是针对维度准确性地问题。

校准流程需要打印一个测试模型并用游标卡尺测量模型的尺寸。

在进行进阶三角洲校准时，必须先完成三角洲校准（通过DELTA_CALIBRATE命令）并保存结果（通过SAVE_CONFIG 命令）。

用切片器对[docs/prints/calibrate_size.stl](prints/calibrate_size.stl)的模型进行切片，生成G-Code。速度设置使用低速（例如 40mm/s）。如可能使用刚性塑料（如PLA）。模型的外径为140mm。如果尺寸太大则按照热床大小进行缩小（但应注意X和Y应按比例进行缩放）。如果打印机的热床足够大，也可以放大该模型。大尺寸可以提高测量的准确性，但是模型的床附着对测量准确性影响更大。

打印测试模型并等待其完全冷却。打印机设置和校准模型必须一一对应（即，在打印校准模型后，不要再进行DELTA_CALIBRATE或修改打印机参数，否则模型测量无效），再执行下面的命令。

如可能，在模型黏着在床上进行测量，但若模型脱离热床也没有关系——这步骤只是为了减少模型的弯曲。

首先测量中心柱到印有“A”标志的柱的距离（即中心到A柱方向的距离）。

![delta-a-distance](img/delta-a-distance.jpg)

之后，按照逆时针方向逐一测量中心柱到边沿柱的距离（中心到C标签柱的距离，中心到B标签柱的距离等）。

![delta_cal_e_step1](img/delta_cal_e_step1.png)

以 逗号分隔的浮点数 的形式将数据输入到Klipper：

```
DELTA_ANALYZE CENTER_DISTS=<a_dist>,<far_c_dist>,<b_dist>,<far_a_dist>,<c_dist>,<far_b_dist>
```

数据之间不要插入 空格 。

之后测量A柱和C柱之间的距离。

![delta-ab-distance](img/delta-outer-distance.jpg)

之后测量C柱和B柱，以及B柱和A柱之间的距离等。

![delta_cal_e_step2](img/delta_cal_e_step2.png)

将参数输入到Klipper：

```
DELTA_ANALYZE OUTER_DISTS=<a_to_far_c>,<far_c_to_b>,<b_to_far_a>,<far_a_to_c>,<c_to_far_b>,<far_b_to_a>
```

此时可以拆除床上的模型。最后要测量的是柱子的尺寸。首先测量中心柱在A柱方向上，B柱方向上以及C柱方向上的尺寸。

![delta-a-pillar](img/delta-a-pillar.jpg)

![delta_cal_e_step3](img/delta_cal_e_step3.png)

将结果输入Klipper：

```
DELTA_ANALYZE CENTER_PILLAR_WIDTHS=<a>,<b>,<c>
```

然后侧向外侧柱子的尺寸。首先测量A柱朝向C柱内侧的面之间的尺寸（见下图）。

![delta-ab-pillar](img/delta-outer-pillar.jpg)

之后逆时针逐一测量外则柱子的尺寸（测量C柱朝向B柱内侧的面之间的尺寸，测量B柱朝向A柱内侧的面之间的尺寸等）。

![delta_cal_e_step4](img/delta_cal_e_step4.png)

并将参数输入Klipper：

```
DELTA_ANALYZE OUTER_PILLAR_WIDTHS=<a>,<far_c>,<b>,<far_a>,<c>,<far_b>
```

如果模型经过了缩放，则输入切片时使用的缩放系数：

```
DELTA_ANALYZE SCALE=1.0
```

（输入系数2.0则模型被放大一倍，输入系数0.5则模型被缩小至一半。）

最后输入下列面命令进行三角校准：

```
DELTA_ANALYZE CALIBRATE=extended
```

该命令将进行数分钟的计算。计算将给出三角洲参数（三角半径，柱夹角，限位位置，臂长）。之后使用SAVE_CONFIG 保存并应用结果：

```
SAVE_CONFIG
```

SAVE_CONFIG 会保存计算并更新后的三角参数。之后的DELTA_CALIBRATE 命令也会给予进阶计算的结果。请勿在SAVE_CONFIG 重新输入原始测量数据，因为此时原始测量数据已不会应用至三角参数上。

### 额外要点

* 如果三角洲打印机的尺寸相当准确，则两柱间的距离应该在74mm附近，并各个柱子的尺寸应为9mm（即目标为柱子面向另一柱子的一侧到所向柱子的统一面的距离为65mm）。如果尺寸不准确，则可以通过DELTA_ANALYZE ，基于之前DELTA_CALIBRATE 获得的高度测量数据和距离测量数据生成新的三角参数。
* DELTA_ANALYZE 的结果可能不符合逻辑。比如，它计算出的臂长可能于打印机的实际臂长不符。尽管如此，测试结果表明 DELTA_ANALYZE 总能给出出色的打印结果。推测这是因为计算三角参数的时候将其他未知的影响参数也纳入到计算考虑。例如，臂长的细微差异会使效应器倾斜，而通过调节臂长的计算基准可以补偿这种差异。

## 在三角洲打印机上使用床网

三角洲打印机上也能使用[床网](Bed_Mesh.md)。但是应在开启床网之前对打印机进行三角校准。在未校准的打印机上使用床网会得出奇怪的结果。

注意进行三角校正会使之前获得的床网数据无效。应在进行三角校正后重新运行BED_MESH_CALIBRATE。
