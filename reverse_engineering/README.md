# 信号线逆向工程实验
> 记录下来说是便于后人进一步研究。

- [信号线逆向工程实验](#信号线逆向工程实验)
  - [实验目的](#实验目的)
  - [实验器材](#实验器材)
  - [实验步骤](#实验步骤)
  - [实验动作设计](#实验动作设计)
  - [实验结果](#实验结果)
    - [信号线电平](#信号线电平)
    - [逻辑分析仪数据](#逻辑分析仪数据)
  - [实验数据分析](#实验数据分析)
    - [硬件分析](#硬件分析)
    - [信号线的基本功能](#信号线的基本功能)
    - [确定数据传输线的通讯协议](#确定数据传输线的通讯协议)
    - [确定数据传输和开门的流程](#确定数据传输和开门的流程)
    - [确定不同刷卡动作下的数据传输线的具体内容](#确定不同刷卡动作下的数据传输线的具体内容)


本篇文章为逆向南科大二期宿舍门锁的过程记录，因为整个逆向的过程类似于实验，这里用实验的方式来指导动作和记录结果。

在阅读本文之前，你需要知道以下内容：
1. **二期宿舍门锁的基本功能**
TODO
2. **二期宿舍门锁的基本结构**
TODO
3. **Freedorm桥接板的基本功能**
TODO
4. **逻辑分析仪的基本功能**
TODO

## 实验目的

1. **了解信号线的基本功能**
2. **确定数据传输线的通讯协议**
3. **确定数据传输和开门的流程**
4. **确定不同刷卡动作下的数据传输线的具体内容**

## 实验器材
 1. **二期宿舍门锁**（研究对象）
 2. **Freedorm桥接板**（用来“中间人”截取信号）
 3. **逻辑分析仪**（用来获取分析信号）
 4. **数据线**（用来连接逻辑分析仪和上位机电脑）
 5. **杜邦线**（用来模拟短接调试线）
 6. **装有Pulseview的电脑**（Pulseview是一个开源的逻辑分析仪上位机软件，用来分析信号）
 7. **对应的门禁卡**（用来执行刷卡成功动作）
 8. **空白门禁卡**（用来执行刷卡失败动作）
 9. **(optional) 宿管卡**（用来给逻辑分析仪供电）

最后安装好的实验器材如下图所示：
![实验器材](/reverse_engineering/README/setup.jpg)

## 实验步骤
1. 安装桥接版，具体步骤见[Freedorm安装教程](/TODO)
2. 测量信号线电平并记录
3. 连接逻辑分析仪和电脑
4. 开始录制信号
5. 执行实验动作
6. 按照命名规则保存数据

## 实验动作设计
针对每个实验目的，设计对应的实验动作，如下：

## 实验结果
### 信号线电平

通过测量对GND电平，得到下表：
|      | D1  | D0  | LED | LOCK |
| ---- | --- | --- | --- | ---- |
| 颜色 | 白  | 绿  | 橙  | 黄   |
| 电平 | 5V  | 5V  | 3V3 | 3V3  |

 

### 逻辑分析仪数据

通过执行刷卡动作，逻辑分析仪得到[一些数据]()，命名规则为`data_刷卡动作_对应反馈`。

刷卡动作有（比较感兴趣的进行了加粗）：
1. **`admin`：宿管卡**
2. **`user`：本宿舍住户校园卡**
3. **`stranger`：陌生人校园卡**
4. **`touch_num`：点击门锁面板密码键盘数字键**
5. **`touch_ok`：点击门锁面板密码键盘确认键**
6. `blank`：空白UFUID-IC卡
7. `miband9`：小米手环9模拟IC卡
8. **`D1`: 短接D1和GND**
9. **`D0`: 短接D0和GND**
10. **`LED`: 短接LED和GND**
11. **`LOCK`: 短接LOCK和GND**
12. **`D1&user`: 短接D1和GND，刷本宿舍住户校园卡**
13. `D0&user`: 短接D0和GND，刷本宿舍住户校园卡
14. `LED&user`: 短接LED和GND，刷本宿舍住户校园卡
15. `LOCK&user`: 短接LOCK和GND，刷本宿舍住户校园卡
16. **`D1&admin`: 短接D1和GND，刷宿管卡**
17. **`D0&admin`: 短接D0和GND，刷宿管卡**

对应反馈有：
1. `success`：门打开
2. `fail`：门不打开
3. `blank`：没有反馈，门不打开
4. `locked`：门锁上了，刷卡刷不开

#### 刷卡动作-反馈类型对应表

通过实验，得到每种刷卡动作的对应反馈表，如下表所示：
| 刷卡动作      | 门锁           | 面板LED                  | 蜂鸣器   | 反馈类型 |
| ------------- | -------------- | ------------------------ | -------- | -------- |
| **admin**     | 开，6s后锁上   | 持续6s绿色               | 长滴一声 | success  |
| **user**      | 开，6s后锁上   | 持续6s绿色               | 长滴一声 | success  |
| **stranger**  | 不开           | 红色闪烁3s               | 长滴一声 | fail     |
| **touch_num** | 不开           | 无反应                   | 短滴一声 | blank    |
| **touch_ok**  | 不开           | 绿色闪烁5s               | 默       | fail     |
| blank         | 不开           | 无反应                   | 默       | blank    |
| miband9       | 不开           | 无反应                   | 默       | blank    |
| D1            | 不开           | 保持蓝色                 | 默       | blank    |
| D0            | 不开           | 保持蓝色                 | 默       | blank    |
| LED           | 不开           | 蓝色变成绿色，并保持绿色 | 默       | blank    |
| **LOCK**      | **常开**       | 保持蓝色                 | 默       | success  |
| **D1&user**   | **不开**       | 绿色闪烁一下，1s         | 长滴一声 | locked   |
| **D0&user**   | **不开**       | 绿色闪烁一下，1s         | 长滴一声 | lockde   |
| LED&user      | 开，6s后锁上   | 持续6s绿色               | 长滴一声 | blank    |
| LOCK&user     | 本来就是常开的 | 持续6s绿色               | 长滴一声 | success  |
| **D1&admin**  |                |                          |          |          |
| **D0&admin**  |                |                          |          |          |

有太多种可能的刷卡动作，因此选取了感兴趣的11个刷卡动作，用逻辑分析仪获取数据。
每个数据对应一个动作；有4个通道，对应D1、D0、LED、LOCK。储存在[data文件夹下](/reverse_engineering/data)。

## 实验数据分析
### 硬件分析
在真正开始逆向之前，我们需要了解硬件的基本情况，我们来看看用了什么硬件。
### 信号线的基本功能

首先查询制造商[智慧赛宁](http://www.szsnk.com/)的资料，也没有翻到相关的资料，只能自己动手了。

![智慧赛宁logo](/reverse_engineering/README/snk_logo.jpg)

这一部分通过pcb板子上的丝印已经大概了解，8pin的ph2.0排线中，有两根线被剪断，分别是BUZ和SENSE，剩下的6根线分别是：GND、D1、D0、LED、LOCK、12V。

![dorm_pcb_silkprint](/reverse_engineering/README/pin_define.jpg)
![dorm_pcb_silkprint](/reverse_engineering/README/ph2_cable.jpg)

暂时不浪费时间在没用到的线（BUZ和SENCE）上和完全已知（12V和GND）的线上，我们只关注D1、D0、LED、LOCK这四根线。

通过查看按门板上数字的逻辑分析仪的数据 [data_touch_num_1234567890#_blank](reverse_engineering/data/data_touch_num_1234567890#_blank.sr)，这个数据是我分别单独按了`1`、`2`、`3`、`4`、`5`、`6`、`7`、`8`、`9`、`0`、`#`这11个数字键（没有按确认键`*`）

得到波形和对应数字二进制编码如下：

**数字键`1` `0001`：**
> ![num_1](/reverse_engineering/README/num_1.png)

**数字键`2` `0010`：**
> ![num_2](/reverse_engineering/README/num_2.png)

**数字键`3` `0011`：**
> ![num_3](/reverse_engineering/README/num_3.png)

**数字键`4` `0100`：**
> ![num_4](/reverse_engineering/README/num_4.png)

如果你对二进制足够熟悉，那应该能看到这些数字都是从左到右（大端序）四位二进制编码

#### 按键对应编码表
经过多次实验和统计，门板上的数字键的二进制编码如下：
| 门板按键          | 数字 | 二进制编码 |
| ----------------- | ---- | ---------- |
| 1                 | 1    | 0001       |
| 2                 | 2    | 0010       |
| 3                 | 3    | 0011       |
| 4                 | 4    | 0100       |
| 5                 | 5    | 0101       |
| 6                 | 6    | 0110       |
| 7                 | 7    | 0111       |
| 8                 | 8    | 1000       |
| 9                 | 9    | 1001       |
| 0                 | 0    | 0000       |
| *（确定键）       | 10   | 1011       |
| #  （不清楚功能） | 11   | 1010       |

所以D1和D0的功能确定了，D1是数据传输线，比特1；D0是数据传输线，比特0。

接下来我们来看看LED和LOCK这两根线的功能。

我们可以观察刷卡成功和失败的数据（data_user2_success.sr data_stranger_fail.sr）：

![data_user2_success](/reverse_engineering/README/data_user2_success.png)
回顾上面的[刷卡动作-反馈类型对应表](#刷卡动作-反馈类型对应表)，刷卡成功后，LED保持绿色6s，门锁打开，6s后锁上。
于是初步猜测LED是控制门锁面板指示灯的。LOCK是控制门锁电机状态的，低电平打开门。
![data_stranger_fail](/reverse_engineering/README/data_stranger_fail.png)
同样回顾上面的[刷卡动作-反馈类型对应表](#刷卡动作-反馈类型对应表)，刷卡失败后，LED闪烁红色3s，门锁不打开，发出长滴一声。对应LED振荡3次，LOCK保持高电平。

所以我们可以初步确定LED和LOCK的功能，LED是控制门锁面板指示灯，LOCK是控制门锁电机状态，低电平门锁打开。

但是似乎LED还有更细节的功能，比如为什么有时候是低电平是绿色，有时候是红色呢？时间有限，我不去深究了。

#### 信号线功能表
总结来说，假如GND和12V这两根线完全就是提供电源，6根线的功能如下：
| pcb丝印 | 信号线颜色 | 功能               |
| ------- | ---------- | ------------------ |
| D1      | 白         | 数据传输线，比特1  |
| D0      | 绿         | 数据传输线，比特0  |
| LED     | 橙         | 控制门锁面板指示灯 |
| LOCK    | 黄         | 控制门锁电机状态   |
| GND     | 黑         | 接地               |
| 12V     | 红         | 12V电源            |

### 确定数据传输线的通讯协议
正如[上面](#按键对应编码表) 所说，D1和D0分别是数据传输线，比特1和比特0。那么我们可以初步确定这是一种魔改的二进制通讯协议。

没有时钟信号，单次脉冲宽度200~250us，脉冲见间隔2150us，D1和D0不会同时出现脉冲，D1代表二进制1，D0代表二进制0，按照大端序（先出现的是MSB-最高有效位）的顺序传输二进制数据。

GPT说这不是一个标准的有规范的通讯协议，而是一种自定义的通讯协议，Freedorm项目的产品定义不需要用到这个通讯协议，所以这里我就不深究了。

### 确定数据传输和开门的流程

在确定开门流程的细节之前，需要先了解一下门锁的工作架构。


#### 门锁物理架构
经过我的观察，门锁的物理架构如下：

1. 连接门锁的8pin ph2.0排线穿过门体内部，……
![cable.jpg](/reverse_engineering/README/cable.jpg)
2. 通过一个弹簧铠甲管穿过进入墙体，……
![cable_case.jpg](/reverse_engineering/README/cable_case.jpg)
![cable_hole.jpg](/reverse_engineering/README/cable_hole.jpg)
3. 从墙体里出来，进入黑色理线管，……
![door.jpg](/reverse_engineering/README/door.jpg)
4. 黑色理线管到走廊上方理线槽，……
![wire_duct.jpg](/reverse_engineering/README/wire_duct.jpg)
5. 理线槽最终走到每层楼的弱电井。
![elv_riser.jpg](/reverse_engineering/README/elv_riser.jpg)

#### 门锁逻辑架构
以下都是我的猜测，没有找到任何资料交叉验证，可能有部分错误，抛砖引玉，仅供参考。

门锁模块本身是一个独立的模块，电源由外部12v供应，硬件主要包含NFC验证电路、电机，还有指示灯。

但是门锁模块本身不负责具体的住户身份校验，只负责执行开门和关门的操作。

具体的住户身份校验是由弱电井中的门禁控制器负责的，门禁控制器负责接收门锁模块的数据传输线（D1、D0）的数据，校验身份，然后通过LED和LOCK控制门锁模块的指示灯和电机。

然后门禁控制器接入网络，可以远程更新住户信息，也可以远程开门。

**一个完整的开门流程如下：**

1. 住户刷卡
2. 门锁模块通过NFC读取信息，将信息通过D1、D0传输给门禁控制器
3. 门禁控制器校验信息，如果通过，通过LED和LOCK控制门锁模块的指示灯和电机
4. 门锁模块执行开门操作

[![](https://mermaid.ink/img/pako:eNqVkk1Lw0AQhv9K2FMFBb3mIEirF6uC3iSXJVnbYJqt-TiICM2pUVoLfhXFggU_iqCloC3UaH-M2U1y8i84GiulLaJ7mlneZ_ad2dlBMlUIEpFJtmyiyySl4oyBc5IuwMljw1JlNY91S1glpqoQCLAp-C8V7nZGNWkqby5RxdbIpyqqNqJjhzfqrFZNsIde6JbhamIUm5NlYppJqlsG1TRifMPBjcMPbpnbZmeNBPNawfGT3z0BPq7wY2hqdnbgZVFgboeV6--eu7yQfPf2YvWAtWF92HxmlVNAgr2i36tzpzkWGXYpClHhPOwVUzNvBSc17XuX4evRID_S1vgq_LIe3ZV-B4ctx0z8PjSank-Bh_RKchHKsErnL13Ho-WlYnDVDZwmOyzBfPlFdyzYnzUM1yvA1_zPJ7tqhY_X4NPv1XjJgaWIqvfBfpsXnC-naBLliJHDqgKLuPNZW0JWluSIhEQIFbKBbc2SkKTvghTbFl3b1mUkWoZNJpFB7UwWiRtYMyGz8wq2-lv8cwtrtk5pP9_9AJF1StY?type=png)](https://mermaid.live/edit#pako:eNqVkk1Lw0AQhv9K2FMFBb3mIEirF6uC3iSXJVnbYJqt-TiICM2pUVoLfhXFggU_iqCloC3UaH-M2U1y8i84GiulLaJ7mlneZ_ad2dlBMlUIEpFJtmyiyySl4oyBc5IuwMljw1JlNY91S1glpqoQCLAp-C8V7nZGNWkqby5RxdbIpyqqNqJjhzfqrFZNsIde6JbhamIUm5NlYppJqlsG1TRifMPBjcMPbpnbZmeNBPNawfGT3z0BPq7wY2hqdnbgZVFgboeV6--eu7yQfPf2YvWAtWF92HxmlVNAgr2i36tzpzkWGXYpClHhPOwVUzNvBSc17XuX4evRID_S1vgq_LIe3ZV-B4ctx0z8PjSank-Bh_RKchHKsErnL13Ho-WlYnDVDZwmOyzBfPlFdyzYnzUM1yvA1_zPJ7tqhY_X4NPv1XjJgaWIqvfBfpsXnC-naBLliJHDqgKLuPNZW0JWluSIhEQIFbKBbc2SkKTvghTbFl3b1mUkWoZNJpFB7UwWiRtYMyGz8wq2-lv8cwtrtk5pP9_9AJF1StY)

对应的逻辑分析仪数据为[data_user2_success.sr](reverse_engineering/data/data_user2_success.sr)：

![data_user2_success](/reverse_engineering/README/data_user2_success.png)

通过分析多次刷卡数据[data_user_6_success.sr](reverse_engineering/data/data_user_6_success.sr)，每一次刷卡成功的数据传输都是一样的，34bit，所以我们可以初步确定这是在传输身份信息。

更进一步，分析了宿舍三位室友的刷卡数据，其中两位的验证信息只有一位不同，正好两位室友都没有补过卡，所以这两张卡都是开学一起被办理的，点到为止。



### 确定不同刷卡动作下的数据传输线的具体内容

由于此次逆向的背景是Freedorm项目，目前为止已经得到想要的东西了，这部分留给对此更加感兴趣的人。