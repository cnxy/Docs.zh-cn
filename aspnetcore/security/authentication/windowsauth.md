---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: ardalis
description: 本文介绍如何在使用 IIS Express、 IIS、 HTTP.sys 和 WebListener 的 ASP.NET Core 中配置 Windows 身份验证。
ms.author: riande
ms.date: 08/18/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: a8066d248c0d4db1d1f61b2a14bdb4656a2f4265
ms.sourcegitcommit: ecf2cd4e0613569025b28e12de3baa21d86d4258
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43312407"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>在 ASP.NET Core 中配置 Windows 身份验证

作者：[Steve Smith](https://ardalis.com) 和 [Scott Addie](https://twitter.com/Scott_Addie)

承载有 IIS、 ASP.NET Core 应用可以配置 Windows 身份验证[HTTP.sys](xref:fundamentals/servers/httpsys)，或[WebListener](xref:fundamentals/servers/weblistener)。

## <a name="windows-authentication"></a>Windows 身份验证

Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。 使用 Active Directory 域标识或其他 Windows 帐户标识的用户在公司网络上运行你的服务器时，可以使用 Windows 身份验证。 Windows 身份验证是最适合于 intranet 环境中的用户、 客户端应用程序和 web 服务器属于同一个 Windows 域。

[了解有关 Windows 身份验证的详细信息，并为 IIS 安装](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)。

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>启用 Windows 身份验证在 ASP.NET Core 应用

Visual Studio Web 应用程序模板可配置为支持 Windows 身份验证。

### <a name="use-the-windows-authentication-app-template"></a>使用 Windows 身份验证应用程序模板

在 Visual Studio 中：

1. 创建新的 ASP.NET Core Web 应用程序。
1. 从模板列表中选择 Web 应用程序。
1. 选择**更改身份验证**按钮，然后选择**Windows 身份验证**。

运行应用。 用户名将显示在顶部的应用程序。

![Windows 身份验证浏览器屏幕截图](windowsauth/_static/browser-screenshot.png)

对于使用 IIS Express 的开发工作，该模板提供使用 Windows 身份验证所需的所有配置。 以下部分介绍如何手动配置 Windows 身份验证的 ASP.NET Core 应用。

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Windows 和匿名身份验证的 visual Studio 设置

Visual Studio 项目**属性**页的**调试**选项卡提供有关 Windows 身份验证和匿名身份验证的复选框。

![Windows 身份验证浏览器屏幕截图](windowsauth/_static/vs-auth-property-menu.png)

或者，可以在配置这两个属性*launchSettings.json*文件：

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a>启用与 IIS Windows 身份验证

IIS 使用[ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)到承载 ASP.NET Core 应用程序。 在 IIS 中，不应用配置 Windows 身份验证。 以下部分说明如何使用 IIS 管理器配置为使用 Windows 身份验证的 ASP.NET Core 应用。

### <a name="iis-configuration"></a>IIS 配置

启用 Windows 身份验证的 IIS 角色服务。 有关详细信息，请参阅[IIS 角色服务 （请参阅步骤 2） 中启用 Windows 身份验证](xref:host-and-deploy/iis/index#iis-configuration)。

默认情况下，IIS 集成中间件配置为自动进行身份验证请求。 有关详细信息，请参阅[使用 IIS 的 Windows 上托管 ASP.NET Core: IIS 选项 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)。

默认情况下，ASP.NET Core 模块配置为转发到应用的 Windows 身份验证令牌。 有关详细信息，请参阅[ASP.NET Core 模块配置参考： aspNetCore 元素的特性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。

### <a name="create-a-new-iis-site"></a>创建新的 IIS 站点

指定的名称和文件夹，并允许它创建新的应用程序池。

### <a name="customize-authentication"></a>自定义身份验证

打开该站点的身份验证功能。

![IIS 身份验证菜单](windowsauth/_static/iis-authentication-menu.png)

禁用匿名身份验证并启用 Windows 身份验证。

![IIS 身份验证设置](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>将你的项目发布到 IIS 的站点文件夹

使用 Visual Studio 或.NET Core CLI，将应用发布到目标文件夹。

![Visual Studio 发布对话框](windowsauth/_static/vs-publish-app.png)

详细了解如何[发布到 IIS](xref:host-and-deploy/iis/index)。

启动应用程序以验证 Windows 身份验证正常工作。

::: moniker range=">= aspnetcore-2.0"

## <a name="enable-windows-authentication-with-httpsys"></a>启用 Windows 身份验证使用 HTTP.sys

虽然 Kestrel 不支持 Windows 身份验证，您可以使用[HTTP.sys](xref:fundamentals/servers/httpsys)以在 Windows 上支持自承载的方案。 下面的示例配置以使用 Windows 身份验证使用 HTTP.sys 的应用程序的 web 主机：

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

> [!NOTE]
> HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。 Kerberos 和 HTTP.sys 不支持用户模式身份验证。 必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。 注册主机的服务主体名称 (SPN)，而不是应用的用户。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

## <a name="enable-windows-authentication-with-weblistener"></a>启用与 WebListener 的 Windows 身份验证

虽然 Kestrel 不支持 Windows 身份验证，您可以使用[WebListener](xref:fundamentals/servers/weblistener)以在 Windows 上支持自承载的方案。 下面的示例配置应用程序的 web 主机，若要将 WebListener 与 Windows 身份验证：

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

> [!NOTE]
> WebListener 通过 Kerberos 身份验证协议委托给内核模式身份验证。 Kerberos 和 WebListener 不支持用户模式身份验证。 必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。 注册主机的服务主体名称 (SPN)，而不是应用的用户。

::: moniker-end

## <a name="work-with-windows-authentication"></a>使用 Windows 身份验证

匿名访问的配置状态确定的方式`[Authorize]`和`[AllowAnonymous]`应用中使用属性。 以下两个部分介绍如何处理的匿名访问权限的禁止和允许配置状态。

### <a name="disallow-anonymous-access"></a>不允许匿名访问

启用 Windows 身份验证，并且禁用了匿名访问，则`[Authorize]`和`[AllowAnonymous]`特性不起作用。 如果 IIS 站点 （或 HTTP.sys 或 WebListener 服务器） 配置为不允许匿名访问，请求将永远不会到达您的应用程序。 出于此原因，`[AllowAnonymous]`属性不适用。

### <a name="allow-anonymous-access"></a>允许匿名访问

启用 Windows 身份验证和匿名访问，使用`[Authorize]`和`[AllowAnonymous]`属性。 `[Authorize]`属性允许你保护部分，该应用程序的真正需要 Windows 身份验证。 `[AllowAnonymous]`特性重写`[Authorize]`特性允许匿名访问的应用中的使用情况。 请参阅[简单授权](xref:security/authorization/simple)属性使用情况详细信息。

在 ASP.NET Core 2.x，请`[Authorize]`属性需要进行额外配置中的*Startup.cs*以进行 Windows 身份验证质询匿名请求。 建议的配置略有根据正在使用的 web 服务器而异。

> [!NOTE]
> 默认情况下，缺少授权可访问的页面的用户会显示 HTTP 403 响应为空。 [StatusCodePages 中间件](xref:fundamentals/error-handling#configure-status-code-pages)可以配置为向用户提供更好的"拒绝访问"体验。

#### <a name="iis"></a>IIS

如果使用 IIS，添加以下代码到`ConfigureServices`方法：

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

如果使用 HTTP.sys，添加以下代码到`ConfigureServices`方法：

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Impersonation

ASP.NET Core 未实现模拟。 应用程序运行具有使用应用程序池或进程标识的所有请求的应用程序标识。 如果您需要显式执行代表用户操作，使用`WindowsIdentity.RunImpersonated`。 在此上下文中运行的单个操作，然后关闭上下文。

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

请注意，`RunImpersonated`不支持异步操作，不应该用于复杂的方案。 例如，包装整个请求或中间件链不受支持或推荐的。
