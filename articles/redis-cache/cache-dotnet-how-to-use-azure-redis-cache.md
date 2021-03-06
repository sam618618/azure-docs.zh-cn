---
title: "如何使用 Azure Redis 缓存 | Microsoft Docs"
description: "了解如何使用 Azure Redis 缓存提高 Azure 应用程序的性能"
services: redis-cache,app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 209d4f610f0d5199d9018c506acef3b7328478ef


---
# <a name="how-to-use-azure-redis-cache"></a>如何使用 Azure Redis Cache
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

本指南说明如何开始使用 **Azure Redis 缓存**。 Microsoft Azure Redis 缓存基于流行的开放源代码 Redis 缓存。 它让您访问 Microsoft 管理的安全专用的 Redis 缓存。 使用 Azure Redis 缓存创建的缓存可从 Microsoft Azure 内的任何应用程序进行访问。

Microsoft Azure Redis 缓存提供以下层：

* **基本** - 单个节点。 多种大小，最大 53 GB。
* **标准** - 双节点主/副本配置。 多种大小，最大 53 GB。 99.9% SLA。
* **高级** - 双节点主/副本配置，最多有 10 个分片。 从 6 GB 到 530 GB 的多种大小（有关详细信息，请与我们联系）。 标准层的所有功能加上其他功能，包括支持 [Redis 群集](cache-how-to-premium-clustering.md)、[Redis 持久性](cache-how-to-premium-persistence.md)和 [Azure 虚拟网络](cache-how-to-premium-vnet.md)。 99.9% SLA。

每个级别在功能和定价方面存在差异。 有关定价信息，请参阅[缓存定价详细信息][缓存定价详细信息]。

本指南说明如何使用以 C\# 代码编写的 [StackExchange.Redis][StackExchange.Redis] 客户端。 涉及的任务包括**创建和配置缓存**、**配置缓存客户端**，以及**在缓存中添加和删除对象**。 有关使用 Azure Redis 缓存的详细信息，请参阅[后续步骤][后续步骤]部分。 有关构建使用 Redis 缓存的 ASP.NET MVC Web 应用的分步教程，请参阅 [How to create a Web App with Redis Cache](cache-web-app-howto.md)（如何创建使用 Redis 缓存的 Web 应用）。

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>开始使用 Azure Redis Cache
Azure Redis Cache 非常容易上手。 若要开始使用，需要首先设置和配置缓存。 接下来，配置缓存客户端，以便它们可以访问缓存。 在配置了缓存客户端后，就可以开始使用它们。

* [创建缓存][创建缓存]
* [配置缓存客户端][配置缓存客户端]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>创建缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>访问已创建的缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

有关配置缓存的详细信息，请参阅 [如何配置 Azure Redis 缓存](cache-configure.md)。

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>配置缓存客户端
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

在配置了你的客户端项目的缓存后，你可以使用以下各节中介绍的方法来使用你的缓存。

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>使用缓存
本节中的步骤介绍如何使用缓存执行常见任务。

