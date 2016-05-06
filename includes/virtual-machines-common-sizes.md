
若要查看 Azure VM 的一般限制，请参阅 [Azure subscription and service limits, quotas, and constraints](/documentation/articles/azure-subscription-service-limits)（Azure 订阅和服务限制、配额和约束）。

标准大小包括多个系列：A、D、DS、G 和 GS。其中某些大小的注意事项包括：

*   D 系列的 VM 旨在运行需要更高计算能力和临时磁盘性能的应用程序。D 系列 VM 为临时磁盘提供更快的处理器、更高的内存内核比和固态驱动器 (SSD)。有关详细信息，请参阅 Azure 博客[新的 D 系列虚拟机大小](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)上的公告。

*   Dv2 系列是原 D 系列的后续系列，其特点是 CPU 功能更强大。Dv2 系列 CPU 比 D 系列 CPU 快大约 35%。该系列基于最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 处理器，通过 Intel Turbo Boost Technology 2.0 可以达到 3.2 GHz。Dv2 系列的内存和磁盘配置与 D 系列相同。

*   G 系列的 VM 可提供最大的内存，并在配备 Intel Xeon E5 V3 系列处理器的主机上运行。


*   DS 系列、DSv2 系列和 GS 系列的 VM 可使用高级存储，从而为 I/O 密集型工作负荷提供高性能、低延迟的存储。这些 VM 使用固态硬盘 (SSD) 托管虚拟机的磁盘，而且还提供本地 SSD 磁盘高速缓存。高级存储只在某些区域可用。有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](/documentation/articles/storage-premium-storage)。


*   A 系列 VM 可以部署在各种不同的硬件类型和处理器上。根据硬件限制大小，为运行中的实例提供一致的处理器性能，不论硬件部署的位置。若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。

*   A0 大小在物理硬件上过度订阅。仅针对此特定大小，其他客户部署可能影响正在运行的工作负荷的性能。以下概述的相对性能为预期的基准，受限于近似变化性的 15%。


