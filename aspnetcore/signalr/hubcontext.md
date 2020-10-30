---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: 'Dowiedz się, jak używać :::no-loc(SignalR)::: usługi ASP.NET Core HubContext do wysyłania powiadomień do klientów spoza centrum.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058184"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="7ea4c-103">Wysyłanie komunikatów spoza centrum</span><span class="sxs-lookup"><span data-stu-id="7ea4c-103">Send messages from outside a hub</span></span>

<span data-ttu-id="7ea4c-104">Autor [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="7ea4c-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="7ea4c-105">:::no-loc(SignalR):::Centrum to podstawowe streszczenie służące do wysyłania komunikatów do klientów podłączonych do :::no-loc(SignalR)::: serwera programu.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="7ea4c-106">Możliwe jest również wysyłanie komunikatów z innych miejsc w aplikacji przy użyciu `IHubContext` usługi.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="7ea4c-107">W tym artykule wyjaśniono, jak uzyskać dostęp do programu w :::no-loc(SignalR)::: `IHubContext` celu wysyłania powiadomień do klientów spoza centrum.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-107">This article explains how to access a :::no-loc(SignalR)::: `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="7ea4c-108">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7ea4c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="7ea4c-109">Pobierz wystąpienie elementu IHubContext</span><span class="sxs-lookup"><span data-stu-id="7ea4c-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="7ea4c-110">W ASP.NET Core :::no-loc(SignalR)::: można uzyskać dostęp do wystąpienia `IHubContext` przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="7ea4c-111">Można wstrzyknąć wystąpienie `IHubContext` do kontrolera, oprogramowania pośredniczącego lub innej usługi.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="7ea4c-112">Użyj wystąpienia, aby wysyłać komunikaty do klientów.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="7ea4c-113">Różni się to od ASP.NET 4. x :::no-loc(SignalR)::: , które były używane GlobalHost w celu zapewnienia dostępu do programu `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="7ea4c-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="7ea4c-114">ASP.NET Core ma strukturę iniekcji zależności, która eliminuje potrzebę tego globalnej pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="7ea4c-115">Wstrzyknąć wystąpienie elementu IHubContext w kontrolerze</span><span class="sxs-lookup"><span data-stu-id="7ea4c-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="7ea4c-116">Można wstrzyknąć wystąpienie `IHubContext` do kontrolera przez dodanie go do konstruktora:</span><span class="sxs-lookup"><span data-stu-id="7ea4c-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="7ea4c-117">Teraz, mając dostęp do wystąpienia `IHubContext` , można wywołać metody centrum, tak jakby znajdowały się one w centrum.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="7ea4c-118">Pobierz wystąpienie elementu IHubContext w oprogramowaniu pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="7ea4c-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="7ea4c-119">Uzyskaj dostęp do tego `IHubContext` potoku w ramach oprogramowania pośredniczącego, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="7ea4c-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="7ea4c-120">Gdy metody piasty są wywoływane spoza `Hub` klasy, nie istnieje obiekt wywołujący skojarzony z wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="7ea4c-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="7ea4c-121">W związku z tym nie ma dostępu do `ConnectionId` `Caller` właściwości, i `Others` .</span><span class="sxs-lookup"><span data-stu-id="7ea4c-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="7ea4c-122">Pobierz wystąpienie elementu IHubContext z IHost</span><span class="sxs-lookup"><span data-stu-id="7ea4c-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="7ea4c-123">Dostęp do programu `IHubContext` z hosta sieci Web jest przydatny do integracji z obszarami poza ASP.NET Core, na przykład przy użyciu platform wtrysku zależności innych firm:</span><span class="sxs-lookup"><span data-stu-id="7ea4c-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="7ea4c-124">Wstrzyknąć HubContext o jednoznacznie określonym typie</span><span class="sxs-lookup"><span data-stu-id="7ea4c-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="7ea4c-125">Aby wstrzyknąć HubContext o jednoznacznie określonym typie, upewnij się, że koncentrator dziedziczy z `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="7ea4c-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="7ea4c-126">Wstrzyknąć go przy użyciu `IHubContext<THub, T>` interfejsu zamiast `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="7ea4c-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="7ea4c-127">Aby uzyskać więcej informacji, zobacz [centra o jednoznacznie określonym typie](xref:signalr/hubs#strongly-typed-hubs) .</span><span class="sxs-lookup"><span data-stu-id="7ea4c-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="7ea4c-128">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="7ea4c-128">Related resources</span></span>

* [<span data-ttu-id="7ea4c-129">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="7ea4c-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7ea4c-130">Centra</span><span class="sxs-lookup"><span data-stu-id="7ea4c-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7ea4c-131">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="7ea4c-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
