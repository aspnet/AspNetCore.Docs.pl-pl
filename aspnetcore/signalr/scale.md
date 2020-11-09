---
title: 'ASP.NET Core SignalR hosting i skalowanie produkcji'
author: bradygaster
description: 'Dowiedz się, jak uniknąć problemów z wydajnością i skalowaniem w aplikacjach korzystających z ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: d3e9cd23a55702bcf9b002dcce556428683afeca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052776"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="77e99-103">ASP.NET Core SignalR hosting i skalowanie</span><span class="sxs-lookup"><span data-stu-id="77e99-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="77e99-104">Autorzy [Andrew Stanton-pielęgniarki](https://twitter.com/anurse), [Brady Gastera](https://twitter.com/bradygaster)i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="77e99-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="77e99-105">W tym artykule wyjaśniono zagadnienia dotyczące hostingu i skalowania dla aplikacji o dużym natężeniu ruchu, które używają ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="77e99-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="77e99-106">Sesje programu Sticky</span><span class="sxs-lookup"><span data-stu-id="77e99-106">Sticky Sessions</span></span>

<span data-ttu-id="77e99-107">SignalR wymaga, aby wszystkie żądania HTTP dotyczące określonego połączenia były obsługiwane przez ten sam proces serwera.</span><span class="sxs-lookup"><span data-stu-id="77e99-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="77e99-108">Gdy SignalR program jest uruchomiony w farmie serwerów (na wielu serwerach), należy użyć "sesji programu Sticky Notes".</span><span class="sxs-lookup"><span data-stu-id="77e99-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="77e99-109">"Sesje programu Sticky Notes" są również nazywane koligacją sesji przez niektóre moduły równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="77e99-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="77e99-110">Azure App Service używa [routingu żądań aplikacji](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) do przesyłania żądań.</span><span class="sxs-lookup"><span data-stu-id="77e99-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="77e99-111">Włączenie ustawienia "koligacja ARR" w Azure App Service spowoduje włączenie "sesji programu Sticky Notes".</span><span class="sxs-lookup"><span data-stu-id="77e99-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="77e99-112">Jedyną sytuacją, w której nie są wymagane sesje programu Sticky, są:</span><span class="sxs-lookup"><span data-stu-id="77e99-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="77e99-113">W przypadku hostowania na jednym serwerze w ramach jednego procesu.</span><span class="sxs-lookup"><span data-stu-id="77e99-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="77e99-114">W przypadku korzystania z SignalR usługi platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="77e99-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="77e99-115">Gdy wszyscy klienci są skonfigurowani do korzystania **tylko** z obiektów WebSockets, **a** [ustawienie SkipNegotiation](xref:signalr/configuration#configure-additional-options) jest włączone w konfiguracji klienta.</span><span class="sxs-lookup"><span data-stu-id="77e99-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="77e99-116">We wszystkich innych przypadkach (w tym gdy jest używany plan Redis), środowisko serwera musi być skonfigurowane dla sesji programu Sticky Notes.</span><span class="sxs-lookup"><span data-stu-id="77e99-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="77e99-117">Aby uzyskać wskazówki dotyczące konfigurowania Azure App Service dla programu SignalR , zobacz <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="77e99-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="77e99-118">Zasoby połączenia TCP</span><span class="sxs-lookup"><span data-stu-id="77e99-118">TCP connection resources</span></span>

<span data-ttu-id="77e99-119">Liczba współbieżnych połączeń TCP, które może obsługiwać serwer sieci Web, jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="77e99-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="77e99-120">Klienci standardowi HTTP korzystają z połączeń *tymczasowych* .</span><span class="sxs-lookup"><span data-stu-id="77e99-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="77e99-121">Te połączenia można zamknąć, gdy klient przechodzi w stan bezczynności i zostanie otwarty ponownie później.</span><span class="sxs-lookup"><span data-stu-id="77e99-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="77e99-122">Z drugiej strony SignalR połączenie jest *trwałe* .</span><span class="sxs-lookup"><span data-stu-id="77e99-122">On the other hand, a SignalR connection is *persistent* .</span></span> <span data-ttu-id="77e99-123">SignalR połączenia pozostają otwarte nawet wtedy, gdy klient przejdzie w stan bezczynności.</span><span class="sxs-lookup"><span data-stu-id="77e99-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="77e99-124">W aplikacji o dużym natężeniu ruchu, która obsługuje wielu klientów, te trwałe połączenia mogą spowodować, że serwery osiągnął maksymalną liczbę połączeń.</span><span class="sxs-lookup"><span data-stu-id="77e99-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="77e99-125">Połączenia trwałe zużywają także dodatkową pamięć, aby śledzić każde połączenie.</span><span class="sxs-lookup"><span data-stu-id="77e99-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="77e99-126">Duże wykorzystanie zasobów związanych z połączeniami przez SignalR program może mieć wpływ na inne aplikacje sieci Web, które są hostowane na tym samym serwerze.</span><span class="sxs-lookup"><span data-stu-id="77e99-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="77e99-127">W przypadku SignalR otwarcia i przechowywania ostatnich dostępnych połączeń TCP inne aplikacje sieci Web na tym samym serwerze również nie będą miały dostępnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="77e99-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="77e99-128">Jeśli na serwerze wykorzystano połączenia, zobaczysz losowe błędy gniazda i błędy resetowania połączenia.</span><span class="sxs-lookup"><span data-stu-id="77e99-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="77e99-129">Przykład:</span><span class="sxs-lookup"><span data-stu-id="77e99-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="77e99-130">Aby zapobiec SignalR wykorzystaniu przez zasoby błędów w innych aplikacjach sieci Web, uruchom SignalR na różnych serwerach niż inne aplikacje sieci Web.</span><span class="sxs-lookup"><span data-stu-id="77e99-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="77e99-131">Aby zapobiec SignalR wykorzystaniu przez zasoby błędów w SignalR aplikacji, Przeskaluj w poziomie, aby ograniczyć liczbę połączeń, które serwer musi obsłużyć.</span><span class="sxs-lookup"><span data-stu-id="77e99-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="77e99-132">Skalowanie w poziomie</span><span class="sxs-lookup"><span data-stu-id="77e99-132">Scale out</span></span>

<span data-ttu-id="77e99-133">Aplikacja, która korzysta SignalR z programu, musi śledzić wszystkie połączenia, które tworzą problemy dla farmy serwerów.</span><span class="sxs-lookup"><span data-stu-id="77e99-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="77e99-134">Dodaj serwer i pobiera nowe połączenia, o których nie wie inne serwery.</span><span class="sxs-lookup"><span data-stu-id="77e99-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="77e99-135">Na przykład SignalR na każdym serwerze na poniższym diagramie nie można wypróbować połączeń na innych serwerach.</span><span class="sxs-lookup"><span data-stu-id="77e99-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="77e99-136">Gdy SignalR na jednym z serwerów chce wysłać komunikat do wszystkich klientów, komunikat zostanie przekierowany do klientów podłączonych do tego serwera.</span><span class="sxs-lookup"><span data-stu-id="77e99-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Skalowanie::: No-Loc (sygnalizujący)::: bez planu](scale/_static/scale-no-backplane.png)

