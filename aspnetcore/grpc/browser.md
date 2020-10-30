---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby możliwe było wywoływanie z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058912"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="9b57f-103">Używanie gRPC w aplikacjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="9b57f-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="9b57f-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9b57f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="9b57f-105">Dowiedz się, jak skonfigurować istniejącą ASP.NET Core usługę gRPC, która ma być wywoływana z aplikacji przeglądarki, przy użyciu protokołu [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) .</span><span class="sxs-lookup"><span data-stu-id="9b57f-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="9b57f-106">gRPC-Web umożliwia przeglądarce JavaScript i :::no-loc(Blazor)::: aplikacjom wywoływanie usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="9b57f-106">gRPC-Web allows browser JavaScript and :::no-loc(Blazor)::: apps to call gRPC services.</span></span> <span data-ttu-id="9b57f-107">Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="9b57f-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="9b57f-108">usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9b57f-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="9b57f-109">Aby uzyskać instrukcje dotyczące dodawania usługi gRPC do istniejącej aplikacji ASP.NET Core, zobacz [Dodawanie usług gRPC do aplikacji ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="9b57f-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="9b57f-110">Instrukcje dotyczące tworzenia projektu gRPC można znaleźć w temacie <xref:tutorials/grpc/grpc-start> .</span><span class="sxs-lookup"><span data-stu-id="9b57f-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="9b57f-111">gRPC — sieć Web w ASP.NET Core a wysłannika</span><span class="sxs-lookup"><span data-stu-id="9b57f-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="9b57f-112">Dostępne są dwa sposoby dodawania usługi gRPC-Web do aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9b57f-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="9b57f-113">Obsługa gRPC-Web i gRPC protokołu HTTP/2 w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b57f-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="9b57f-114">Ta opcja powoduje użycie oprogramowania pośredniczącego dostarczonego przez `Grpc.AspNetCore.Web` pakiet.</span><span class="sxs-lookup"><span data-stu-id="9b57f-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="9b57f-115">Użyj funkcji gRPC-Web [wysłannika serwera proxy](https://www.envoyproxy.io/) , aby przetłumaczyć GRPC-Web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="9b57f-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="9b57f-116">Przetłumaczone wywołanie jest następnie przekazywane do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b57f-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="9b57f-117">Każde podejście ma pewne wady i zalety.</span><span class="sxs-lookup"><span data-stu-id="9b57f-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="9b57f-118">Jeśli środowisko aplikacji korzysta już z wysłannika jako serwera proxy, warto również użyć wysłannika do zapewnienia obsługi gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9b57f-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="9b57f-119">W przypadku rozwiązania Basic for gRPC-Web, które wymaga tylko ASP.NET Core, `Grpc.AspNetCore.Web` to dobry wybór.</span><span class="sxs-lookup"><span data-stu-id="9b57f-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="9b57f-120">Konfigurowanie gRPC-sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b57f-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="9b57f-121">usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9b57f-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="9b57f-122">gRPC — sieć Web nie wymaga żadnych zmian w usługach.</span><span class="sxs-lookup"><span data-stu-id="9b57f-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="9b57f-123">Jedyną modyfikacją jest konfiguracja uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="9b57f-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="9b57f-124">Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9b57f-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="9b57f-125">Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="9b57f-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="9b57f-126">Skonfiguruj aplikację do używania gRPC-Web, dodając `UseGrpcWeb` i `EnableGrpcWeb` do *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9b57f-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs* :</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="9b57f-127">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9b57f-127">The preceding code:</span></span>

