---
title: Azure AD Connect：从 DirSync 升级 | Microsoft Docs
description: 了解如何从 DirSync 升级到 Azure AD Connect。本文介绍从 DirSync 升级到 Azure AD Connect 的步骤。
services: active-directory
documentationcenter: ''
author: andkjell
manager: stevenpo
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: andkjell;shoatman;billmath

---
# Azure AD Connect：从 DirSync 升级
Azure AD Connect 是 DirSync 的后继产品。你将在本主题中了解可从 DirSync 升级的方式。这些步骤不适用于从另一个版本的 Azure AD Connect 或从 Azure AD Sync 升级。

在开始安装 Azure AD Connect 之前，请确保[下载 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) 并完成 [Azure AD Connect：硬件和先决条件](active-directory-aadconnect-prerequisites.md)中的预先准备步骤。特别是，请阅读以下内容，因为其中描述了与 DirSync 不同的方面：

* 所需的 .Net 和 PowerShell 版本。服务器上所需要的版本比 DirSync 需要的版本新。
* 代理服务器配置。如果使用代理服务器连接 Internet，则该设置必须在升级之前配置。DirSync 始终使用安装它时为用户配置的代理服务器，但是 Azure AD Connect 使用计算机设置。
* 需要在代理服务器中打开的 URL。对于基本的应用场景，DirSync 也支持这些 URL，要求是一样的。如果想要使用任何包括在 Azure AD Connect 中的新功能，则必须打开一些新的 URL。

