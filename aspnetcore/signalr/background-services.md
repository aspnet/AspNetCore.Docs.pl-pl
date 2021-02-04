---
title: ASP.NET Core hosta SignalR w usługach w tle
author: bradygaster
description: Dowiedz się, jak wysyłać komunikaty do SignalR klientów z klas BackgroundService platformy .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 4b877c64a881fec15ac9e9bd74ffdde0b5fa60f9
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530180"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="3a6f5-103">ASP.NET Core hosta SignalR w usługach w tle</span><span class="sxs-lookup"><span data-stu-id="3a6f5-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="3a6f5-104">Autor [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="3a6f5-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="3a6f5-105">Ten artykuł zawiera wskazówki dotyczące:</span><span class="sxs-lookup"><span data-stu-id="3a6f5-105">This article provides guidance for:</span></span>

* <span data-ttu-id="3a6f5-106">Hosting SignalR centrów przy użyciu procesu roboczego w tle hostowanego z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="3a6f5-107">Wysyłanie komunikatów do podłączonych klientów z poziomu platformy .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="3a6f5-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a6f5-108">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3a6f5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a6f5-109">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3a6f5-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="3a6f5-110">Włącz SignalR przy uruchamianiu</span><span class="sxs-lookup"><span data-stu-id="3a6f5-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a6f5-111">Hostowanie ASP.NET Core SignalR Hub w kontekście procesu roboczego w tle jest takie samo, jak hostowanie centrum w aplikacji sieci web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="3a6f5-112">W `Startup.ConfigureServices` metodzie wywoływanie `services.AddSignalR` powoduje dodanie wymaganych usług do warstwy iniekcja (ASP.NET Core zależność) do obsługi SignalR .</span><span class="sxs-lookup"><span data-stu-id="3a6f5-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="3a6f5-113">W programie `Startup.Configure` `MapHub` Metoda jest wywoływana w `UseEndpoints` wywołaniu zwrotnym, aby połączyć punkty końcowe centrum w potoku żądania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a6f5-114">Hostowanie ASP.NET Core SignalR Hub w kontekście procesu roboczego w tle jest takie samo, jak hostowanie centrum w aplikacji sieci web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="3a6f5-115">W `Startup.ConfigureServices` metodzie wywoływanie `services.AddSignalR` powoduje dodanie wymaganych usług do warstwy iniekcja (ASP.NET Core zależność) do obsługi SignalR .</span><span class="sxs-lookup"><span data-stu-id="3a6f5-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="3a6f5-116">W programie `Startup.Configure` `UseSignalR` Metoda jest wywoływana, aby połączyć punkty końcowe centrum w potoku żądania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="3a6f5-117">W poprzednim przykładzie `ClockHub` Klasa implementuje `Hub<T>` klasę, aby utworzyć koncentrator o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="3a6f5-118">Program `ClockHub` został skonfigurowany w klasie w `Startup` celu reagowania na żądania w punkcie końcowym `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="3a6f5-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="3a6f5-119">Aby uzyskać więcej informacji na temat silnych typach centrów, zobacz [Używanie centrów w SignalR dla ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="3a6f5-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="3a6f5-120">Ta funkcja nie jest ograniczona do [klasy \<T> centrum](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="3a6f5-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="3a6f5-121">Każda klasa, która dziedziczy z [koncentratora](xref:Microsoft.AspNetCore.SignalR.Hub), na przykład [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), działa.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="3a6f5-122">Interfejs używany przez silnie typ `ClockHub` jest `IClock` interfejsem.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="3a6f5-123">Wywoływanie SignalR centrum z poziomu usługi w tle</span><span class="sxs-lookup"><span data-stu-id="3a6f5-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="3a6f5-124">Podczas uruchamiania, `Worker` Klasa, a `BackgroundService` , jest włączona przy użyciu `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="3a6f5-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="3a6f5-125">Ponieważ SignalR jest również włączona w `Startup` fazie, w którym każde centrum jest dołączone do pojedynczego punktu końcowego w potoku żądania HTTP ASP.NET Core, każde centrum jest reprezentowane przez `IHubContext<T>` serwer.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="3a6f5-126">Przy użyciu ASP.NET Core DI Features, inne klasy tworzone przez warstwę hostingu, takie jak `BackgroundService` klasy, klasy kontrolerów MVC lub Razor modele stron, mogą uzyskać odwołania do centrów po stronie serwera, akceptując wystąpienia `IHubContext<ClockHub, IClock>` w trakcie konstruowania.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="3a6f5-127">Ponieważ `ExecuteAsync` Metoda jest wywoływana iteracyjnie w usłudze w tle, bieżąca data i godzina serwera są wysyłane do podłączonych klientów przy użyciu `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="3a6f5-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="3a6f5-128">Reagowanie na SignalR zdarzenia za pomocą usług w tle</span><span class="sxs-lookup"><span data-stu-id="3a6f5-128">React to SignalR events with background services</span></span>

<span data-ttu-id="3a6f5-129">Podobnie jak w przypadku aplikacji jednostronicowej korzystającej z klienta JavaScript dla programu SignalR lub aplikacji klasycznej platformy .NET korzystającej z programu <xref:signalr/dotnet-client> , a lub do łączenia się z `BackgroundService` `IHostedService` SignalR centrami i reagowania na zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-129">Like a Single Page App using the JavaScript client for SignalR, or a .NET desktop app using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="3a6f5-130">`ClockHubClient`Klasa implementuje `IClock` interfejs i `IHostedService` interfejs.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="3a6f5-131">W ten sposób można ją włączyć w sposób `Startup` ciągły i reagować na zdarzenia centrów z serwera.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="3a6f5-132">Podczas inicjowania program `ClockHubClient` tworzy wystąpienie `HubConnection` a i włącza `IClock.ShowTime` metodę jako procedurę obsługi dla `ShowTime` zdarzenia centrum.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="3a6f5-133">W `IHostedService.StartAsync` implementacji `HubConnection` jest uruchamiany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="3a6f5-134">W trakcie `IHostedService.StopAsync` metody `HubConnection` jest usuwany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="3a6f5-135">W `IHostedService.StartAsync` implementacji `HubConnection` jest uruchamiany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="3a6f5-136">W trakcie `IHostedService.StopAsync` metody `HubConnection` jest usuwany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="3a6f5-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3a6f5-137">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3a6f5-137">Additional resources</span></span>

* [<span data-ttu-id="3a6f5-138">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3a6f5-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3a6f5-139">Centra</span><span class="sxs-lookup"><span data-stu-id="3a6f5-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3a6f5-140">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="3a6f5-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="3a6f5-141">Centra o jednoznacznie określonym typie</span><span class="sxs-lookup"><span data-stu-id="3a6f5-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