* <span data-ttu-id="9b57f-128">Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb` po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="9b57f-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="9b57f-129">Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje gRPC-Web with `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="9b57f-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="9b57f-130">Alternatywnie można skonfigurować oprogramowanie pośredniczące gRPC-Web, tak aby wszystkie usługi obsługiwały usługę gRPC-Web domyślnie i `EnableGrpcWeb` nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="9b57f-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="9b57f-131">Określ `new GrpcWebOptions { DefaultEnabled = true }` , kiedy zostanie dodane oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="9b57f-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="9b57f-132">Występuje znany problem, który powoduje niepowodzenie gRPC-Web, gdy jest [hostowany przez Http.sys](xref:fundamentals/servers/httpsys) w programie .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="9b57f-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="9b57f-133">Obejście problemu z gRPC-Web Work on Http.sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="9b57f-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="9b57f-134">gRPC — Web i CORS</span><span class="sxs-lookup"><span data-stu-id="9b57f-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="9b57f-135">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9b57f-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="9b57f-136">To ograniczenie dotyczy tworzenia połączeń sieci Web gRPC z aplikacjami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9b57f-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="9b57f-137">Na przykład aplikacja przeglądarki obsługiwana przez program `https://www.contoso.com` ma zablokowany dostęp do usług gRPC — sieci Web hostowanych w systemie `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="9b57f-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="9b57f-138">Aby osłabić to ograniczenie, można użyć funkcji udostępniania zasobów między źródłami (CORS).</span><span class="sxs-lookup"><span data-stu-id="9b57f-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="9b57f-139">Aby zezwolić aplikacji przeglądarki na wykonywanie wywołań sieci Web między źródłami gRPC, skonfiguruj mechanizm [CORS w ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="9b57f-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="9b57f-140">Użyj wbudowanej obsługi mechanizmu CORS i Uwidocznij nagłówki specyficzne dla gRPC za pomocą <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .</span><span class="sxs-lookup"><span data-stu-id="9b57f-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="9b57f-141">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9b57f-141">The preceding code:</span></span>

* <span data-ttu-id="9b57f-142">Wywołania `AddCors` dodawania usług CORS i konfigurowania zasad CORS, które ujawniają nagłówki specyficzne dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b57f-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="9b57f-143">Wywołuje `UseCors` Dodawanie oprogramowania CORS po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="9b57f-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="9b57f-144">Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje mechanizm CORS z `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="9b57f-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="9b57f-145">gRPC — sieć Web i przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="9b57f-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="9b57f-146">Tradycyjna gRPC za pośrednictwem protokołu HTTP/2 obsługuje przesyłanie strumieniowe we wszystkich kierunkach.</span><span class="sxs-lookup"><span data-stu-id="9b57f-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="9b57f-147">gRPC-Web oferuje ograniczoną obsługę przesyłania strumieniowego:</span><span class="sxs-lookup"><span data-stu-id="9b57f-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="9b57f-148">gRPC — klienci korzystający z przeglądarki sieci Web nie obsługują wywoływania przesyłania strumieniowego i dwukierunkowego przesyłania strumieniowego klienta.</span><span class="sxs-lookup"><span data-stu-id="9b57f-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="9b57f-149">ASP.NET Core usługi gRPC Services hostowane na Azure App Service i usługach IIS nie obsługują przesyłania strumieniowego dwukierunkowego.</span><span class="sxs-lookup"><span data-stu-id="9b57f-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="9b57f-150">W przypadku korzystania z usługi gRPC-Web zalecamy użycie metod jednoargumentowych i metod przesyłania strumieniowego serwera.</span><span class="sxs-lookup"><span data-stu-id="9b57f-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="9b57f-151">Wywołaj gRPC-Web z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="9b57f-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="9b57f-152">Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="9b57f-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="9b57f-153">Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="9b57f-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="9b57f-154">Serwer musi być skonfigurowany do obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="9b57f-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="9b57f-155">Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9b57f-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="9b57f-156">Przesyłanie strumieniowe serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9b57f-156">Server streaming is supported.</span></span>
* <span data-ttu-id="9b57f-157">Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="9b57f-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="9b57f-158">JavaScript gRPC — klient sieci Web</span><span class="sxs-lookup"><span data-stu-id="9b57f-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="9b57f-159">Istnieje skrypt JavaScript gRPC-Web Client.</span><span class="sxs-lookup"><span data-stu-id="9b57f-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="9b57f-160">Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="9b57f-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="9b57f-161">Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="9b57f-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="9b57f-162">Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9b57f-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="9b57f-163">Jest to przydatne w przypadku [:::no-loc(Blazor WebAssembly):::](xref:blazor/index#blazor-webassembly) aplikacji, które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b57f-163">This is useful for [:::no-loc(Blazor WebAssembly):::](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="9b57f-164">Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="9b57f-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="9b57f-165">Jedyną modyfikacją jest sposób tworzenia kanału.</span><span class="sxs-lookup"><span data-stu-id="9b57f-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="9b57f-166">Aby użyć gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="9b57f-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="9b57f-167">Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="9b57f-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="9b57f-168">Upewnij się, że odwołanie do [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) Package ma wartość 2.29.0 lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="9b57f-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="9b57f-169">Skonfiguruj kanał, aby używał `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="9b57f-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="9b57f-170">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9b57f-170">The preceding code:</span></span>

* <span data-ttu-id="9b57f-171">Konfiguruje kanał do korzystania z gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9b57f-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="9b57f-172">Tworzy klienta i wywołuje połączenie przy użyciu kanału.</span><span class="sxs-lookup"><span data-stu-id="9b57f-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="9b57f-173">`GrpcWebHandler` dostępne są następujące opcje konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9b57f-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="9b57f-174">**InnerHandler** : podstawowy, <xref:System.Net.Http.HttpMessageHandler> który wysyła żądanie HTTP gRPC, na przykład `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="9b57f-174">**InnerHandler** : The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="9b57f-175">**GrpcWebMode** : typ wyliczeniowy, który określa, czy żądanie HTTP gRPC `Content-Type` ma wartość `application/grpc-web` lub `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="9b57f-175">**GrpcWebMode** : An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="9b57f-176">`GrpcWebMode.GrpcWeb` konfiguruje zawartość do wysłania bez kodowania.</span><span class="sxs-lookup"><span data-stu-id="9b57f-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="9b57f-177">Wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="9b57f-177">Default value.</span></span>
    * <span data-ttu-id="9b57f-178">`GrpcWebMode.GrpcWebText` konfiguruje zawartość do kodowania base64.</span><span class="sxs-lookup"><span data-stu-id="9b57f-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="9b57f-179">Wymagane dla wywołań przesyłania strumieniowego serwera w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="9b57f-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="9b57f-180">**HttpVersion** : protokół http `Version` służący do ustawiania [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) w źródłowym żądaniu HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b57f-180">**HttpVersion** : HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="9b57f-181">gRPC — sieć Web nie wymaga określonej wersji i nie przesłania jej domyślnie, chyba że zostanie określona.</span><span class="sxs-lookup"><span data-stu-id="9b57f-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9b57f-182">Wygenerowane gRPC klienci mają metody synchronizacji i asynchroniczne do wywoływania metod jednoargumentowych.</span><span class="sxs-lookup"><span data-stu-id="9b57f-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="9b57f-183">Na przykład `SayHello` jest synchronizowana i `SayHelloAsync` jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="9b57f-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="9b57f-184">Wywołanie metody synchronizacji w :::no-loc(Blazor WebAssembly)::: aplikacji spowoduje, że aplikacja przestanie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="9b57f-184">Calling a sync method in a :::no-loc(Blazor WebAssembly)::: app will cause the app to become unresponsive.</span></span> <span data-ttu-id="9b57f-185">Metody asynchroniczne muszą być zawsze używane w programie :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="9b57f-185">Async methods must always be used in :::no-loc(Blazor WebAssembly):::.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="9b57f-186">Używanie fabryki klienta gRPC z usługą gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="9b57f-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="9b57f-187">Klienta .NET zgodnego z programem gRPC-Web można utworzyć przy użyciu integracji usługi gRPC z usługą [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="9b57f-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="9b57f-188">Aby użyć gRPC-Web z fabryką klienta:</span><span class="sxs-lookup"><span data-stu-id="9b57f-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="9b57f-189">Dodaj odwołania do pakietu do pliku projektu dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="9b57f-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="9b57f-190">GRPC .NET. Client. Web</span><span class="sxs-lookup"><span data-stu-id="9b57f-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="9b57f-191">GRPC .NET. ClientFactory</span><span class="sxs-lookup"><span data-stu-id="9b57f-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="9b57f-192">Zarejestruj klienta gRPC z iniekcją zależności (DI) przy użyciu uniwersalnej `AddGrpcClient` metody rozszerzania.</span><span class="sxs-lookup"><span data-stu-id="9b57f-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="9b57f-193">W :::no-loc(Blazor WebAssembly)::: aplikacji usługi są zarejestrowane przy użyciu programu "di in" `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="9b57f-193">In a :::no-loc(Blazor WebAssembly)::: app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="9b57f-194">Skonfiguruj `GrpcWebHandler` przy użyciu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="9b57f-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="9b57f-195">Aby uzyskać więcej informacji, zobacz <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="9b57f-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b57f-196">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9b57f-196">Additional resources</span></span>

* [<span data-ttu-id="9b57f-197">gRPC dla klientów sieci Web — projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="9b57f-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