如果不是从 DirSync 升级，请参阅[相关文档](#related-documentation)了解其他应用场景。

## 从 DirSync 升级
根据当前的 DirSync 部署，可以使用不同的升级选项。如果预期的升级时间少于 3 小时，建议执行就地升级。如果预期的升级时间超过 3 小时，建议在另一台服务器上进行并行部署。如果对象数目超过 50,000 个，预计需要 3 个多小时才能完成升级。

| 方案 |
| --- | --- |
| [就地升级](#in-place-upgrade) |
| [并行部署](#parallel-deployment) |

> [!NOTE]
> 当你规划从 DirSync 升级到 Azure AD Connect 时，在升级之前请勿自行卸载 DirSync。Azure AD Connect 将读取和迁移 DirSync 的配置，并在检查服务器之后卸载 DirSync。
> 
> 

**就地升级** 向导会显示完成升级的预期所需时间。这个估计是基于需要 3 小时才能完成包含 50,000 个对象（用户、联系人和组）的数据库的升级的假设。如果数据库中的对象数少于 50,000 个，则 Azure AD Connect 建议就地升级。如果确定继续，则在升级期间自动应用当前设置，并且服务器自动恢复活动的同步。

如果想要执行配置迁移并执行并行部署，可以拒绝就地升级建议。例如，你可以借机刷新硬件和操作系统。有关详细信息，请参阅[并行部署](#parallel-deployment)部分。

**并行部署** 如果对象数超过 50,000 个，则建议执行并行部署。这样，用户不会遇到任何操作延迟。Azure AD Connect 安装程序将尝试预估升级时的停机时间，但是，如果你曾升级过 DirSync，那么，你自己的经验可能会提供最佳指导。

### 要升级的受支持 DirSync 配置
可升级以下 DirSync 支持的配置更改：

* 域和 OU 筛选
* 备用 ID (UPN)
* 密码同步与 Exchange 混合设置
* 林/域与 Azure AD 设置
* 基于用户属性进行筛选

以下更改无法升级。如果具有此配置，则会阻止升级：

* 不支持的 DirSync 更改，例如删除属性和使用自定义扩展 DLL

![已阻止升级](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

在这些情况下，建议在[过渡模式](active-directory-aadconnectsync-operations.md#staging-mode)下安装新的 Azure AD Connect 服务器，并确认旧的 DirSync 及新的 Azure AD Connect 配置。使用自定义配置重新应用所有更改，如 [Azure AD Connect 同步自定义配置](active-directory-aadconnectsync-whatis.md)中所述。

无法检索且未迁移 DirSync 用于服务帐户的密码。这些密码会在升级期间重置。

### 从 DirSync 升级到 Azure AD Connect 的高级步骤
1. 欢迎使用 Azure AD Connect
2. 当前 DirSync 配置的分析
3. 收集 Azure AD 全局管理员密码
4. 收集企业管理员帐户的凭据（仅在 Azure AD Connect 安装期间使用）
5. 安装 Azure AD Connect
   * 卸载 DirSync（或暂时禁用）
   * 安装 Azure AD Connect
   * （可选）开始同步

发生以下情况时，需要执行其他步骤：

* 你当前正在使用完全版 SQL Server - 本地或远程
* 要同步的对象超过 50,000 个

## 就地升级
1. 启动 Azure AD Connect 安装程序 (MSI)。
2. 查看并同意许可条款和隐私声明。![欢迎使用 Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. 单击“下一步”开始分析现有的 DirSync 安装。![分析现有的目录同步安装](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. 分析完成后，可看到如何继续的建议。
   * 如果使用 SQL Server Express 并且对象数少于 50,000 个，则会显示以下屏幕：![分析完成，已准备好从 DirSync 升级](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
   * 如果对 DirSync 使用完整的 SQL Server，可看到此页面：![分析完成，已准备好从 DirSync 升级](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png) 系统会显示有关 DirSync 使用的现有 SQL Server 数据库服务器的信息。如果需要，请做相应的调整。单击“下一步”继续安装。
   * 如果对象数超过 50,000 个，可看到此屏幕：![分析完成，已准备好从 DirSync 升级](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png) 若要继续进行就地升级，请单击此消息旁的复选框：“继续在此计算机上升级 DirSync”。 若要改为执行[并行部署](#parallel-deployment)，请导出 DirSync 配置设置并将其转移到新服务器。
5. 提供当前用于连接 Azure AD 的帐户的密码。这必须是 DirSync 当前使用的帐户。![输入你的 Azure AD 凭据](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png) 如果收到错误消息并且出现了连接问题，请参阅[排查连接问题](active-directory-aadconnect-troubleshoot-connectivity.md)。
6. 提供 Active Directory 的企业管理员帐户。![输入你的 ADDS 凭据](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. 你现在可以开始配置。单击“升级”后，卸载 DirSync 并配置 Azure AD Connect，然后开始同步。![已准备好配置](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. 安装完成后，请注销并再次登录到 Windows，然后即可使用同步服务管理器或同步规则编辑器，或者尝试进行其他任何配置更改。

## 并行部署
### 导出 DirSync 配置
**对象数超过 50,000 时执行并行部署**

如果对象数超过 50,000 个，Azure AD Connect 安装程序会建议执行并行部署。

将显示如下屏幕：

![分析已完成](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

如果想继续进行并行部署，需要执行以下步骤：

* 单击“导出设置”按钮。在不同的服务器上安装 Azure AD Connect 时，这些设置会从当前的 DirSync 迁移到新的 Azure AD Connect 安装。

成功导出设置后，可以退出 DirSync 服务器上的 Azure AD Connect 向导。继续执行下一步，以[在不同的服务器上安装 Azure AD Connect](#installation-of-azure-ad-connect-on-separate-server)

**对象数少于 50,000 时执行并行部署**

如果你的对象数少于 50,000 个，但仍然想要执行并行部署，请执行以下操作：

1. 运行 Azure AD Connect 安装程序 (MSI)。
2. 看到“欢迎使用 Azure AD Connect”屏幕时，请单击窗口右上角的“X”退出安装向导。
3. 打开命令提示符。
4. 从 Azure AD Connect 的安装位置（默认值：C:\\Program Files\\Microsoft Azure Active Directory Connect）执行以下命令：`AzureADConnect.exe /ForceExport`。
5. 单击“导出设置”按钮。在不同的服务器上安装 Azure AD Connect 时，这些设置会从当前的 DirSync 迁移到新的 Azure AD Connect 安装。

![分析已完成](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

成功导出设置后，可以退出 DirSync 服务器上的 Azure AD Connect 向导。继续执行下一步，以[在不同的服务器上安装 Azure AD Connect](#installation-of-azure-ad-connect-on-separate-server)

### 在不同的服务器上安装 Azure AD Connect
在新的服务器上安装 Azure AD Connect 时，假设要执行 Azure AD Connect 的全新安装。由于想要使用 DirSync 配置，因此还要执行一些额外的步骤：

1. 运行 Azure AD Connect 安装程序 (MSI)。
2. 看到“欢迎使用 Azure AD Connect”屏幕时，请单击窗口右上角的“X”退出安装向导。
3. 打开命令提示符。
4. 从 Azure AD Connect 的安装位置（默认值：C:\\Program Files\\Microsoft Azure Active Directory Connect）执行以下命令：`AzureADConnect.exe /migrate`。Azure AD Connect 安装向导将会启动并显示以下屏幕：![输入你的 Azure AD 凭据](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. 选择从 DirSync 安装中导出的设置文件。
6. 配置任何高级选项，包括：
   * Azure AD Connect 的自定义安装位置。
   * 现有 SQL Server 实例（默认值：Azure AD Connect 将安装 SQL Server 2012 Express）。请不要使用与 DirSync 服务器相同的数据库实例。
   * 用于连接 SQL Server 的服务帐户（如果你的 SQL Server 数据库位于远程，则此帐户必须是域服务帐户）。可以在此屏幕上看到以下选项：![输入你的 Azure AD 凭据](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. 单击“下一步”。
8. 在“已准备好配置”页上，保留选中“配置完成后立即开始同步过程”。服务器现在处于[过渡模式](active-directory-aadconnectsync-operations.md#staging-mode)下，因此未将更改导出到 Azure AD。
9. 单击“安装”。
10. 安装完成后，请注销并再次登录到 Windows，然后即可使用同步服务管理器或同步规则编辑器，或者尝试进行其他任何配置更改。

> [!NOTE]
> 开始 Windows Server Active Directory 和 Azure Active Directory 之间的同步，但不会将任何更改导出到 Azure AD。每次只能有一个同步工具在主动导出更改。此状态称为[过渡模式](active-directory-aadconnectsync-operations.md#staging-mode)。
> 
> 

### 验证 Azure AD Connect 是否已准备好开始同步
若要验证 Azure AD Connect 是否已准备好接管 DirSync，需要从“开始”菜单的“Azure AD Connect”组中打开“同步服务管理器”。

在应用程序中，转至“操作”选项卡。在此选项卡上，确认已完成以下操作：

* 在 AD 连接器上导入
* 在 Azure AD 连接器上导入
* 在 AD 连接器上执行完全同步
* 在 Azure AD 连接器上执行完全同步

![导入和同步已完成](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

检查这些操作的结果，并确保没有任何错误。

如果想查看并检查即将导出到 Azure AD 的更改，请阅读有关如何在[过渡模式](active-directory-aadconnectsync-operations.md#staging-mode)下验证配置的主题。进行所需的配置更改，直到没有任何意外的错误。

完成这些步骤并对结果满意之后，就可以从 DirSync 切换到 Azure AD。

### 卸载 DirSync（旧服务器）
* 在“程序和功能”中查找“Windows Azure Active Directory 同步工具”
* 卸载“Windows Azure Active Directory 同步工具”
* 最长可能需要 15 分钟才能完成卸载。

如果想要稍后卸载 DirSync，也可以暂时关闭服务器或禁用服务。如果出现问题，则此方法允许重新启用服务。但是，预期下一步不会出现问题，所以无需重新启用。

卸载或禁用 DirSync 之后，没有任何活动的服务器导出到 Azure AD。必须完成下一步启用 Azure AD Connect 的操作，才能将本地 Active Directory 中的任何更改继续同步到 Azure AD。

### 启用 Azure AD Connect（新服务器）
安装之后，重新打开 Azure AD Connect 时可以进行其他配置更改。从“开始”菜单或桌面快捷方式启动 **Azure AD Connect**。请确保不要尝试重新运行安装 MSI。

你应该看到以下内容：

![其他任务](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* 选择“配置过渡模式”。
* 取消选中“已启用过渡模式”复选框可以关闭过渡。

![输入你的 Azure AD 凭据](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* 单击“下一步”按钮。
* 在确认页面上，单击“安装”按钮。

Azure AD Connect 现为你的活动服务器。

## 后续步骤
安装 Azure AD Connect 后，可以[验证安装并分配许可证](active-directory-aadconnect-whats-next.md)。

了解有关这些功能（在安装过程中已启用）的详细信息，请参阅[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)、[防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)和 [Azure AD Connect Health](active-directory-aadconnect-health-sync.md)。

了解有关这些常见主题的详细信息，请参阅[计划程序以及如何触发同步](active-directory-aadconnectsync-feature-scheduler.md)。

了解有关[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。

## 相关文档
| 主题 |
| --- | --- |
| Azure AD Connect 概述 |
| 从先前的 Connect 版本升级 |
| 使用快速设置安装 |
| 使用自定义设置安装 |
| 用于安装的帐户 |

<!---HONumber=AcomDC_0921_2016-->