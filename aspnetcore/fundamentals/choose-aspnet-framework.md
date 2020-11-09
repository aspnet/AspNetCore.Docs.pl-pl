---
title: Wybierz między ASP.NET 4. x i ASP.NET Core
author: rick-anderson
description: Wyjaśnia ASP.NET Core a ASP.NET 4. x oraz jak wybierać między nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 95ac4784634d38add5e28644d42b0182e15c6de9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060030"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="c764a-103">Wybierz między ASP.NET 4. x i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c764a-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="c764a-104">ASP.NET Core jest przeprojektowana ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="c764a-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="c764a-105">W tym artykule wymieniono różnice między nimi.</span><span class="sxs-lookup"><span data-stu-id="c764a-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="c764a-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c764a-106">ASP.NET Core</span></span>

<span data-ttu-id="c764a-107">ASP.NET Core to międzyplatformowa platforma służąca do tworzenia nowoczesnych, opartych na chmurze aplikacji sieci Web w systemie Windows, macOS lub Linux.</span><span class="sxs-lookup"><span data-stu-id="c764a-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="c764a-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="c764a-108">ASP.NET 4.x</span></span>

<span data-ttu-id="c764a-109">ASP.NET 4. x to przedwczesne środowisko, które zapewnia usługi, które są konieczne do tworzenia aplikacji sieci Web klasy korporacyjnej opartych na serwerze w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="c764a-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="c764a-110">Wybór struktury</span><span class="sxs-lookup"><span data-stu-id="c764a-110">Framework selection</span></span>

<span data-ttu-id="c764a-111">Poniższa tabela zawiera porównanie ASP.NET Core z ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="c764a-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="c764a-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c764a-112">ASP.NET Core</span></span> | <span data-ttu-id="c764a-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="c764a-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="c764a-114">Kompilacja dla systemu Windows, macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="c764a-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="c764a-115">Kompiluj dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="c764a-115">Build for Windows</span></span>|
|<span data-ttu-id="c764a-116">[ Razor Strony](xref:razor-pages/index) to zalecane podejście do tworzenia interfejsu użytkownika sieci Web w ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="c764a-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="c764a-117">Zobacz również [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api)i [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="c764a-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="c764a-118">Korzystanie z [formularzy sieci Web](/aspnet/web-forms), [SignalR](/aspnet/signalr) , [MVC](/aspnet/mvc), internetowego [interfejsu API](/aspnet/web-api/), elementów [webhook](/aspnet/webhooks/)lub [stron sieci Web](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="c764a-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="c764a-119">Wiele wersji na maszynę</span><span class="sxs-lookup"><span data-stu-id="c764a-119">Multiple versions per machine</span></span>|<span data-ttu-id="c764a-120">Jedna wersja na maszynę</span><span class="sxs-lookup"><span data-stu-id="c764a-120">One version per machine</span></span>|
|<span data-ttu-id="c764a-121">Programowanie za pomocą [programu Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)lub [Visual Studio Code](https://code.visualstudio.com/) przy użyciu języka C# lub F #</span><span class="sxs-lookup"><span data-stu-id="c764a-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="c764a-122">Programowanie za pomocą [programu Visual Studio](https://visualstudio.microsoft.com/vs/) przy użyciu języka C#, VB lub F #</span><span class="sxs-lookup"><span data-stu-id="c764a-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="c764a-123">Wyższa wydajność niż ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="c764a-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="c764a-124">Dobra wydajność</span><span class="sxs-lookup"><span data-stu-id="c764a-124">Good performance</span></span>|
|[<span data-ttu-id="c764a-125">Korzystanie z środowiska uruchomieniowego platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="c764a-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="c764a-126">Użyj środowiska uruchomieniowego .NET Framework</span><span class="sxs-lookup"><span data-stu-id="c764a-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="c764a-127">Aby uzyskać informacje na temat obsługi programu ASP.NET Core 2. x w systemie .NET Framework, zobacz [ASP.NET Core określania celu .NET Framework](xref:index#target-framework) .</span><span class="sxs-lookup"><span data-stu-id="c764a-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="c764a-128">Scenariusze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c764a-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="c764a-129">Witryny internetowe</span><span class="sxs-lookup"><span data-stu-id="c764a-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="c764a-130">Interfejsy API</span><span class="sxs-lookup"><span data-stu-id="c764a-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="c764a-131">Przesyłanie w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="c764a-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="c764a-132">Wdrażanie aplikacji platformy ASP.NET Core na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="c764a-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="c764a-133">Scenariusze ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="c764a-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="c764a-134">Witryny internetowe</span><span class="sxs-lookup"><span data-stu-id="c764a-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="c764a-135">Interfejsy API</span><span class="sxs-lookup"><span data-stu-id="c764a-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="c764a-136">Przesyłanie w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="c764a-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="c764a-137">Tworzenie aplikacji sieci Web ASP.NET 4. x na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="c764a-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="c764a-138">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c764a-138">Additional resources</span></span>

* [<span data-ttu-id="c764a-139">Wprowadzenie do platformy ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c764a-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="c764a-140">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c764a-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
