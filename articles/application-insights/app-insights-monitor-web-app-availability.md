---
title: "监视任何网站的可用性和响应能力 | Microsoft Docs"
description: "在 Application Insights 中设置 Web 测试。 当网站不可用或响应速度缓慢时接收警报。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: b70c8baab03703bc00b75c2c611f69e3b71d6cd7
ms.openlocfilehash: d3478ef704c0029f69cca141bd3fa0b3ac54de15


---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>监视任何网站的可用性和响应能力
将 Web 应用或网站部署到任何服务器之后，可以设置 Web 测试来监视其可用性和响应能力。 [Visual Studio Application Insights](app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果应用程序无响应或响应太慢，则会发出警报。

![Web 测试示例](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

对于可以从公共 Internet 访问的任何 HTTP 或 HTTPS 终结点，均可设置 Web 测试。

Web 测试有两种类型：

* [URL ping 测试](#create)：可以在 Azure 门户中创建的简单测试。
* [多步骤 Web 测试](#multi-step-web-tests)：可以在 Visual Studio Ultimate 或 Visual Studio Enterprise 中创建并上载到门户的测试。

对于每个应用程序资源，最多可以创建 10 个 Web 测试。

## <a name="a-namecreatea1-create-a-resource-for-your-test-reports"></a><a name="create"></a>1.为测试报告创建资源
如果已经为此应用程序[设置 Application Insights 资源][启动]，并且想要在同一位置中查看可用性报告，请跳过本步骤。

注册 [Microsoft Azure](http://azure.com)，转到 [Azure 门户](https://portal.azure.com)，然后创建 Application Insights 资源。

![“新建”>“Application Insights”](./media/app-insights-monitor-web-app-availability/11-new-app.png)

单击“所有资源”，打开新资源的“概述”边栏选项卡。

## <a name="a-namesetupa2-create-a-url-ping-test"></a><a name="setup"></a>2.创建 URL ping 测试
在 Application Insights 资源中，找到“可用性”磁贴。 单击它，打开应用程序的“Web 测试”边栏选项卡，然后添加 Web 测试。

![至少填写网站的 URL](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **URL** 必须在公共 Internet 上公开。 它可以包含查询字符串 &#151; 例如，可以稍微训练一下数据库。 如果 URL 解析为重定向，最多可以跟踪 10 个重定向。
* **分析依赖请求**：测试时请求图像、脚本、样式文件和页面的其他资源，记录的响应时间包括这些时间。 如果无法在超时期限内为整个测试成功下载所有这些资源，测试将会失败。
* **启用重试**：测试失败时，将在短时间后重试。 仅当连续三次尝试失败时，才报告失败。 然后，将按照一般的测试频率执行后续测试。 重试将会暂停，直到下次成功为止。 可在每个测试位置单独应用此规则。 （建议使用此设置。 平均大约有 80% 的失败可在重试后消除。）
* **测试频率**：设置从每个测试位置运行测试的频率。 如果有五个测试位置，且频率为五分钟，则平均每隔一分钟测试站点一次。
* **测试位置** 是服务器将 Web 请求发送到的 URL 位置。 请选择多个位置，以便区分网站问题与网络问题。 最多可以选择 16 个位置。
* **成功准则**：

    **测试超时**：减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源。

    **HTTP 响应**：视为成功的返回状态代码。 代码 200 指示返回了正常网页。

    **内容匹配**：类似于“欢迎!”的字符串。 将测试它是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。
* **警报** 。 某个位置的失败很可能是网络问题，而不是站点问题。 但是，可以将阈值更改为更敏感或更不敏感，也可以更改要将电子邮件发送给哪个人。

    可以设置在引发警报时调用的 [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)。 （但请注意，查询参数不会以“属性”的形式传递。）

### <a name="test-more-urls"></a>测试其他 URL
添加更多测试。 例如，除了测试主页，还可以测试搜索 URL 来确保数据库正在运行。

## <a name="a-namemonitora3-see-your-web-test-results"></a><a name="monitor"></a>3.查看 Web 测试结果
1-2 分钟后，结果将出现在“Web 测试”边栏选项卡中。

![主页边栏选项卡中的摘要结果](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

单击摘要图表上的任何条形可以获取该时间段的更详细视图。

这些图表合并了此应用程序的所有 Web 测试结果。

## <a name="a-namefailuresaif-you-see-failures"></a><a name="failures"></a>如果看到失败
单击红点。

![单击红点](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

或者，向下滚动并单击成功率小于 100% 的测试。

![单击特定的 Web 测试](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

该测试的结果将会打开。

![单击特定的 Web 测试](./media/app-insights-monitor-web-app-availability/16-1test.png)

从多个位置运行测试 &#151; 选择结果小于 100% 的测试。

![单击特定的 Web 测试](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)

向下滚动到“失败的测试”并选择结果。

单击该结果，在门户中对它进行评估并查看失败原因。

![Web 测试运行结果](./media/app-insights-monitor-web-app-availability/18-availDetails.png)

或者，可以下载结果文件，然后在 Visual Studio 中检查。

*看起来正常，但却报告为失败*  检查所有图像、脚本、样式表和页面加载的任何其他文件。 如果其中有任何一个失败，即使 html 主页加载正常，测试也仍会报告为失败。

## <a name="multistep-web-tests"></a>多步骤 Web 测试
可以监视涉及一连串 URL 的方案。 例如，如果正在监视销售网站，可以测试是否能够正常地将商品添加购物车。

若要创建多步骤测试，可以使用 Visual Studio 来录制方案，然后将录制内容上载到 Application Insights。 Application Insights 将按特定间隔重放该方案，并验证响应。

请注意，无法在测试中使用编码的函数：方案步骤必须作为脚本包含在 .webtest 文件中。

#### <a name="1-record-a-scenario"></a>1.录制方案
使用 Visual Studio Enterprise 或 Ultimate 录制 Web 会话。

1. 创建 Web 性能测试项目。

    ![在 Visual Studio 中，基于“Web 性能测试和负载测试”模板创建项目。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. 打开 .webtest 文件并开始录制。

    ![打开 .webtest 文件并单击“录制”。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. 执行要在测试中模拟的用户操作：打开网站、将产品加入购物车，等等。 然后停止测试。

    ![Web 测试录制程序在 Internet Explorer 中运行。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    不要录制太长的方案。 以 100 个步骤和 2 分钟为限。
4. 编辑测试：

   * 添加验证，检查收到的文本和响应代码。
   * 删除所有多余的交互。 也可以删除图片、广告或跟踪站点的依赖请求。

     请记住，只能编辑测试脚本 - 不能添加自定义代码或调用其他 Web 测试。 不要在测试中插入循环。 可以使用标准 Web 测试插件。
5. 在 Visual Studio 中运行测试，确保它可以正常工作。

    Web 测试运行器将打开 Web 浏览器，并重复录制的操作。 请确保它按预期工作。

    ![在 Visual Studio 中，打开 .webtest 文件并单击“运行”。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2.将 Web 测试上载到 Application Insights
1. 在 Application Insights 门户中创建新 Web 测试。

    ![在 Web 测试边栏选项卡中选择“添加”。](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. 选择多步骤测试并上载 .webtest 文件。

    ![选择多步骤 Web 测试。](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    像设置 ping 测试一样设置测试位置、频率和警报参数。

像在单 url 测试中一样查看测试结果和所有失败。

失败的常见原因是测试运行时间太久。 运行时间不能超过两分钟。

别忘了，必须正确加载页面的所有资源，测试才能成功（包括脚本、样式表、图像，等等）。

请注意，Web 测试必须完全包含在 .webtest 文件中：不能在测试中使用编码的函数。

### <a name="plugging-time-and-random-numbers-into-your-multistep-test"></a>将时间和随机数插入多步骤测试
假设要测试的工具从外部源获取与时间相关的数据（例如股票）。 录制 Web 测试时，必须使用具体的时间，但要将它们设置为测试参数：StartTime 和 EndTime。

![带参数的 Web 测试。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

运行测试时，EndTime 应该始终为当前时间，StartTime 在 15 分钟前。

Web 测试插件提供时间参数化方式。

1. 针对所需的每个变量参数值添加一个 Web 测试插件。 在 Web 测试工具栏中，选择“添加 Web 测试插件”。

    ![选择“添加 Web 测试插件”并选择类型。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    本示例使用两个日期时间插件实例。 一个实例设置为“15 分钟前”，另一个实例设置为“现在”。
2. 打开每个插件的属性。 为插件命名，然后将它设置为使用当前时间。 对于其中一个插件，将“添加分钟”设置为 -15。

    ![设置名称，“使用当前时间”和“添加分钟”。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. 在 Web 测试参数中，使用 {{plug-in name}} 来引用插件名称。

    ![在测试参数中使用 {{plug-in name}}。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

现在，将测试上载到门户。 每次运行测试时，将使用动态值。

## <a name="dealing-with-signin"></a>处理登录
如果用户登录应用，可以使用许多选项来模拟登录，以便可以在登录后测试页面。 使用的方法取决于应用提供的安全性类型。

在所有情况下，应该只针对测试目的在应用程序中创建帐户。 如果可能，请限制此测试帐户的权限，以便 Web 测试不会影响实际用户。

### <a name="simple-username-and-password"></a>简单的用户名和密码
以普通方式录制 Web 测试。 先删除 Cookie。

### <a name="saml-authentication"></a>SAML 身份验证
使用可用于 Web 测试的 SAML 插件。

### <a name="client-secret"></a>客户端机密
如果应用的某个登录路由涉及到客户端机密，请使用该路由。 例如，Azure Active Directory (AAD) 就是提供客户端机密登录的服务。 在 AAD 中，客户端机密是应用密钥。

下面是使用应用密钥的 Azure Web 应用的 Web 测试示例：

![客户端机密示例](./media/app-insights-monitor-web-app-availability/110.png)

1. 使用客户端机密 (AppKey) 从 AAD 获取令牌。
2. 从响应中提取持有者令牌。
3. 使用授权标头中的持有者令牌调用 API。

确保 Web 测试是实际客户端 - 即，在 AAD 中有自身的应用 - 并使用其 clientId 和 appkey。 测试中的服务在 AAD 中也有自身的应用：此应用的 appID URI 反映在“resource”字段中的 Web 测试内。

### <a name="open-authentication"></a>开放身份验证
开放身份验证的示例包括使用 Microsoft 或 Google 帐户登录。 许多使用 OAuth 的应用提供替代的客户端机密，因此第一个技巧就是调查这种可能性。

如果测试必须使用 OAuth 登录，则常规方法是：

* 使用 Fiddler 等工具检查 Web 浏览器、身份验证站点与应用之间的流量。
* 使用不同的计算机或浏览器或者以较长的间隔执行两次以上的登录（使令牌过期）。
* 通过比较不同的会话，识别从身份验证站点返回的令牌，然后在登录后将此令牌传递给应用服务器。
* 使用 Visual Studio 录制 Web 测试。
* 参数化令牌，设置参数来指定从验证器返回令牌的时间，并在站点查询中使用该参数。
  （Visual Studio 会尝试参数化测试，但无法正确参数化令牌。）

## <a name="a-nameedita-edit-or-disable-a-test"></a><a name="edit"></a> 编辑或禁用测试
打开单个测试进行编辑或将其禁用。

![编辑或禁用 Web 测试](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

对服务执行维护时，可能需要禁用 Web 测试。

## <a name="performance-tests"></a>性能测试
可以在网站上运行负载测试。 与可用性测试一样，可以从全球各地的站点发送简单请求或多步骤请求。 与可用性测试不同的是，发送的许多请求可以模拟多个并发用户。

在“概述”边栏选项卡中，打开“设置”、“性能测试”。 创建测试时，系统会邀请你连接或创建 Visual Studio Team Services 帐户。

测试完成时，将显示响应时间和成功率。

## <a name="automation"></a>自动化
* [使用 PowerShell 脚本设置 Web 测试](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/) 。
* 设置在引发警报时调用的 [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) 。

## <a name="questions-problems"></a>有疑问？ 遇到问题？
* *是否可以从 Web 测试调用代码？*

    不可以。 测试步骤必须在 .webtest 文件中指定。 此外，不能调用其他 Web 测试或使用循环。 但是可以借助一些有用的插件。
* *是否支持 HTTPS？*

    支持 TLS 1.1 和 TLS 1.2。
* *“Web 测试”与“可用性测试”之间是否有差异？*

    这两个术语可以换用。
* *如何在防火墙后面运行的内部服务器上使用可用性测试？*

    请将防火墙配置为允许从 [Web 测试代理 IP 地址](app-insights-ip-addresses.md)发出的请求。
* *上载多步骤 Web 测试失败*

    存在 300 K 大小限制。

    不支持循环。

    不支持对其他 Web 测试的引用。

    不支持数据源。
* *多步骤测试无法完成*

    存在每个测试 100 个请求的限制。

    如果运行时间超过两分钟，测试将会停止。
* *如何使用客户端证书运行测试？*

    抱歉，不支持这种测试。

## <a name="a-namevideoavideo"></a><a name="video"></a>视频
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Monitoring-Availability-with-Application-Insights/player]
>
>

## <a name="a-namenextanext-steps"></a><a name="next"></a>后续步骤
[搜索诊断日志][诊断]

[故障排除][问题与解答]

[Web 测试代理 IP 地址](app-insights-ip-addresses.md)

<!--Link references-->

[Azure 可用性]: ../insights-create-web-tests.md
[诊断]: app-insights-diagnostic-search.md
[问题与解答]: app-insights-troubleshoot-faq.md
[启动]: app-insights-overview.md



<!--HONumber=Nov16_HO2-->