* [连接到缓存][连接到缓存]
* [将对象添加到缓存以及从缓存检索对象][将对象添加到缓存以及从缓存检索对象]
* [处理缓存中的 .NET 对象](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>连接到缓存
若要以编程方式使用缓存，你需要引用该缓存。 以下代码添加到你想使用 StackExchange.Redis 客户端的任何文件的顶部，以访问 Azure Redis 缓存。

    using StackExchange.Redis;

> [!NOTE]
> StackExchange.Redis 客户端需要.NET Framework 4 或更高版本。
> 
> 

到 Azure Redis 缓存的连接由 `ConnectionMultiplexer` 类管理。 此类旨在共享并在客户端应用程序中重复使用，不需要在每次执行操作的基础上创建。 

要连接到 Azure Redis 缓存并返回连接的 `ConnectionMultiplexer` 的实例，请调用静态 `Connect` 方法并传递到缓存端点和密钥中，如下例所示。 使用从 Azure 门户生成的密钥作为密码参数。

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> 警告：切勿将凭据存储在源代码中。 为了使本示例简单明了，我将以源代码来呈现凭据内容。 有关如何存储凭据的详细信息，请参阅[应用程序字符串和连接字符串的工作原理][应用程序字符串和连接字符串的工作原理]。
> 
> 

如果你不想使用 SSL，请设置 `ssl=false` 或者省略 `ssl` 参数。

> [!NOTE]
> 默认情况下，将为新缓存禁用非 SSL 端口。 有关启用非 SSL 端口的说明，请参阅 [访问端口](cache-configure.md#access-ports)。
> 
> 

共享应用程序中的 `ConnectionMultiplexer` 实例的一个方法是，拥有返回连接示例的静态属性（与下列示例类似）。 这种线程安全方法，可仅初始化单一连接的 `ConnectionMultiplexer` 实例。 在这些示例中， `abortConnect` 设置为 false，这表示即使未建立 Azure Redis 缓存连接，也可成功调用。 `ConnectionMultiplexer` 的一个关键功能是，一旦还原网络问题和其他原因，它将自动还原缓存连接。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

有关高级连接配置选项的详细信息，请参阅 [StackExchange.Redis 配置模型][StackExchange.Redis 配置模型]。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

建立连接后，通过调用 `ConnectionMultiplexer.GetDatabase` 方法返回对 Redis 缓存数据库的引用。 从 `GetDatabase` 方法返回的对象是一个轻型直通对象，不需要存储。

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

现在您知道如何连接到 Azure Redis Cache 实例并将引用返回缓存数据库，让我们看看如何使用缓存。

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>添加和从缓存检索对象
可以使用 `StringSet` 和 `StringGet` 方法在缓存中存储和检索项。

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis 将大多数数据存储为 Redis 字符串，但这些字符串可能包含许多类型的数据，包括序列化的二进制数据，可在缓存中存储 .NET 对象时使用。

调用 `StringGet` 时，如果该对象存在，则返回它，如果该对象不存在，则返回 `null`。 在这种情况可以从所需的数据源检索值，并将其存储在缓存中供后续使用。 这称为缓存端模式。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

要在缓存中指定项的过期时间，请使用 `StringSet` 的 `TimeSpan` 参数。

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>处理缓存中的 .NET 对象
Azure Redis 缓存可以缓存 .NET 对象以及基元数据类型，但在缓存 .NET 对象之前，必须对其进行序列化。 这是应用程序开发人员的责任，同时赋与开发人员选择序列化程序的弹性。

序列化对象的一种简单方式是使用 [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) 中的 `JsonConvert` 序列化方法，并与 JSON 相互序列化。 以下示例演示了使用 `Employee` 对象实例执行 GET 和 SET。

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>后续步骤
现在，你已学习了基础知识，接下来请打开以下链接了解有关 Azure Redis 缓存的详细信息。

* 了解 Azure Redis 缓存的 ASP.NET 提供程序。
  * [Azure Redis 会话状态提供程序](cache-aspnet-session-state-provider.md)
  * [Azure Redis 缓存 ASP.NET 输出缓存提供程序](cache-aspnet-output-cache-provider.md)
* [启用缓存诊断](cache-how-to-monitor.md#enable-cache-diagnostics)，以便可以[监视](cache-how-to-monitor.md)缓存的运行状况。 可以在 Azure 门户中查看度量值，也可以使用所选的工具 [下载和查看](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 这些度量值。
* 查看 [StackExchange.Redis 缓存客户端文档][StackExchange.Redis 缓存客户端文档]。
  * 可以从许多 Redis 客户端和开发语言访问 azure Redis 缓存。 有关详细信息，请参阅 [http://redis.io/clients][http://redis.io/clients]。
* Azure Redis 缓存还可与第三方服务和工具搭配使用，如 Redsmin 和 Redis Desktop Manager。
  * 有关 Redsmin 的详细信息，请参阅[如何检索 Azure Redis 连接字符串并将其用于 Redsmin][如何检索 Azure Redis 连接字符串并将其用于 Redsmin]。
  * 使用 [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager)通过 GUI 访问和检查 Azure Redis 缓存中的数据。
* 请参阅 [redis][redis] 文档并阅读 [redis 数据类型][redis 数据类型]和 [Redis 数据类型的十五分钟介绍][Redis 数据类型的十五分钟介绍]。

<!-- INTRA-TOPIC LINKS -->
[后续步骤]: #next-steps
[Azure Redis 缓存简介（视频）]: #video
[什么是 Azure Redis 缓存？]: #what-is
[创建 Azure 缓存]: #create-cache
[哪种类型的缓存适合我？]: #choosing-cache
[准备 Visual Studio 项目以使用 Azure Caching]: #prepare-vs
[将应用程序配置为使用缓存]: #configure-app
[开始使用 Azure Redis Cache]: #getting-started-cache-service
[创建缓存]: #create-cache
[配置缓存]: #enable-caching
[配置缓存客户端]: #NuGet
[使用缓存]: #working-with-caches
[连接到缓存]: #connect-to-cache
[添加和从缓存检索对象]: #add-object
[在缓存中指定对象的有效期]: #specify-expiration
[在缓存中存储 ASP.NET 会话状态]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[缓存]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[使用其他语言针对 Azure Redis 缓存进行开发]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[如何检索 Azure Redis 连接字符串并将其用于 Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis 会话状态提供程序]: http://go.microsoft.com/fwlink/?LinkId=398249
[如何以编程方式配置缓存客户端]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[用于 Azure 缓存的会话状态提供程序]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric 缓存：缓存会话状态]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[用于 Azure 缓存的输出缓存提供程序]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure 共享缓存]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[团队博客]: http://blogs.msdn.com/b/windowsazure/
[Azure 缓存]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[如何配置虚拟机大小]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching 容量规划注意事项]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure 缓存]: http://go.microsoft.com/fwlink/?LinkId=252658
[如何以声明方式设置 ASP.NET 页面可缓存性]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[如何以编程方式设置页面的可缓存性]: http://msdn.microsoft.com/library/z852zf6b.aspx
[在 Azure Redis 缓存中配置缓存]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis 配置模型]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[处理缓存中的 .NET 对象]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[安装 NuGet 包管理器]: http://go.microsoft.com/fwlink/?LinkId=240311
[缓存定价详细信息]: http://www.windowsazure.com/pricing/details/cache/
[Azure 门户]: https://portal.azure.com/

[Azure Redis 缓存概述]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[迁移到 Azure Redis 缓存]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis 缓存示例]: http://go.microsoft.com/fwlink/?LinkId=320840
[使用资源组管理 Azure 资源]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis 缓存客户端文档]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis 数据类型]: http://redis.io/topics/data-types
[Redis 数据类型的十五分钟介绍]: http://redis.io/topics/data-types-intro

[应用程序字符串和连接字符串的工作原理]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/





<!--HONumber=Nov16_HO2-->


