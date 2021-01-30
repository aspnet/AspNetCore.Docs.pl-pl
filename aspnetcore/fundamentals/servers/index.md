---
title: Implementacje serwera sieci Web w ASP.NET Core
author: rick-anderson
description: Odkryj serwery sieci Web Kestrel i HTTP.sys ASP.NET Core. Dowiedz się, jak wybrać serwer i kiedy używać serwera zwrotnego serwera proxy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 91d1373d764644820d1fac6064ee503e1ef4455c
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057346"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="0c65a-104">Implementacje serwera sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c65a-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="0c65a-105">Autorzy [Dykstra](https://github.com/tdykstra), [Steve Kowalski](https://ardalis.com/), [Stephen](https://twitter.com/halter73), i [Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="0c65a-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="0c65a-106">Aplikacja ASP.NET Core jest uruchamiana z użyciem implementacji serwera HTTP w procesie.</span><span class="sxs-lookup"><span data-stu-id="0c65a-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="0c65a-107">Implementacja serwera nasłuchuje żądań HTTP i umieszcza je w aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="0c65a-108">Windows</span><span class="sxs-lookup"><span data-stu-id="0c65a-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="0c65a-109">ASP.NET Core dostarcza następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="0c65a-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="0c65a-110">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http.</span><span class="sxs-lookup"><span data-stu-id="0c65a-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="0c65a-111">Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji, `Http.Sys` takich jak Udostępnianie portów.</span><span class="sxs-lookup"><span data-stu-id="0c65a-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in `Http.Sys` such as port sharing.</span></span>
* <span data-ttu-id="0c65a-112">Serwer HTTP usług IIS jest [serwerem w procesie](#hosting-models) dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-112">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="0c65a-113">[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="0c65a-113">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="0c65a-114">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)uruchomiona jest aplikacja:</span><span class="sxs-lookup"><span data-stu-id="0c65a-114">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="0c65a-115">W tym samym procesie co proces roboczy usług IIS ( [model hostingu w procesie](#hosting-models)) z serwerem HTTP usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-115">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="0c65a-116">*W procesie* jest zalecana konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="0c65a-116">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="0c65a-117">W procesie innym niż proces roboczy usług IIS ( [model hostingu poza procesem](#hosting-models)) z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="0c65a-117">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="0c65a-118">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest natywnym modułem usług IIS, który obsługuje natywne żądania usług IIS między usługami IIS a serwerem HTTP lub Kestrel IIS w procesie.</span><span class="sxs-lookup"><span data-stu-id="0c65a-118">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="0c65a-119">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="0c65a-119">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="0c65a-120">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="0c65a-120">Hosting models</span></span>

<span data-ttu-id="0c65a-121">Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-121">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="0c65a-122">Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.</span><span class="sxs-lookup"><span data-stu-id="0c65a-122">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="0c65a-123">Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0c65a-123">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0c65a-124">Korzystając z hostingu poza procesem, ASP.NET Core aplikacje są uruchamiane w procesie oddzielonym od procesu roboczego usług IIS, a moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="0c65a-124">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="0c65a-125">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="0c65a-125">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="0c65a-126">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0c65a-126">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0c65a-127">Aby uzyskać więcej informacji i wskazówki dotyczące konfiguracji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="0c65a-127">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="0c65a-128">macOS</span><span class="sxs-lookup"><span data-stu-id="0c65a-128">macOS</span></span>](#tab/macos)

<span data-ttu-id="0c65a-129">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="0c65a-129">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="0c65a-130">Linux</span><span class="sxs-lookup"><span data-stu-id="0c65a-130">Linux</span></span>](#tab/linux)

<span data-ttu-id="0c65a-131">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="0c65a-131">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="0c65a-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0c65a-132">Kestrel</span></span>

 <span data-ttu-id="0c65a-133">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http.</span><span class="sxs-lookup"><span data-stu-id="0c65a-133">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="0c65a-134">Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji, `Http.Sys` takich jak Udostępnianie portów.</span><span class="sxs-lookup"><span data-stu-id="0c65a-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in `Http.Sys` such as port sharing.</span></span>
 
<span data-ttu-id="0c65a-135">Użyj Kestrel:</span><span class="sxs-lookup"><span data-stu-id="0c65a-135">Use Kestrel:</span></span>

* <span data-ttu-id="0c65a-136">Jako serwer graniczny przetwarza żądania bezpośrednio z sieci, w tym z Internetu.</span><span class="sxs-lookup"><span data-stu-id="0c65a-136">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="0c65a-138">*Serwer proxy zwrotnego*, taki jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0c65a-138">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="0c65a-139">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c65a-139">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="0c65a-141">&mdash;Obsługiwana jest konfiguracja hostingu z serwerem zwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="0c65a-141">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="0c65a-142">Aby uzyskać wskazówki dotyczące konfiguracji Kestrel i informacje o tym, kiedy używać Kestrel w konfiguracji zwrotnego serwera proxy, zobacz <xref:fundamentals/servers/kestrel> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-142">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="0c65a-143">Windows</span><span class="sxs-lookup"><span data-stu-id="0c65a-143">Windows</span></span>](#tab/windows)

<span data-ttu-id="0c65a-144">ASP.NET Core dostarcza następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="0c65a-144">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="0c65a-145">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="0c65a-145">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="0c65a-146">[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="0c65a-146">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="0c65a-147">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja działa w procesie innym niż proces roboczy usług IIS (*poza procesem*) z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="0c65a-147">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="0c65a-148">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="0c65a-148">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="0c65a-149">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="0c65a-149">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="0c65a-150">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0c65a-150">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0c65a-151">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="0c65a-151">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="0c65a-153">Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="0c65a-153">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="0c65a-154">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="0c65a-154">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="0c65a-155">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="0c65a-155">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="0c65a-156">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="0c65a-156">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="0c65a-157">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="0c65a-157">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="0c65a-158">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-158">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="0c65a-159">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="0c65a-159">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="0c65a-160">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c65a-160">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="0c65a-161">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c65a-161">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="0c65a-162">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="0c65a-162">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="0c65a-163">Aby uzyskać wskazówki dotyczące konfiguracji modułu usług IIS i ASP.NET Core, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="0c65a-163">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="0c65a-164">macOS</span><span class="sxs-lookup"><span data-stu-id="0c65a-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="0c65a-165">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="0c65a-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="0c65a-166">Linux</span><span class="sxs-lookup"><span data-stu-id="0c65a-166">Linux</span></span>](#tab/linux)

<span data-ttu-id="0c65a-167">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="0c65a-167">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="0c65a-168">Nginx z Kestrel</span><span class="sxs-lookup"><span data-stu-id="0c65a-168">Nginx with Kestrel</span></span>

<span data-ttu-id="0c65a-169">Aby uzyskać informacje na temat używania Nginx w systemie Linux jako serwera zwrotnego proxy dla Kestrel, zobacz <xref:host-and-deploy/linux-nginx> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-169">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="0c65a-170">Apache z Kestrel</span><span class="sxs-lookup"><span data-stu-id="0c65a-170">Apache with Kestrel</span></span>

<span data-ttu-id="0c65a-171">Aby uzyskać informacje na temat korzystania z usługi Apache w systemie Linux jako serwera zwrotnego proxy dla usługi Kestrel, zobacz <xref:host-and-deploy/linux-apache> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-171">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="0c65a-172">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0c65a-172">HTTP.sys</span></span>

<span data-ttu-id="0c65a-173">Jeśli ASP.NET Core aplikacje są uruchamiane w systemie Windows, HTTP.sys jest alternatywą dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c65a-173">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="0c65a-174">Kestrel jest zalecane w stosunku do HTTP.sys, chyba że aplikacja wymaga funkcji niedostępnych w Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c65a-174">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="0c65a-175">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="0c65a-175">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys komunikują się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="0c65a-177">HTTP.sys można również używać dla aplikacji, które są dostępne tylko w sieci wewnętrznej.</span><span class="sxs-lookup"><span data-stu-id="0c65a-177">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="0c65a-179">Aby uzyskać HTTP.sys wskazówki dotyczące konfiguracji, zobacz <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-179">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="0c65a-180">Infrastruktura serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c65a-180">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="0c65a-181"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Dostępne w `Startup.Configure` metodzie uwidacznia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> Właściwość typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-181">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="0c65a-182">Kestrel i HTTP.sys uwidaczniają tylko jedną funkcję, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ale różne implementacje serwera mogą uwidaczniać dodatkowe funkcje.</span><span class="sxs-lookup"><span data-stu-id="0c65a-182">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="0c65a-183">`IServerAddressesFeature` można go użyć, aby dowiedzieć się, który port implementacji serwera w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="0c65a-183">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="0c65a-184">Serwery niestandardowe</span><span class="sxs-lookup"><span data-stu-id="0c65a-184">Custom servers</span></span>

<span data-ttu-id="0c65a-185">Jeśli wbudowane serwery nie spełniają wymagań aplikacji, można utworzyć niestandardową implementację serwera.</span><span class="sxs-lookup"><span data-stu-id="0c65a-185">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="0c65a-186">W [przewodniku otwierania interfejsu sieci Web dla platformy .NET (Owin)](xref:fundamentals/owin) przedstawiono sposób pisania implementacji opartej na [nowin](https://github.com/Bobris/Nowin) <xref:Microsoft.AspNetCore.Hosting.Server.IServer> .</span><span class="sxs-lookup"><span data-stu-id="0c65a-186">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="0c65a-187">Tylko interfejsy funkcji używane przez aplikację wymagają implementacji, ale <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> muszą być obsługiwane w minimalnym i <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> wymaganym czasie.</span><span class="sxs-lookup"><span data-stu-id="0c65a-187">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="0c65a-188">Uruchamianie serwera</span><span class="sxs-lookup"><span data-stu-id="0c65a-188">Server startup</span></span>

<span data-ttu-id="0c65a-189">Serwer jest uruchamiany po uruchomieniu aplikacji zintegrowanego środowiska programistycznego (IDE) lub edytora:</span><span class="sxs-lookup"><span data-stu-id="0c65a-189">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="0c65a-190">[Visual Studio](https://visualstudio.microsoft.com): profile uruchamiania mogą służyć do uruchamiania aplikacji i serwera za pomocą [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) lub konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="0c65a-190">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="0c65a-191">[Visual Studio Code](https://code.visualstudio.com/): aplikacja i serwer są uruchamiane przez [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), która aktywuje debuger CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="0c65a-191">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="0c65a-192">[Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/): aplikacja i serwer są uruchamiane za pomocą [debugera Soft-Mode mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="0c65a-192">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="0c65a-193">Podczas uruchamiania aplikacji z poziomu wiersza polecenia w folderze projektu, [uruchomienie dotnet](/dotnet/core/tools/dotnet-run) uruchamia aplikację i serwer (tylko Kestrel i HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="0c65a-193">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="0c65a-194">Konfiguracja jest określana przez `-c|--configuration` opcję, która jest ustawiona na wartość `Debug` (domyślnie) lub `Release` .</span><span class="sxs-lookup"><span data-stu-id="0c65a-194">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="0c65a-195">*launchSettings.jsw* pliku zapewnia konfigurację podczas uruchamiania aplikacji przy użyciu `dotnet run` debugera wbudowanego w narzędzia, takiego jak Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c65a-195">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="0c65a-196">Jeśli profile uruchamiania są obecne w *launchSettings.jsw* pliku, użyj `--launch-profile {PROFILE NAME}` opcji z `dotnet run` poleceniem lub wybierz profil w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c65a-196">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="0c65a-197">Aby uzyskać więcej informacji, [](/dotnet/core/tools/dotnet-run) zobacz [pakietem rozkładu dotnet i .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="0c65a-197">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="0c65a-198">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="0c65a-198">HTTP/2 support</span></span>

<span data-ttu-id="0c65a-199">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania:</span><span class="sxs-lookup"><span data-stu-id="0c65a-199">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="0c65a-200">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0c65a-200">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="0c65a-201">System operacyjny</span><span class="sxs-lookup"><span data-stu-id="0c65a-201">Operating system</span></span>
    * <span data-ttu-id="0c65a-202">Windows Server 2016/Windows 10 lub nowszy&dagger;</span><span class="sxs-lookup"><span data-stu-id="0c65a-202">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="0c65a-203">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="0c65a-203">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="0c65a-204">Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="0c65a-204">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="0c65a-205">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-205">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0c65a-206">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0c65a-206">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="0c65a-207">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-207">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="0c65a-208">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0c65a-208">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="0c65a-209">Usługi IIS (w procesie)</span><span class="sxs-lookup"><span data-stu-id="0c65a-209">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0c65a-210">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-210">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0c65a-211">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-211">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0c65a-212">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="0c65a-212">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0c65a-213">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-213">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0c65a-214">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0c65a-214">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="0c65a-215">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-215">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="0c65a-216">&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="0c65a-216">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0c65a-217">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="0c65a-217">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0c65a-218">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="0c65a-218">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="0c65a-219">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0c65a-219">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="0c65a-220">System operacyjny</span><span class="sxs-lookup"><span data-stu-id="0c65a-220">Operating system</span></span>
    * <span data-ttu-id="0c65a-221">Windows Server 2016/Windows 10 lub nowszy&dagger;</span><span class="sxs-lookup"><span data-stu-id="0c65a-221">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="0c65a-222">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="0c65a-222">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="0c65a-223">Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="0c65a-223">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="0c65a-224">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-224">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0c65a-225">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0c65a-225">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="0c65a-226">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-226">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="0c65a-227">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0c65a-227">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="0c65a-228">Usługi IIS (w procesie)</span><span class="sxs-lookup"><span data-stu-id="0c65a-228">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0c65a-229">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0c65a-230">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-230">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0c65a-231">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="0c65a-231">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0c65a-232">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-232">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0c65a-233">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0c65a-233">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="0c65a-234">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-234">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="0c65a-235">&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="0c65a-235">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0c65a-236">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="0c65a-236">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0c65a-237">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="0c65a-237">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="0c65a-238">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0c65a-238">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="0c65a-239">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-239">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="0c65a-240">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0c65a-240">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="0c65a-241">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="0c65a-241">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0c65a-242">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="0c65a-242">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0c65a-243">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0c65a-243">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="0c65a-244">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0c65a-244">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="0c65a-245">Połączenie HTTP/2 musi korzystać z [negocjacji protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) i TLS 1,2 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="0c65a-245">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="0c65a-246">Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi scenariuszy wdrażania serwera.</span><span class="sxs-lookup"><span data-stu-id="0c65a-246">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c65a-247">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0c65a-247">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
