---
title: Platforma ASP.NET Core — podstawy
author: rick-anderson
description: Poznaj podstawowe koncepcje tworzenia aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 27b182394abe12a1631e5ba350942904bf4094aa
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035895"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="f6116-103">Platforma ASP.NET Core — podstawy</span><span class="sxs-lookup"><span data-stu-id="f6116-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6116-104">Ten artykuł zawiera omówienie najważniejszych tematów dotyczących sposobu tworzenia aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="f6116-105">Klasa Startup</span><span class="sxs-lookup"><span data-stu-id="f6116-105">The Startup class</span></span>

<span data-ttu-id="f6116-106">`Startup`Klasa jest:</span><span class="sxs-lookup"><span data-stu-id="f6116-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="f6116-107">Usługi wymagane przez aplikację są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="f6116-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="f6116-108">Potok obsługi żądań aplikacji jest zdefiniowany jako seria części oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="f6116-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="f6116-109">Oto przykładowa `Startup` Klasa:</span><span class="sxs-lookup"><span data-stu-id="f6116-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="f6116-110">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f6116-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="f6116-111">Wstrzykiwanie zależności (usługi)</span><span class="sxs-lookup"><span data-stu-id="f6116-111">Dependency injection (services)</span></span>

<span data-ttu-id="f6116-112">ASP.NET Core zawiera wbudowaną platformę wstrzykiwania zależności (DI), która udostępnia skonfigurowane usługi w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6116-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="f6116-113">Na przykład składnik rejestrowania to usługa.</span><span class="sxs-lookup"><span data-stu-id="f6116-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="f6116-114">Kod do konfigurowania (lub *rejestrowania*) usług jest dodawany do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="f6116-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f6116-115">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f6116-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="f6116-116">Usługi są zwykle rozwiązywane przez iniekcję konstruktora przy użyciu funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="f6116-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="f6116-117">Z iniekcją konstruktora Klasa deklaruje parametr konstruktora dla wymaganego typu lub interfejsu.</span><span class="sxs-lookup"><span data-stu-id="f6116-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="f6116-118">Program DI Framework oferuje wystąpienie tej usługi w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="f6116-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="f6116-119">W poniższym przykładzie zastosowano iniekcję konstruktora do rozpoznania elementu `RazorPagesMovieContext` z typu di:</span><span class="sxs-lookup"><span data-stu-id="f6116-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="f6116-120">Jeśli wbudowana wersja kontenera kontroli (IoC) nie spełnia wszystkich potrzeb aplikacji, zamiast tego można użyć kontenera IoC innej firmy.</span><span class="sxs-lookup"><span data-stu-id="f6116-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="f6116-121">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f6116-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="f6116-122">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="f6116-122">Middleware</span></span>

<span data-ttu-id="f6116-123">Potok obsługi żądań składa się z serii komponentów oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="f6116-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="f6116-124">Każdy składnik wykonuje operacje na `HttpContext` i wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.</span><span class="sxs-lookup"><span data-stu-id="f6116-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="f6116-125">Zgodnie z Konwencją składnik pośredniczący jest dodawany do potoku przez wywołanie `Use...` metody rozszerzenia w `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f6116-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="f6116-126">Na przykład, aby włączyć renderowanie plików statycznych, wywołaj `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="f6116-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="f6116-127">W poniższym przykładzie zostanie skonfigurowany potok obsługi żądania:</span><span class="sxs-lookup"><span data-stu-id="f6116-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="f6116-128">ASP.NET Core obejmuje bogaty zestaw wbudowanych programów pośredniczących.</span><span class="sxs-lookup"><span data-stu-id="f6116-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="f6116-129">Można również pisać niestandardowe składniki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="f6116-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="f6116-130">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="f6116-131">Host</span><span class="sxs-lookup"><span data-stu-id="f6116-131">Host</span></span>

<span data-ttu-id="f6116-132">Podczas uruchamiania aplikacja ASP.NET Core kompiluje *hosta*.</span><span class="sxs-lookup"><span data-stu-id="f6116-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="f6116-133">Host hermetyzuje wszystkie zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="f6116-134">Implementacja serwera HTTP</span><span class="sxs-lookup"><span data-stu-id="f6116-134">An HTTP server implementation</span></span>
* <span data-ttu-id="f6116-135">Składniki oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="f6116-135">Middleware components</span></span>
* <span data-ttu-id="f6116-136">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="f6116-136">Logging</span></span>
* <span data-ttu-id="f6116-137">Usługi iniekcji zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="f6116-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="f6116-138">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="f6116-138">Configuration</span></span>

