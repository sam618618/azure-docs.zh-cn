<properties
	pageTitle="自动缩放和应用服务环境 | Azure"
	description="自动缩放和应用服务环境"
	services="app-service"
	documentationCenter=""
	authors="btardif"
	manager="wpickett"
	editor=""
/>

<tags
	ms.service="app-service"
	ms.date="08/07/2016"
	wacn.date=""/>

# 自动缩放和应用服务环境

Azure 应用服务环境支持 *自动缩放* 。可以根据指标或计划自动缩放各个辅助池。

![辅助池的自动缩放选项。][intro]

自动缩放可以自动地增长和收缩应用服务环境，使之与预算和/或负载分布相适应，从而优化资源利用情况。

## 配置辅助池自动缩放

可以通过辅助池的“设置”选项卡访问自动缩放功能。

![辅助池的“设置”选项卡。][settings-scale]

该处的界面对用户来说应该相当熟悉，因为在缩放应用服务计划时看到的就是这样的界面。可以手动输入缩放值。

![手动缩放设置。][scale-manual]

也可配置自动缩放配置文件。

![自动缩放设置。][scale-profile]

自动缩放配置文件用于设置缩放限制。这样一来，用户就可以通过设置下限缩放值来获取一致的性能体验 (1)，通过设置上限来确定可预测的支出上限 (2)。

![配置文件中的缩放设置。][scale-profile2]

定义配置文件以后，即可通过添加自动缩放规则来增加或减小辅助池中的实例数，具体范围由配置文件定义。自动缩放规则基于各种指标。

![缩放规则。][scale-rule]

 可以使用任何辅助池或前端指标来定义自动缩放规则。这些指标也是可以在资源边栏选项卡图中监视的指标，或者可以为其设置警报的指标。

## 自动缩放示例

可以通过演示一个方案，对应用服务环境的自动缩放进行适当阐述。

本文介绍在设置自动缩放时的所有必需考虑事项，以及在纳入托管在应用服务环境中的各种自动缩放应用服务环境时需要考虑到的所有有效交互。

### 方案简介

Frank 是企业的系统管理员，他已将所管理的部分工作负荷迁移到应用服务环境。

应用服务环境已配置为手动缩放，如下所示：

* **前端：**3
* **辅助池 1**：10
* **辅助池 2**：5
* **辅助池 3**：5

辅助池 1 用于生产工作负荷，而辅助池 2 和辅助池 3 则用于质量保证 (QA) 和开发工作负荷。

QA 和开发型应用服务计划配置为手动缩放，而生产型应用服务计划则设置为根据负载和流量的变化自动缩放。

Frank 非常熟悉该应用程序。他知道负载高峰时间在上午 9:00 到下午 6:00 之间，因为该应用程序为业务线 (LOB) 应用程序，员工会在办公时使用。高峰时间过后，使用量会下降，此时用户完成了当天的工作。高峰时间之外仍会有一些负载，因为用户可以使用其移动设备或家用电脑远程访问该应用。生产型应用服务计划已配置为按以下规则根据 CPU 使用率自动缩放：

![LOB 应用的特定设置。][asp-scale]

|	**自动缩放配置文件 - 工作日 - 应用服务计划** |	**自动缩放配置文件 - 周末 - 应用服务计划** |
|----------------------------------------------------|----------------------------------------------------|
|	**名称:** 工作日配置文件 |	**名称:** 周末配置文件 |
|	**缩放方式:** 计划和性能规则 |	**缩放方式:** 计划和性能规则 |
|	**配置文件:** 工作日 |	**配置文件:** 周末 |
|	**类型:** 循环 |	**类型:** 循环 |
|	**目标范围:** 5 到 20 个实例 |	**目标范围:** 3 到 10 个实例 |
|	**星期:** 星期一、星期二、星期三、星期四、星期五 |	**星期:** 星期六、星期日 |
|	**开始时间:** 上午 9:00 |	**开始时间:** 上午 9:00 |
|	**时区:** UTC-08 |	**时区:** UTC-08 |
| | |
|	**自动缩放规则(向上缩放)** |	**自动缩放规则(向上缩放)** |
|	**资源:** 生产(应用服务环境) |	**资源:** 生产(应用服务环境) |
|	**指标:** CPU 百分比 |	**指标:** CPU 百分比 |
|	**操作(Operation):** 大于 60% |	**操作(Operation):** 大于 80% |
|	**持续时间:** 5 分钟 |	**持续时间:** 10 分钟 |
|	**时间聚合:** 平均 |	**时间聚合:** 平均 |
|	**操作(Action):** 计数加 2 |	**操作(Action):** 计数加 1 |
|	**冷却(分钟):** 15 |	**冷却(分钟):** 20 |
| | |
 |	**自动缩放规则(向下缩放)** |	**自动缩放规则(向下缩放)** |