<span data-ttu-id="77e99-138">Opcjami rozwiązywania tego problemu jest [ SignalR usługa platformy Azure](#azure-signalr-service) i [Redis plan](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="77e99-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="77e99-139">Usługa platformy Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="77e99-139">Azure SignalR Service</span></span>

<span data-ttu-id="77e99-140">Usługa platformy Azure SignalR to serwer proxy, a nie plan.</span><span class="sxs-lookup"><span data-stu-id="77e99-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="77e99-141">Za każdym razem, gdy klient inicjuje połączenie z serwerem, klient zostaje przekierowany do programu w celu nawiązania połączenia z usługą.</span><span class="sxs-lookup"><span data-stu-id="77e99-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="77e99-142">Ten proces przedstawiono na poniższym diagramie:</span><span class="sxs-lookup"><span data-stu-id="77e99-142">That process is illustrated in the following diagram:</span></span>

![Nawiązywanie połączenia z platformą Azure::: No-Loc (Sygnalizującer)::: Service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="77e99-144">Wynika to z tego, że usługa zarządza wszystkimi połączeniami klientów, natomiast każdy serwer potrzebuje tylko małej stałej liczby połączeń z usługą, jak pokazano na poniższym diagramie:</span><span class="sxs-lookup"><span data-stu-id="77e99-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Klienci połączeni z usługą, serwery połączone z usługą](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="77e99-146">Takie podejście do skalowania w poziomie ma kilka korzyści w porównaniu z Redisą dla planu wieloplanowego:</span><span class="sxs-lookup"><span data-stu-id="77e99-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="77e99-147">Sesje programu Sticky Notes, nazywane również [koligacją klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), nie są wymagane, ponieważ klienci są natychmiast przekierowywani do SignalR usługi platformy Azure po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="77e99-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="77e99-148">SignalRAplikacja może skalować w poziomie na podstawie liczby wysłanych komunikatów, natomiast SignalR usługa Azure skaluje się do obsługi dowolnej liczby połączeń.</span><span class="sxs-lookup"><span data-stu-id="77e99-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="77e99-149">Na przykład mogą istnieć tysiące klientów, ale jeśli są wysyłane tylko kilka komunikatów na sekundę, SignalR aplikacja nie będzie musiała skalować w poziomie do wielu serwerów, aby obsługiwać same połączenia.</span><span class="sxs-lookup"><span data-stu-id="77e99-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="77e99-150">SignalRAplikacja nie będzie używać znacznie większej liczby zasobów połączenia niż aplikacja internetowa bez SignalR .</span><span class="sxs-lookup"><span data-stu-id="77e99-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="77e99-151">Z tego względu zalecamy korzystanie z usługi platformy Azure SignalR dla wszystkich ASP.NET Core SignalR aplikacji hostowanych na platformie Azure, w tym App Service, maszyn wirtualnych i kontenerów.</span><span class="sxs-lookup"><span data-stu-id="77e99-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="77e99-152">Aby uzyskać więcej informacji, [Zobacz SignalR dokumentację usługi platformy Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="77e99-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="77e99-153">Płyta montażowa Redis</span><span class="sxs-lookup"><span data-stu-id="77e99-153">Redis backplane</span></span>

<span data-ttu-id="77e99-154">[Redis](https://redis.io/) to magazyn kluczy w pamięci, który obsługuje system obsługi komunikatów z modelem publikowania/subskrybowania.</span><span class="sxs-lookup"><span data-stu-id="77e99-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="77e99-155">SignalRPlan Redis używa funkcji pub/sub do przesyłania dalej komunikatów do innych serwerów.</span><span class="sxs-lookup"><span data-stu-id="77e99-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="77e99-156">Gdy klient nawiązuje połączenie, informacje o połączeniu są przesyłane do planu.</span><span class="sxs-lookup"><span data-stu-id="77e99-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="77e99-157">Gdy serwer chce wysłać komunikat do wszystkich klientów, wysyła do planu.</span><span class="sxs-lookup"><span data-stu-id="77e99-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="77e99-158">W planie dla wszystkich podłączonych klientów i serwerów, na których się znajdują.</span><span class="sxs-lookup"><span data-stu-id="77e99-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="77e99-159">Wysyła komunikat do wszystkich klientów za pośrednictwem odpowiednich serwerów.</span><span class="sxs-lookup"><span data-stu-id="77e99-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="77e99-160">Ten proces przedstawiono na poniższym diagramie:</span><span class="sxs-lookup"><span data-stu-id="77e99-160">This process is illustrated in the following diagram:</span></span>

![Redis, wiadomości wysyłane z jednego serwera do wszystkich klientów](scale/_static/redis-backplane.png)

<span data-ttu-id="77e99-162">Plan Redis to zalecane podejście skalowalne w poziomie dla aplikacji hostowanych we własnej infrastrukturze.</span><span class="sxs-lookup"><span data-stu-id="77e99-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="77e99-163">Jeśli istnieje duże opóźnienie połączenia między centrum danych i centrum danych platformy Azure, SignalR usługa Azure może nie być praktyczną opcją dla aplikacji lokalnych z małymi opóźnieniami lub z wymaganiami dotyczącymi dużej przepływności.</span><span class="sxs-lookup"><span data-stu-id="77e99-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="77e99-164">SignalRWymienione wcześniej zalety usługi platformy Azure są niekorzystne dla planu Redis:</span><span class="sxs-lookup"><span data-stu-id="77e99-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="77e99-165">Wymagane są sesje programu Sticky Notes, znane także jako [koligacja klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), z wyjątkiem sytuacji, gdy spełnione są **obie** następujące sytuacje:</span><span class="sxs-lookup"><span data-stu-id="77e99-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="77e99-166">Wszyscy klienci są skonfigurowani do korzystania **tylko** z obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="77e99-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="77e99-167">[Ustawienie SkipNegotiation](xref:signalr/configuration#configure-additional-options) jest włączone w konfiguracji klienta.</span><span class="sxs-lookup"><span data-stu-id="77e99-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="77e99-168">Po zainicjowaniu połączenia na serwerze połączenie musi pozostać na tym serwerze.</span><span class="sxs-lookup"><span data-stu-id="77e99-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="77e99-169">SignalRAplikacja musi być skalowana w poziomie na podstawie liczby klientów, nawet jeśli jest wysyłanych kilka komunikatów.</span><span class="sxs-lookup"><span data-stu-id="77e99-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="77e99-170">SignalRAplikacja używa znacznie większej liczby zasobów połączenia niż aplikacja internetowa bez SignalR .</span><span class="sxs-lookup"><span data-stu-id="77e99-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="77e99-171">Ograniczenia usług IIS w systemie operacyjnym Windows Client</span><span class="sxs-lookup"><span data-stu-id="77e99-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="77e99-172">Windows 10 i Windows 8. x są systemami operacyjnymi klienta.</span><span class="sxs-lookup"><span data-stu-id="77e99-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="77e99-173">Program IIS w systemach operacyjnych klienta ma limit 10 współbieżnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="77e99-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="77e99-174">SignalRpołączenia:</span><span class="sxs-lookup"><span data-stu-id="77e99-174">SignalR's connections are:</span></span>

* <span data-ttu-id="77e99-175">Przejściowe i często ponownie nawiązane.</span><span class="sxs-lookup"><span data-stu-id="77e99-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="77e99-176">**Nie** usunięto natychmiast, gdy nie jest już używane.</span><span class="sxs-lookup"><span data-stu-id="77e99-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="77e99-177">Powyższe warunki mogą spowodować osiągnięcie 10 limitów połączeń w systemie operacyjnym klienta.</span><span class="sxs-lookup"><span data-stu-id="77e99-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="77e99-178">Gdy system operacyjny klienta jest używany do programowania, zalecamy:</span><span class="sxs-lookup"><span data-stu-id="77e99-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="77e99-179">Należy unikać usług IIS.</span><span class="sxs-lookup"><span data-stu-id="77e99-179">Avoid IIS.</span></span>
* <span data-ttu-id="77e99-180">Użyj Kestrel lub IIS Express jako celów wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="77e99-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="77e99-181">System Linux z serwerem Nginx</span><span class="sxs-lookup"><span data-stu-id="77e99-181">Linux with Nginx</span></span>

<span data-ttu-id="77e99-182">Poniżej znajdują się minimalne wymagane ustawienia umożliwiające włączenie obiektów WebSockets, ServerSentEvents i LongPolling dla SignalR :</span><span class="sxs-lookup"><span data-stu-id="77e99-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for SignalR:</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="77e99-183">Gdy używane są wiele serwerów zaplecza, należy dodać sesje programu Sticky, aby uniemożliwić nawiązywanie SignalR połączeń z serwerów podczas nawiązywania połączenia.</span><span class="sxs-lookup"><span data-stu-id="77e99-183">When multiple backend servers are used, sticky sessions must be added to prevent SignalR connections from switching servers when connecting.</span></span> <span data-ttu-id="77e99-184">Istnieje wiele sposobów dodawania sesji programu Sticky Nginx.</span><span class="sxs-lookup"><span data-stu-id="77e99-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="77e99-185">Poniżej przedstawiono dwa podejścia w zależności od tego, co jest dostępne.</span><span class="sxs-lookup"><span data-stu-id="77e99-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="77e99-186">Dodano następujące elementy oprócz poprzedniej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="77e99-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="77e99-187">W poniższych przykładach `backend` jest nazwą grupy serwerów.</span><span class="sxs-lookup"><span data-stu-id="77e99-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="77e99-188">W programie [Nginx Open Source](https://nginx.org/en/)Użyj metody `ip_hash` do kierowania połączeń do serwera na podstawie adresu IP klienta:</span><span class="sxs-lookup"><span data-stu-id="77e99-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="77e99-189">Za pomocą programu [Nginx Plus](https://www.nginx.com/products/nginx) `sticky` Dodaj cookie do żądania i Przypnij żądania użytkownika do serwera:</span><span class="sxs-lookup"><span data-stu-id="77e99-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a cookie to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="77e99-190">Na koniec przejdź `proxy_pass http://localhost:5000` `server` do sekcji do `proxy_pass http://backend` .</span><span class="sxs-lookup"><span data-stu-id="77e99-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="77e99-191">Aby uzyskać więcej informacji na temat obiektów WebSockets za pośrednictwem usługi Nginx, zobacz [Nginx jako serwer proxy protokołu WebSocket](https://www.nginx.com/blog/websocket-nginx).</span><span class="sxs-lookup"><span data-stu-id="77e99-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="77e99-192">Aby uzyskać więcej informacji o równoważeniu obciążenia i sesjach Nginx, zobacz [równoważenie obciążenia sieciowego](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span><span class="sxs-lookup"><span data-stu-id="77e99-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="77e99-193">Aby uzyskać więcej informacji na temat ASP.NET Core za pomocą Nginx, zobacz następujący artykuł:</span><span class="sxs-lookup"><span data-stu-id="77e99-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="77e99-194">Dostawcy rozwiązań innych firm SignalR</span><span class="sxs-lookup"><span data-stu-id="77e99-194">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="77e99-195">NCache</span><span class="sxs-lookup"><span data-stu-id="77e99-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="77e99-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="77e99-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="77e99-197">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="77e99-197">Next steps</span></span>

<span data-ttu-id="77e99-198">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="77e99-198">For more information, see the following resources:</span></span>

* [<span data-ttu-id="77e99-199">SignalRDokumentacja usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="77e99-199">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="77e99-200">Konfigurowanie planu Redis</span><span class="sxs-lookup"><span data-stu-id="77e99-200">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