虚拟机的大小会影响定价。大小还会影响虚拟机的处理、内存和存储容量。存储成本是基于存储帐户中的已使用页数进行单独计算。有关详细信息，请参阅[虚拟机定价详细信息](/home/features/virtual-machines/#price)和 [Azure 存储定价](/home/features/storage/#price)。


以下注意事项可能会帮助你决定大小：


* A8-A11 大小也称为“计算密集型实例”。运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。有关使用这些大小的详细信息和注意事项，请参阅 [About the A8, A9, A10, and A11 compute intensive instances（关于 A8、A9、A10 和 A11 计算密集型实例）](/documentation/articles/virtual-machines-windows-a8-a9-a10-a11-specs)。


*	Dv2 系列、D 系列、G 系列以及对应的 DS/GS 系列是请求更快速的 CPU、更好的本地磁盘性能，或有更高内存要求之应用程序的最佳选择。它们为许多企业级应用程序提供强大的组合。

*   Azure 数据中心内的一些物理主机可能不支持更大的虚拟机大小，例如 A5 - A11。因此，在以下情况下，可能会显示错误消息**<machine name>**“未能配置虚拟机”或**<machine name>**“未能创建虚拟机”：将现有虚拟机的大小调整为新的大小时；在 2013 年 4 月 16 日之前创建的虚拟网络中创建新的虚拟机时；或者向现有的云服务中添加新的虚拟机时。有关每个部署方案的解决方法，请参阅支持论坛上的 [Error: "Failed to configure virtual machine"（错误：“无法配置虚拟机”）](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows)。


## 性能注意事项

我们已创建 Azure 计算单元 (ACU) 的概念，以提供一种比较 Azure SKU 中的计算 (CPU) 性能的方法。这可帮助你轻松确定哪个 SKU 最有可能满足你的性能需要。ACU 当前已在小型 (Standard\_A1) VM 上标准化为 100，而所有其他 SKU 则表示 SKU 在运行标准基准测试时大约可以快多少。

>[AZURE.IMPORTANT] ACU 仅是准则。你的工作负荷的结果可能会有所不同。

<br>

|SKU 系列 |ACU/核心 |
|---|---|
|[Standard\_A0](#standard-tier-a-series) |50 |
|[Standard\_A1-4](#standard-tier-a-series) |100 |
|[Standard\_A5-7](#standard-tier-a-series) |100 |
|[A8-A11](#standard-tier-a-series) |225 *|
|[D1-14](#standard-tier-d-series) |160 |
|[D1-15v2](#standard-tier-dv2-series) |210 - 250 *|
|[DS1-14](#standard-tier-ds-series) |160 |
|[DS1-14v2](#standard-tier-dsv2-series) |210-250* |
|[G1-5](#standard-tier-g-series) |180 - 240 *|
|[GS1-5](#standard-tier-gs-series) |180 - 240 *|


ACU 标有 *使用 Intel® Turbo 技术来增加 CPU 频率，并提升性能。提升量可能因 VM 大小、工作负荷和同一主机上运行的其他工作负荷而有所不同。

## 大小表

下表显示这些虚拟机提供的大小和容量。

* 存储容器使用 1024^3 字节数（GB 的计量单位）表示。这有时也称为 GiB，即二进制定义。在比较使用不同进制系统的大小时，请记住二进制大小可能看上去小于十进制大小，但对于任何具体大小（例如 1 GB），二进制系统提供的容量多于十进制系统，因为 1024^3 大于 1000^3。

* 最大网络带宽是每个 VM 类型分配并分配的最大聚合带宽。最大带宽提供选择正确 VM 类型的指导原则，以确保有适当的网络容量可用。在低、中、高和极高之间切换时，吞吐量将随之增加。实际的网络性能将取决于许多因素，包括网络和应用程序负载，以及应用程序的网络设置。


## 标准层：A 系列

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）| 最大网络带宽 |
|---|---|---|---|---|---|---|---|
|Standard\_A0 |1|768 MB|1| 临时磁盘 = 20 GB |1|1x500| 低 |
|Standard\_A1 |1|1\.75 GB|1|临时磁盘 = 70 GB |2|2x500| 中 |
|Standard\_A2 |2|3\.5 GB|1|临时磁盘 = 135 GB |4|4x500| 中 |
|Standard\_A3 |4|7 GB|2|临时磁盘 = 285 GB |8|8x500| 高 |
|Standard\_A4 |8|14 GB|4|临时磁盘 = 605 GB |16|16x500| 高 |
|Standard\_A5 |2|14 GB|1|临时磁盘 = 135 GB |4|4X500| 中 |
|Standard\_A6 |4|28 GB|2|临时磁盘 = 285 GB |8|8x500| 高 |
|Standard\_A7 |8|56 GB|4|临时磁盘 = 605 GB |16|16x500| 高 |



## 标准层：A 系列 - 计算密集型实例

注意：有关使用这些大小的信息和注意事项，请参阅 [About the A8, A9, A10, and A11 compute intensive instances（关于 A8、A9、A10 和 A11 计算密集型实例）](/documentation/articles/virtual-machines-windows-a8-a9-a10-a11-specs)。

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）| 最大网络带宽 |
|---|---|---|---|---|---|---|---|
|Standard\_A8|8|56 GB|2| 临时 = 382 GB |16|16x500| 高 |
|Standard\_A9|16|112 GB|4| 临时 = 382 GB |16|16x500| 很高 |
|Standard\_A10|8|56 GB|2| 临时 = 382 GB |16|16x500| 高 |
|Standard\_A11|16|112 GB|4| 临时 = 382 GB |16|16x500| 很高 |

## 标准层：D 系列

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）| 最大网络带宽 |
|---|---|---|---|---|---|---|---|
|Standard\_D1 |1|3\.5 GB|1|临时磁盘 (SSD) = 50 GB |2|2x500| 中 |
|Standard\_D2 |2|7 GB|2|临时磁盘 (SSD) = 100 GB |4|4x500| 高 |
|Standard\_D3 |4|14 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500| 高 |
|Standard\_D4 |8|28 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500| 高 |
|Standard\_D11 |2|14 GB|2|临时磁盘 (SSD) = 100 GB |4|4x500| 高 |
|Standard\_D12 |4|28 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500| 高 |
|Standard\_D13 |8|56 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500| 高 |
|Standard\_D14 |16|112 GB|8|临时磁盘 (SSD) = 800 GB |32|32x500| 很高 |


## 标准层：Dv2 系列

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）| 最大网络带宽 |
|---|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3\.5 GB|1|临时磁盘 (SSD) = 50 GB |2|2x500| 中 |
|Standard\_D2\_v2 |2|7 GB|2|临时磁盘 (SSD) = 100 GB |4|4x500| 高 |
|Standard\_D3\_v2 |4|14 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500| 高 |
|Standard\_D4\_v2 |8|28 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500| 高 |
|Standard\_D5\_v2 |16|56 GB|8|临时磁盘 (SSD) = 800 GB |32|32x500| 很高 |
|Standard\_D11\_v2 |2|14 GB|2|临时磁盘 (SSD) = 100 GB |4|4x500| 高 |
|Standard\_D12\_v2 |4|28 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500| 高 |
|Standard\_D13\_v2 |8|56 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500| 高 |
|Standard\_D14\_v2 |16|112 GB|8|临时磁盘 (SSD) = 800 GB |32|32x500| 很高 |
|Standard\_D15\_v2 |20|140 GB|10|临时 (SSD) = 1 TB |40|40x500| 很高 |


## 标准层：DS 系列*

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|高速缓存大小 (GB)|最大磁盘 IOPS 和带宽| 最大网络带宽 |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3\.5|1|本地 SSD 磁盘 = 7 GB |2|43| 3,200 每秒 32 MB | 中 |
|Standard\_DS2 |2|7|2|本地 SSD 磁盘 = 14 GB |4|86| 6,400 每秒 64 MB | 高 |
|Standard\_DS3 |4|14|4|本地 SSD 磁盘 = 28 GB |8|172| 12,800 每秒 128 MB | 高 |
|Standard\_DS4 |8|28|8|本地 SSD 磁盘 = 56 GB |16|344| 25,600 每秒 256 MB | 高 |
|Standard\_DS11 |2|14|2|本地 SSD 磁盘 = 28 GB |4|72| 6,400 每秒 64 MB | 高 |
|Standard\_DS12 |4|28|4|本地 SSD 磁盘 = 56 GB |8|144| 12,800 每秒 128 MB | 高 |
|Standard\_DS13 |8|56|8|本地 SSD 磁盘 = 112 GB |16|288| 25,600 每秒 256 MB | 高 |
|Standard\_DS14 |16|112|8|本地 SSD 磁盘 = 224 GB |32|576| 50,000 每秒 512 MB | 很高 |

**DS 系列 VM 可能的最大每秒输入/输出操作次数 (IOPS) 和吞吐量（带宽）受磁盘大小影响。有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](/documentation/articles/storage-premium-storage)。


## 标准层：DSv2 系列*

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|高速缓存大小 (GB)|最大磁盘 IOPS 和带宽| 最大网络带宽 |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1\_v2 |1|3\.5|1|本地 SSD 磁盘 = 7 GB |2|43| 3,200，每秒 48 MB | 中 |
|Standard\_DS2\_v2 |2|7|2|本地 SSD 磁盘 = 14 GB |4|86| 6,400，每秒 96 MB | 高 |
|Standard\_DS3\_v2 |4|14|4|本地 SSD 磁盘 = 28 GB |8|172| 12,800，每秒 192 MB | 高 |
|Standard\_DS4\_v2 |8|28|8|本地 SSD 磁盘 = 56 GB |16|344| 25,600，每秒 384 MB | 高 |
|Standard\_DS5\_v2 |16|56|8|本地 SSD 磁盘 = 112 GB |32|688| 50,000，每秒 768 MB | 高 |
|Standard\_DS11\_v2 |2|14|2|本地 SSD 磁盘 = 28 GB |4|72| 6,400，每秒 96 MB | 高 |
|Standard\_DS12\_v2 |4|28|4|本地 SSD 磁盘 = 56 GB |8|144| 12,800，每秒 192 MB | 高 |
|Standard\_DS13\_v2 |8|56|8|本地 SSD 磁盘 = 112 GB |16|288| 25,600，每秒 384 MB | 高 |
|Standard\_DS14\_v2 |16|112|8|本地 SSD 磁盘 = 224 GB |32|576| 50,000，每秒 768 MB | 很高 |


**DS 系列 VM 可能的最大每秒输入/输出操作次数 (IOPS) 和吞吐量（带宽）受磁盘大小影响。有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](/documentation/articles/storage-premium-storage)。


## 标准层：G 系列*

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）| 最大网络带宽 |
|---|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|本地 SSD 磁盘 = 384 GB |4|4 x 500| 高 |
|Standard\_G2 |4|56 GB|2|本地 SSD 磁盘 = 768 GB |8|8 x 500| 高 |
|Standard\_G3 |8|112 GB|4|本地 SSD 磁盘 = 1,536 GB |16|16 x 500| 很高 | 
|Standard\_G4 |16|224 GB|8|本地 SSD 磁盘 = 3,072 GB |32|32 x 500| 极高 |
|Standard\_G5 |32|448 GB|8|本地 SSD 磁盘 = 6,144 GB |64| 64 x 500 | 极高 |

## 标准层级：GS 系列

|大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|高速缓存大小 (GB)|最大磁盘 IOPS 和带宽| 最大网络带宽 |
|---|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|本地 SSD 磁盘 = 56 GB |4|264| 5,000，每秒 125 MB | 高 |
|Standard\_GS2|4|56|2|本地 SSD 磁盘 = 112 GB |8|528| 10,000，每秒 250 MB | 高 | 
|Standard\_GS3|8|112|4|本地 SSD 磁盘 = 224 GB |16|1056| 20,000，每秒 500 MB | 很高 |
|Standard\_GS4|16|224|8|本地 SSD 磁盘 = 448 GB |32|2112| 40,000，每秒 1,000 MB | 极高 |
|Standard\_GS5|32|448|8|本地 SSD 磁盘 = 896 GB |64|4224| 80,000，每秒 2,000 MB | 极高 |

## 注意：使用 CLI 和 Powershell 的标准 A0 - A4 


在经典部署模型中，某些 VM 大小名称与 CLI 和 PowerShell 中稍有不同：

* Standard\_A0 是特小型 
* Standard\_A1 是小型
* Standard\_A2 是中型
* Standard\_A3 是大型
* Standard\_A4 是超大型


## 后续步骤

- 了解 [Azure 订阅和服务限制、配额和约束](/documentation/articles/azure-subscription-service-limits)。
- 了解高性能计算 (HPC) 等工作负荷的 [A8、A9、A10 和 A11 计算密集型实例](/documentation/articles/virtual-machines-windows-a8-a9-a10-a11-specs)。




<!---HONumber=Mooncake_0425_2016-->