|	**资源:** 生产(应用服务环境) |	**资源:** 生产(应用服务环境) |
|	**指标:** CPU 百分比 |	**指标:** CPU 百分比 |
|	**操作(Operation):** 小于 30% |	**操作(Operation):** 小于 20% |
|	**持续时间:** 10 分钟 |	**持续时间:** 15 分钟 |
|	**时间聚合:** 平均 |	**时间聚合:** 平均 |
|	**操作(Action):** 计数减 1 |	**操作(Action):** 计数减 1 |
|	**冷却(分钟):** 20 |	**冷却(分钟):** 10 |

### 应用服务计划膨胀率

配置为自动缩放的应用服务计划在自动缩放时，会以最大比率/小时进行缩放。可以根据自动缩放规则中提供的值计算此比率。

了解和计算 *应用服务计划膨胀率* 对于应用服务环境的自动缩放很重要，因为辅助池的缩放变化不是即时的。

应用服务计划膨胀率计算如下：

![计算应用服务计划膨胀率。][ASP-Inflation]

根据生产型应用服务计划的工作日配置文件的“自动缩放 - 向上缩放”规则，计算如下：

![基于“自动缩放 - 向上缩放”规则的工作日应用服务计划膨胀率。][Equation1]

根据生产型应用服务计划的周末配置文件的“自动缩放 - 向上缩放”规则，计算公式分解如下：

![基于“自动缩放 - 向上缩放”规则的周末应用服务计划膨胀率。][Equation2]

也可以计算向下缩放操作的此值。

根据生产型应用服务计划的工作日配置文件的“自动缩放 - 向下缩放”规则，计算如下：

![基于“自动缩放 - 向下缩放”规则的工作日应用服务计划膨胀率。][Equation3]

根据生产型应用服务计划的周末配置文件的“自动缩放 - 向下缩放”规则，计算公式分解如下：

![基于“自动缩放 - 向下缩放”规则的周末应用服务计划膨胀率。][Equation4]

这意味着，生产型应用服务计划在工作日可以按 8 实例/小时的最大比率增长，在周末按 4 实例/小时的最大比率增长。该计划在工作日可以按 4 实例/小时的最大比率释放实例，在周末按 6 实例/小时的最大比率释放实例。

如果在辅助池中托管了多个应用服务计划，则需根据托管在该辅助池中的所有应用服务计划的膨胀率之和来计算 *总膨胀率* 。

![计算在辅助池中托管的多个应用服务计划的总膨胀率。][ASP-Total-Inflation]

### 使用应用服务计划膨胀率定义辅助池自动缩放规则

如果辅助池所托管的应用服务计划已配置为自动缩放，则需为辅助池分配容量缓冲区。有了缓冲区，即可根据需要通过自动缩放操作来缩放应用服务计划。最小缓冲区取决于计得的应用服务计划总膨胀率。

由于应用服务环境缩放操作应用起来需要时间，因此当缩放操作仍在进行时，所做的任何更改都应考虑到可能会发生的进一步的需求变化。考虑到这种延迟情况的存在，建议用户在每次进行自动缩放操作时，使用计得的应用服务计划总膨胀率作为需要添加的最小实例数。

Frank 可以根据此方面的信息定义以下自动缩放配置文件和规则：

![LOB 示例的自动缩放配置文件规则。][Worker-Pool-Scale]