<span data-ttu-id="f6116-139">Istnieją dwa różne hosty:</span><span class="sxs-lookup"><span data-stu-id="f6116-139">There are two different hosts:</span></span> 

* <span data-ttu-id="f6116-140">Host ogólny .NET</span><span class="sxs-lookup"><span data-stu-id="f6116-140">.NET Generic Host</span></span>
* <span data-ttu-id="f6116-141">ASP.NET Core hosta sieci Web</span><span class="sxs-lookup"><span data-stu-id="f6116-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="f6116-142">Zalecany jest host ogólny platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="f6116-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="f6116-143">ASP.NET Core host sieci Web jest dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami.</span><span class="sxs-lookup"><span data-stu-id="f6116-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="f6116-144">Poniższy przykład tworzy hosta ogólnego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="f6116-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="f6116-145">`CreateDefaultBuilder`Metody i `ConfigureWebHostDefaults` konfigurują hosta z zestawem opcji domyślnych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="f6116-146">Użyj [Kestrel](#servers) jako serwera sieci Web i Włącz INTEGRACJĘ usług IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="f6116-147">Załaduj konfigurację z pliku *appsettings.json* , *appSettings. { Nazwa środowiska}. JSON*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f6116-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="f6116-148">Wyślij dane wyjściowe rejestrowania do konsoli programu i dostawców debugowania.</span><span class="sxs-lookup"><span data-stu-id="f6116-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="f6116-149">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="f6116-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="f6116-150">Scenariusze inne niż internetowe</span><span class="sxs-lookup"><span data-stu-id="f6116-150">Non-web scenarios</span></span>

<span data-ttu-id="f6116-151">Host ogólny umożliwia innym typom aplikacji korzystanie z rozszerzeń struktury wycinania, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6116-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="f6116-152">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="f6116-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="f6116-153">Serwery</span><span class="sxs-lookup"><span data-stu-id="f6116-153">Servers</span></span>

<span data-ttu-id="f6116-154">Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6116-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="f6116-155">Serwer wyświetla żądania do aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="f6116-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="f6116-156">Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="f6116-157">ASP.NET Core udostępnia następujące implementacje serwera:</span><span class="sxs-lookup"><span data-stu-id="f6116-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f6116-158">*Kestrel* to Międzyplatformowy serwer sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f6116-159">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy za pomocą [usług IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f6116-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f6116-160">W ASP.NET Core 2,0 lub nowszej Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f6116-161">*Serwer http IIS* jest serwerem dla systemu Windows, który korzysta z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="f6116-162">Na tym serwerze aplikacja ASP.NET Core i usługi IIS działają w tym samym procesie.</span><span class="sxs-lookup"><span data-stu-id="f6116-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="f6116-163">*HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="f6116-164">macOS</span><span class="sxs-lookup"><span data-stu-id="f6116-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="f6116-165">ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* .</span><span class="sxs-lookup"><span data-stu-id="f6116-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f6116-166">W ASP.NET Core 2,0 lub nowszej Kestrel może działać jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f6116-167">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f6116-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="f6116-168">Linux</span><span class="sxs-lookup"><span data-stu-id="f6116-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="f6116-169">ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* .</span><span class="sxs-lookup"><span data-stu-id="f6116-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f6116-170">W ASP.NET Core 2,0 lub nowszej Kestrel może działać jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f6116-171">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f6116-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="f6116-172">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="f6116-173">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="f6116-173">Configuration</span></span>

<span data-ttu-id="f6116-174">ASP.NET Core udostępnia platformę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f6116-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="f6116-175">Wbudowani dostawcy konfiguracji są dostępni dla różnych źródeł, takich jak pliki *. JSON* , pliki *. XML* , zmienne środowiskowe i argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f6116-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="f6116-176">Napisz niestandardowych dostawców konfiguracji, aby obsługiwać inne źródła.</span><span class="sxs-lookup"><span data-stu-id="f6116-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="f6116-177">[Domyślnie](xref:fundamentals/configuration/index#default)aplikacje ASP.NET Core są skonfigurowane do odczytywania z *appsettings.json* , zmiennych środowiskowych, wiersza polecenia i nie tylko.</span><span class="sxs-lookup"><span data-stu-id="f6116-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="f6116-178">Po załadowaniu konfiguracji aplikacji wartości z zmiennych środowiskowych zastępują wartości z *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f6116-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="f6116-179">Preferowanym sposobem odczytywania pokrewnych wartości konfiguracji jest użycie [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f6116-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f6116-180">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracyjnych przy użyciu wzorca opcji](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="f6116-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="f6116-181">Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, program .NET Core udostępnia [Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="f6116-181">For managing confidential configuration data such as passwords, .NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="f6116-182">W przypadku wpisów tajnych produkcji zalecamy [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f6116-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="f6116-183">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="f6116-184">Środowiska</span><span class="sxs-lookup"><span data-stu-id="f6116-184">Environments</span></span>

<span data-ttu-id="f6116-185">Środowiska wykonawcze, takie jak `Development` , `Staging` i `Production` , są pojęciem pierwszej klasy w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="f6116-186">Określ środowisko, w którym aplikacja działa, przez ustawienie `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="f6116-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="f6116-187">ASP.NET Core odczytuje tę zmienną środowiskową przy uruchamianiu aplikacji i zapisuje wartość w `IWebHostEnvironment` implementacji.</span><span class="sxs-lookup"><span data-stu-id="f6116-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="f6116-188">Ta implementacja jest dostępna w dowolnym miejscu w aplikacji za pomocą iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="f6116-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="f6116-189">Poniższy przykład służy do konfigurowania aplikacji w celu zapewnienia szczegółowych informacji o błędzie w przypadku uruchamiania w `Development` środowisku:</span><span class="sxs-lookup"><span data-stu-id="f6116-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="f6116-190">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f6116-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="f6116-191">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="f6116-191">Logging</span></span>

<span data-ttu-id="f6116-192">ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm.</span><span class="sxs-lookup"><span data-stu-id="f6116-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f6116-193">Dostępni dostawcy obejmują:</span><span class="sxs-lookup"><span data-stu-id="f6116-193">Available providers include:</span></span>

* <span data-ttu-id="f6116-194">Konsola</span><span class="sxs-lookup"><span data-stu-id="f6116-194">Console</span></span>
* <span data-ttu-id="f6116-195">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f6116-195">Debug</span></span>
* <span data-ttu-id="f6116-196">Śledzenie zdarzeń w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="f6116-197">Dziennik zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-197">Windows Event Log</span></span>
* <span data-ttu-id="f6116-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f6116-198">TraceSource</span></span>
* <span data-ttu-id="f6116-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f6116-199">Azure App Service</span></span>
* <span data-ttu-id="f6116-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f6116-200">Azure Application Insights</span></span>

<span data-ttu-id="f6116-201">Aby utworzyć dzienniki, należy rozwiązać <xref:Microsoft.Extensions.Logging.ILogger%601> usługę od iniekcji zależności (di) i wywoływać metody rejestrowania, takie jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> .</span><span class="sxs-lookup"><span data-stu-id="f6116-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="f6116-202">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f6116-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="f6116-203">Metody rejestrowania, takie jak `LogInformation` Obsługa dowolnej liczby pól.</span><span class="sxs-lookup"><span data-stu-id="f6116-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="f6116-204">Te pola są często używane do konstruowania komunikatu `string` , ale niektórzy dostawcy rejestrowania wysyłają je do magazynu danych jako osobne pola.</span><span class="sxs-lookup"><span data-stu-id="f6116-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="f6116-205">Ta funkcja umożliwia dostawcom rejestrowania implementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f6116-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f6116-206">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="f6116-207">Routing</span><span class="sxs-lookup"><span data-stu-id="f6116-207">Routing</span></span>

<span data-ttu-id="f6116-208">*Trasa* jest WZORCEM adresu URL, który jest mapowany do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="f6116-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="f6116-209">Procedura obsługi jest zazwyczaj Razor stroną, metodą akcji w KONTROLERZE MVC lub w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="f6116-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="f6116-210">Routing ASP.NET Core zapewnia kontrolę nad adresami URL używanymi przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="f6116-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="f6116-211">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f6116-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="f6116-212">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="f6116-212">Error handling</span></span>

<span data-ttu-id="f6116-213">ASP.NET Core ma wbudowane funkcje do obsługi błędów, takie jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="f6116-214">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="f6116-214">A developer exception page</span></span>
* <span data-ttu-id="f6116-215">Niestandardowe strony błędów</span><span class="sxs-lookup"><span data-stu-id="f6116-215">Custom error pages</span></span>
* <span data-ttu-id="f6116-216">Statyczne strony kodów stanu</span><span class="sxs-lookup"><span data-stu-id="f6116-216">Static status code pages</span></span>
* <span data-ttu-id="f6116-217">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="f6116-217">Startup exception handling</span></span>

<span data-ttu-id="f6116-218">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="f6116-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="f6116-219">Zgłaszanie żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="f6116-219">Make HTTP requests</span></span>

<span data-ttu-id="f6116-220">Implementacja programu `IHttpClientFactory` jest dostępna do tworzenia `HttpClient` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="f6116-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="f6116-221">Fabryka:</span><span class="sxs-lookup"><span data-stu-id="f6116-221">The factory:</span></span>

* <span data-ttu-id="f6116-222">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="f6116-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f6116-223">Na przykład zarejestruj i Skonfiguruj klienta usługi *GitHub* do uzyskiwania dostępu do serwisu GitHub.</span><span class="sxs-lookup"><span data-stu-id="f6116-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="f6116-224">Zarejestruj i skonfiguruj domyślnego klienta do innych celów.</span><span class="sxs-lookup"><span data-stu-id="f6116-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="f6116-225">Obsługuje rejestrację i łańcuch wielu procedur delegowania, aby utworzyć potok pośredniczący żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="f6116-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f6116-226">Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="f6116-227">Wzorzec zapewnia mechanizm zarządzania założeniami krzyżowymi dla żądań HTTP, takich jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="f6116-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="f6116-228">Integruje się z usługą *Polly*, popularną biblioteką innej firmy na potrzeby obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="f6116-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="f6116-229">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS, które występują podczas `HttpClient` ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="f6116-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="f6116-230">Dodaje konfigurowalne środowisko rejestrowania za pośrednictwem <xref:Microsoft.Extensions.Logging.ILogger> programu dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="f6116-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f6116-231">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="f6116-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="f6116-232">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="f6116-232">Content root</span></span>

<span data-ttu-id="f6116-233">Katalog główny zawartości jest ścieżką podstawową dla:</span><span class="sxs-lookup"><span data-stu-id="f6116-233">The content root is the base path for:</span></span>

* <span data-ttu-id="f6116-234">Plik wykonywalny obsługujący aplikację (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="f6116-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="f6116-235">Skompilowane zestawy wchodzące w skład aplikacji (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="f6116-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="f6116-236">Pliki zawartości używane przez aplikację, takie jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="f6116-237">Razor pliki (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="f6116-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="f6116-238">Pliki konfiguracji (*. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="f6116-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="f6116-239">Pliki danych (*. DB*)</span><span class="sxs-lookup"><span data-stu-id="f6116-239">Data files (*.db*)</span></span>
* <span data-ttu-id="f6116-240">[Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="f6116-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="f6116-241">Podczas opracowywania, główny element zawartości domyślnie jest katalogiem głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="f6116-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="f6116-242">Ten katalog jest również ścieżką podstawową dla plików zawartości aplikacji i [katalogu głównego sieci Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="f6116-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="f6116-243">Określ inny katalog główny zawartości, ustawiając jego ścieżkę podczas [kompilowania hosta](#host).</span><span class="sxs-lookup"><span data-stu-id="f6116-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="f6116-244">Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="f6116-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="f6116-245">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="f6116-245">Web root</span></span>

<span data-ttu-id="f6116-246">Katalog główny sieci Web jest ścieżką podstawową dla publicznych, statycznych plików zasobów, takich jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="f6116-247">Arkusze stylów (*. css*)</span><span class="sxs-lookup"><span data-stu-id="f6116-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="f6116-248">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="f6116-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="f6116-249">Obrazy (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="f6116-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="f6116-250">Domyślnie pliki statyczne są obsługiwane tylko z katalogu głównego sieci Web i jego podkatalogów.</span><span class="sxs-lookup"><span data-stu-id="f6116-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="f6116-251">Domyślna ścieżka katalogu głównego sieci Web to *{Content root}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f6116-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="f6116-252">Określ inny katalog główny sieci Web przez ustawienie jego ścieżki podczas [kompilowania hosta](#host).</span><span class="sxs-lookup"><span data-stu-id="f6116-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="f6116-253">Aby uzyskać więcej informacji, zobacz [katalog główny sieci Web](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="f6116-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="f6116-254">Zapobiegaj publikowaniu plików w pliku *wwwroot* przy użyciu [ \<Content> elementu projektu](/visualstudio/msbuild/common-msbuild-project-items#content) w projekcie.</span><span class="sxs-lookup"><span data-stu-id="f6116-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="f6116-255">Poniższy przykład uniemożliwia opublikowanie zawartości w katalogu *wwwroot/lokalnym* i jego podkatalogach:</span><span class="sxs-lookup"><span data-stu-id="f6116-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="f6116-256">W Razor plikach *. cshtml* , ukośnika ( `~/` ) wskazuje na katalog główny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="f6116-257">Ścieżka rozpoczynająca `~/` się od jest nazywana *ścieżką wirtualną*.</span><span class="sxs-lookup"><span data-stu-id="f6116-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="f6116-258">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="f6116-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6116-259">Ten artykuł zawiera omówienie najważniejszych tematów dotyczących sposobu tworzenia aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="f6116-260">Klasa Startup</span><span class="sxs-lookup"><span data-stu-id="f6116-260">The Startup class</span></span>

<span data-ttu-id="f6116-261">`Startup`Klasa jest:</span><span class="sxs-lookup"><span data-stu-id="f6116-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="f6116-262">Usługi wymagane przez aplikację są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="f6116-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="f6116-263">Zdefiniowano potok obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="f6116-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="f6116-264">*Usługi* są składnikami, które są używane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="f6116-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="f6116-265">Na przykład składnik rejestrowania to usługa.</span><span class="sxs-lookup"><span data-stu-id="f6116-265">For example, a logging component is a service.</span></span> <span data-ttu-id="f6116-266">Kod do konfigurowania (lub *rejestrowania*) usług jest dodawany do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="f6116-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="f6116-267">Potok obsługi żądań składa się z serii komponentów *oprogramowania pośredniczącego* .</span><span class="sxs-lookup"><span data-stu-id="f6116-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="f6116-268">Na przykład oprogramowanie pośredniczące może obsługiwać żądania dotyczące plików statycznych lub przekierowywać żądania HTTP do protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f6116-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="f6116-269">Każde oprogramowanie pośredniczące wykonuje operacje asynchroniczne na serwerze `HttpContext` , a następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.</span><span class="sxs-lookup"><span data-stu-id="f6116-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="f6116-270">Kod do konfigurowania potoku obsługi żądań został dodany do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="f6116-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="f6116-271">Oto przykładowa `Startup` Klasa:</span><span class="sxs-lookup"><span data-stu-id="f6116-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="f6116-272">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f6116-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="f6116-273">Wstrzykiwanie zależności (usługi)</span><span class="sxs-lookup"><span data-stu-id="f6116-273">Dependency injection (services)</span></span>

<span data-ttu-id="f6116-274">ASP.NET Core ma wbudowaną platformę wstrzykiwania zależności (DI), która udostępnia skonfigurowane usługi dla klas aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6116-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="f6116-275">Jednym ze sposobów uzyskania wystąpienia usługi w klasie jest utworzenie konstruktora z parametrem wymaganego typu.</span><span class="sxs-lookup"><span data-stu-id="f6116-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="f6116-276">Parametr może być typem usługi lub interfejsem.</span><span class="sxs-lookup"><span data-stu-id="f6116-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="f6116-277">System DI zapewnia usługę w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="f6116-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="f6116-278">Oto Klasa, która używa funkcji DI do pobrania obiektu kontekstu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="f6116-279">Wyróżniony wiersz jest przykładem iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="f6116-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="f6116-280">Podczas gdy program jest wbudowany, został zaprojektowany z myślą o umożliwieniu podłączenia kontenera kontroli (IoC) innej firmy.</span><span class="sxs-lookup"><span data-stu-id="f6116-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="f6116-281">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f6116-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="f6116-282">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="f6116-282">Middleware</span></span>

<span data-ttu-id="f6116-283">Potok obsługi żądań składa się z serii komponentów oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="f6116-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="f6116-284">Każdy składnik wykonuje operacje asynchroniczne na serwerze `HttpContext` , a następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.</span><span class="sxs-lookup"><span data-stu-id="f6116-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="f6116-285">Zgodnie z Konwencją składnik pośredniczący jest dodawany do potoku przez wywołanie jego `Use...` metody rozszerzenia w `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f6116-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="f6116-286">Na przykład, aby włączyć renderowanie plików statycznych, wywołaj `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="f6116-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="f6116-287">Wyróżniony kod w poniższym przykładzie konfiguruje potok obsługi żądań:</span><span class="sxs-lookup"><span data-stu-id="f6116-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="f6116-288">ASP.NET Core zawiera rozbudowany zestaw wbudowanych programów pośredniczących i można napisać niestandardowe oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="f6116-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="f6116-289">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="f6116-290">Host</span><span class="sxs-lookup"><span data-stu-id="f6116-290">Host</span></span>

<span data-ttu-id="f6116-291">Aplikacja ASP.NET Core kompiluje *hosta* podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="f6116-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="f6116-292">Host jest obiektem, który hermetyzuje wszystkie zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="f6116-293">Implementacja serwera HTTP</span><span class="sxs-lookup"><span data-stu-id="f6116-293">An HTTP server implementation</span></span>
* <span data-ttu-id="f6116-294">Składniki oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="f6116-294">Middleware components</span></span>
* <span data-ttu-id="f6116-295">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="f6116-295">Logging</span></span>
* <span data-ttu-id="f6116-296">FOSFORAN</span><span class="sxs-lookup"><span data-stu-id="f6116-296">DI</span></span>
* <span data-ttu-id="f6116-297">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="f6116-297">Configuration</span></span>

<span data-ttu-id="f6116-298">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="f6116-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="f6116-299">Dostępne są dwa hosty: host sieci Web i Host ogólny.</span><span class="sxs-lookup"><span data-stu-id="f6116-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="f6116-300">W ASP.NET Core 2. x Host generyczny jest przeznaczony tylko dla scenariuszy innych niż sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="f6116-301">Kod służący do tworzenia hosta `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f6116-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="f6116-302">`CreateDefaultBuilder`Metoda konfiguruje hosta z najczęściej używanymi opcjami, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="f6116-303">Użyj [Kestrel](#servers) jako serwera sieci Web i Włącz INTEGRACJĘ usług IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="f6116-304">Załaduj konfigurację z pliku *appsettings.json* , *appSettings. { Nazwa środowiska}. JSON*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f6116-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="f6116-305">Wyślij dane wyjściowe rejestrowania do konsoli programu i dostawców debugowania.</span><span class="sxs-lookup"><span data-stu-id="f6116-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="f6116-306">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="f6116-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="f6116-307">Scenariusze inne niż internetowe</span><span class="sxs-lookup"><span data-stu-id="f6116-307">Non-web scenarios</span></span>

<span data-ttu-id="f6116-308">Host ogólny umożliwia innym typom aplikacji korzystanie z rozszerzeń struktury wycinania, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6116-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="f6116-309">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="f6116-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="f6116-310">Serwery</span><span class="sxs-lookup"><span data-stu-id="f6116-310">Servers</span></span>

<span data-ttu-id="f6116-311">Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6116-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="f6116-312">Serwer wyświetla żądania do aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="f6116-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="f6116-313">Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="f6116-314">ASP.NET Core udostępnia następujące implementacje serwera:</span><span class="sxs-lookup"><span data-stu-id="f6116-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f6116-315">*Kestrel* to Międzyplatformowy serwer sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f6116-316">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy za pomocą [usług IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f6116-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f6116-317">Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f6116-318">*Serwer http IIS* jest serwerem dla systemu Windows, który korzysta z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="f6116-319">Na tym serwerze aplikacja ASP.NET Core i usługi IIS działają w tym samym procesie.</span><span class="sxs-lookup"><span data-stu-id="f6116-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="f6116-320">*HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="f6116-321">macOS</span><span class="sxs-lookup"><span data-stu-id="f6116-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="f6116-322">ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* .</span><span class="sxs-lookup"><span data-stu-id="f6116-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f6116-323">Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f6116-324">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f6116-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="f6116-325">Linux</span><span class="sxs-lookup"><span data-stu-id="f6116-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="f6116-326">ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* .</span><span class="sxs-lookup"><span data-stu-id="f6116-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f6116-327">Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f6116-328">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f6116-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="f6116-329">Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="f6116-330">ASP.NET Core udostępnia następujące implementacje serwera:</span><span class="sxs-lookup"><span data-stu-id="f6116-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f6116-331">*Kestrel* to Międzyplatformowy serwer sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f6116-332">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy za pomocą [usług IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f6116-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f6116-333">Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f6116-334">*HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="f6116-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="f6116-335">macOS</span><span class="sxs-lookup"><span data-stu-id="f6116-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="f6116-336">ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* .</span><span class="sxs-lookup"><span data-stu-id="f6116-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f6116-337">Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f6116-338">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f6116-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="f6116-339">Linux</span><span class="sxs-lookup"><span data-stu-id="f6116-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="f6116-340">ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* .</span><span class="sxs-lookup"><span data-stu-id="f6116-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f6116-341">Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.</span><span class="sxs-lookup"><span data-stu-id="f6116-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f6116-342">Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f6116-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6116-343">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="f6116-344">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="f6116-344">Configuration</span></span>

<span data-ttu-id="f6116-345">ASP.NET Core udostępnia platformę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f6116-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="f6116-346">Istnieją Wbudowani dostawcy konfiguracji dla różnych źródeł, takich jak pliki *. JSON* , pliki *. XML* , zmienne środowiskowe i argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f6116-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="f6116-347">Możesz również napisać niestandardowych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f6116-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="f6116-348">Można na przykład określić, że konfiguracja pochodzi z *appsettings.json* i zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="f6116-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="f6116-349">Po zażądaniu wartości parametru *ConnectionString* struktura najpierw będzie w *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="f6116-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="f6116-350">Jeśli wartość jest tam znaleziona, ale również w zmiennej środowiskowej, pierwszeństwo ma wartość ze zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="f6116-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="f6116-351">Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, platforma .NET Core udostępnia [tajne Narzędzie Menedżera](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f6116-351">For managing confidential configuration data such as passwords, .NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f6116-352">W przypadku wpisów tajnych produkcji zalecamy [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f6116-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="f6116-353">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="f6116-354">Opcje</span><span class="sxs-lookup"><span data-stu-id="f6116-354">Options</span></span>

<span data-ttu-id="f6116-355">Jeśli to możliwe, ASP.NET Core są zgodne ze *wzorcem opcji* przechowywania i pobierania wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="f6116-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="f6116-356">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="f6116-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="f6116-357">Na przykład poniższy kod ustawia opcje obiektów WebSockets:</span><span class="sxs-lookup"><span data-stu-id="f6116-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="f6116-358">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="f6116-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="f6116-359">Środowiska</span><span class="sxs-lookup"><span data-stu-id="f6116-359">Environments</span></span>

<span data-ttu-id="f6116-360">Środowiska wykonawcze, takie jak *programowanie*, *przemieszczanie* i *produkcja*, są pierwszą klasą koncepcji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="f6116-361">Aby określić środowisko, w którym działa aplikacja, należy ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="f6116-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="f6116-362">ASP.NET Core odczytuje tę zmienną środowiskową przy uruchamianiu aplikacji i zapisuje wartość w `IHostingEnvironment` implementacji.</span><span class="sxs-lookup"><span data-stu-id="f6116-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="f6116-363">Obiekt środowiska jest dostępny w dowolnym miejscu w aplikacji za pomocą funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="f6116-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="f6116-364">Następujący przykładowy kod z klasy służy `Startup` do konfigurowania aplikacji w celu zapewnienia szczegółowych informacji o błędzie tylko wtedy, gdy jest ona uruchamiana w programie Development:</span><span class="sxs-lookup"><span data-stu-id="f6116-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="f6116-365">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f6116-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="f6116-366">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="f6116-366">Logging</span></span>

<span data-ttu-id="f6116-367">ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm.</span><span class="sxs-lookup"><span data-stu-id="f6116-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f6116-368">Dostępne są następujące dostawcy:</span><span class="sxs-lookup"><span data-stu-id="f6116-368">Available providers include the following:</span></span>

* <span data-ttu-id="f6116-369">Konsola</span><span class="sxs-lookup"><span data-stu-id="f6116-369">Console</span></span>
* <span data-ttu-id="f6116-370">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f6116-370">Debug</span></span>
* <span data-ttu-id="f6116-371">Śledzenie zdarzeń w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="f6116-372">Dziennik zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="f6116-372">Windows Event Log</span></span>
* <span data-ttu-id="f6116-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f6116-373">TraceSource</span></span>
* <span data-ttu-id="f6116-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f6116-374">Azure App Service</span></span>
* <span data-ttu-id="f6116-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f6116-375">Azure Application Insights</span></span>

<span data-ttu-id="f6116-376">Zapisuj dzienniki z dowolnego miejsca w kodzie aplikacji, pobierając `ILogger` obiekt z metod rejestrowania i wywoływania.</span><span class="sxs-lookup"><span data-stu-id="f6116-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="f6116-377">Oto przykładowy kod, który używa `ILogger` obiektu, z iniekcją konstruktora i wyróżniania wywołań metody rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f6116-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="f6116-378">`ILogger`Interfejs umożliwia przekazanie dowolnej liczby pól dostawcy rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f6116-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="f6116-379">Pola są często używane do konstruowania ciągu komunikatu, ale dostawcy mogą również wysyłać je jako oddzielne pola do magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="f6116-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="f6116-380">Ta funkcja umożliwia dostawcom rejestrowania implementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f6116-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f6116-381">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f6116-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="f6116-382">Routing</span><span class="sxs-lookup"><span data-stu-id="f6116-382">Routing</span></span>

<span data-ttu-id="f6116-383">*Trasa* jest WZORCEM adresu URL, który jest mapowany do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="f6116-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="f6116-384">Procedura obsługi jest zazwyczaj Razor stroną, metodą akcji w KONTROLERZE MVC lub w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="f6116-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="f6116-385">Routing ASP.NET Core zapewnia kontrolę nad adresami URL używanymi przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="f6116-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="f6116-386">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f6116-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="f6116-387">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="f6116-387">Error handling</span></span>

<span data-ttu-id="f6116-388">ASP.NET Core ma wbudowane funkcje do obsługi błędów, takie jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="f6116-389">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="f6116-389">A developer exception page</span></span>
* <span data-ttu-id="f6116-390">Niestandardowe strony błędów</span><span class="sxs-lookup"><span data-stu-id="f6116-390">Custom error pages</span></span>
* <span data-ttu-id="f6116-391">Statyczne strony kodów stanu</span><span class="sxs-lookup"><span data-stu-id="f6116-391">Static status code pages</span></span>
* <span data-ttu-id="f6116-392">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="f6116-392">Startup exception handling</span></span>

<span data-ttu-id="f6116-393">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="f6116-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="f6116-394">Zgłaszanie żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="f6116-394">Make HTTP requests</span></span>

<span data-ttu-id="f6116-395">Implementacja programu `IHttpClientFactory` jest dostępna do tworzenia `HttpClient` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="f6116-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="f6116-396">Fabryka:</span><span class="sxs-lookup"><span data-stu-id="f6116-396">The factory:</span></span>

* <span data-ttu-id="f6116-397">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="f6116-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f6116-398">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="f6116-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="f6116-399">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="f6116-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f6116-400">Obsługuje rejestrację i łańcuch wielu procedur delegowania, aby utworzyć potok pośredniczący żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="f6116-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f6116-401">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6116-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f6116-402">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="f6116-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="f6116-403">Integruje się z usługą *Polly*, popularną biblioteką innej firmy na potrzeby obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="f6116-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="f6116-404">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="f6116-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f6116-405">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="f6116-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f6116-406">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="f6116-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="f6116-407">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="f6116-407">Content root</span></span>

<span data-ttu-id="f6116-408">Katalog główny zawartości jest ścieżką podstawową do:</span><span class="sxs-lookup"><span data-stu-id="f6116-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="f6116-409">Plik wykonywalny obsługujący aplikację (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="f6116-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="f6116-410">Skompilowane zestawy wchodzące w skład aplikacji (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="f6116-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="f6116-411">Pliki zawartości inne niż kod używane przez aplikację, takie jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="f6116-412">Razor pliki (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="f6116-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="f6116-413">Pliki konfiguracji (*. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="f6116-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="f6116-414">Pliki danych (*. DB*)</span><span class="sxs-lookup"><span data-stu-id="f6116-414">Data files (*.db*)</span></span>
* <span data-ttu-id="f6116-415">[Katalog główny sieci Web](#web-root), zazwyczaj opublikowany folder *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="f6116-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="f6116-416">Podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="f6116-416">During development:</span></span>

* <span data-ttu-id="f6116-417">Katalog główny zawartości domyślnie jest katalogiem głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="f6116-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="f6116-418">Katalog główny projektu służy do tworzenia:</span><span class="sxs-lookup"><span data-stu-id="f6116-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="f6116-419">Ścieżka do plików zawartości nienależących do kodu aplikacji w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="f6116-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="f6116-420">[Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot* w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="f6116-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="f6116-421">Alternatywna ścieżka katalogu głównego zawartości może być określona podczas [kompilowania hosta](#host).</span><span class="sxs-lookup"><span data-stu-id="f6116-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="f6116-422">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="f6116-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="f6116-423">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="f6116-423">Web root</span></span>

<span data-ttu-id="f6116-424">Katalog główny sieci Web jest ścieżką podstawową do publicznych, niekodowych, statycznych plików zasobów, takich jak:</span><span class="sxs-lookup"><span data-stu-id="f6116-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="f6116-425">Arkusze stylów (*. css*)</span><span class="sxs-lookup"><span data-stu-id="f6116-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="f6116-426">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="f6116-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="f6116-427">Obrazy (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="f6116-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="f6116-428">Pliki statyczne są obsługiwane domyślnie tylko z katalogu głównego (i katalogów podrzędnych) sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="f6116-429">Ścieżka katalogu głównego sieci Web jest domyślnie ustawiona na *{Content root}/wwwroot*, ale podczas [kompilowania hosta](#host)można określić inny katalog internetowy w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="f6116-430">Aby uzyskać więcej informacji, zobacz [katalog główny sieci Web](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="f6116-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="f6116-431">Zapobiegaj publikowaniu plików w pliku *wwwroot* przy użyciu [ \<Content> elementu projektu](/visualstudio/msbuild/common-msbuild-project-items#content) w projekcie.</span><span class="sxs-lookup"><span data-stu-id="f6116-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="f6116-432">Poniższy przykład uniemożliwia opublikowanie zawartości w katalogu *wwwroot/lokalnym* i podkatalogach:</span><span class="sxs-lookup"><span data-stu-id="f6116-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="f6116-433">W Razor plikach (*. cshtml*), ukośnik ( `~/` ) wskazuje na katalog główny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f6116-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="f6116-434">Ścieżka rozpoczynająca `~/` się od jest nazywana *ścieżką wirtualną*.</span><span class="sxs-lookup"><span data-stu-id="f6116-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="f6116-435">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="f6116-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