|	**自动缩放配置文件 - 工作日** |	**自动缩放配置文件 - 周末** |
|----------------------------------------------------|--------------------------------------------|
|	**名称:** 工作日配置文件 |	**名称:** 周末配置文件 |
|	**缩放方式:** 计划和性能规则 |	**缩放方式:** 计划和性能规则 |
|	**配置文件:** 工作日 |	**配置文件:** 周末 |
|	**类型:** 循环 |	**类型:** 循环 |
|	**目标范围:** 13 到 25 个实例 |	**目标范围:** 6 到 15 个实例 |
|	**星期:** 星期一、星期二、星期三、星期四、星期五 |	**星期:** 星期六、星期日 |
|	**开始时间:** 上午 7:00 |	**开始时间:** 上午 9:00 |
|	**时区:** UTC-08 |	**时区:** UTC-08 |
| | |
|	**自动缩放规则(向上缩放)** |	**自动缩放规则(向上缩放)** |
|	**资源:** 辅助池 1 |	**资源:** 辅助池 1 |
|	**指标:** WorkersAvailable |	**指标:** WorkersAvailable |
|	**操作(Operation):** 小于 8 |	**操作(Operation):** 小于 3 |
|	**持续时间:** 20 分钟 |	**持续时间:** 30 分钟 |
|	**时间聚合:** 平均 |	**时间聚合:** 平均 |
|	**操作(Action):** 计数加 8 |	**操作(Action):** 计数加 3 |
|	**冷却(分钟):** 180 |	**冷却(分钟):** 180 |
| | |
|	**自动缩放规则(向下缩放)** |	**自动缩放规则(向下缩放)** |
|	**资源:** 辅助池 1 |	**资源:** 辅助池 1 |
|	**指标:** WorkersAvailable |	**指标:** WorkersAvailable |
|	**操作(Operation):** 大于 8 |	**操作(Operation):** 大于 3 |
|	**持续时间:** 20 分钟 |	**持续时间:** 15 分钟 |
|	**时间聚合:** 平均 |	**时间聚合:** 平均 |
|	**操作(Action):** 计数减 2 |	**操作(Action):** 计数减 3 |
|	**冷却(分钟):** 120 |	**冷却(分钟):** 120 |

在配置文件中定义的目标范围计算如下：在应用服务计划的配置文件中定义的最小实例数 + 缓冲实例数。

最大范围为在辅助池中托管的所有应用服务计划的所有最大范围之和。

向上缩放规则的计数增加幅度应该至少设置为向上缩放时应用服务计划膨胀率的 1 倍。

计数减少幅度可调整为向下缩放时应用服务计划膨胀率的 1/2 倍到 1 倍之间。

### 前端池的自动缩放

前端池的自动缩放规则比辅助池的更简单。首先，应确保在设置度量值和冷却计时器的持续时间时，考虑到按应用服务计划进行的缩放操作不是即时的。

Frank 了解到，此方案的错误率在前端的 CPU 使用率达到 80% 以后会增加。为了避免这种情况，他在设置自动缩放规则时增加了实例数，如下所示：

![前端池的自动缩放设置。][Front-End-Scale]

|	**自动缩放配置文件 - 前端** |
|--------------------------------------------|
|	**名称:** 自动缩放 - 前端 |
|	**缩放方式:** 计划和性能规则 |
|	**配置文件:** 每天 |
|	**类型:** 循环 |
|	**目标范围:** 3 到 10 个实例 |
|	**星期:** 每天 |
|	**开始时间:** 上午 9:00 |
|	**时区:** UTC-08 |
| |
|	**自动缩放规则(向上缩放)** |
|	**资源:** 前端池 |
|	**指标:** CPU 百分比 |
|	**操作(Operation):** 大于 60% |
|	**持续时间:** 20 分钟 |
|	**时间聚合:** 平均 |
|	**操作(Action):** 计数加 3 |
|	**冷却(分钟):** 120 |
| |
|	**自动缩放规则(向下缩放)** |
|	**资源:** 辅助池 1 |
|	**指标:** CPU 百分比 |
|	**操作(Operation):** 小于 30% |
|	**持续时间:** 20 分钟 |
|	**时间聚合:** 平均 |
|	**操作(Action):** 计数减 3 |
|	**冷却(分钟):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

<!---HONumber=Mooncake_0919_2016-->