---
title: Obsługa błędów w Blazor aplikacjach ASP.NET Core
author: guardrex
description: Odkryj, jak ASP.NET Core Blazor jak program Blazor zarządza nieobsługiwanymi wyjątkami i jak opracowywać aplikacje wykrywające i obsługujące błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: f7cac477e2c5bca54e24ae3faeadff9b51bdcd0f
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101064"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="e463d-103">Obsługa błędów w Blazor aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e463d-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="e463d-104">W tym artykule opisano, jak Blazor zarządzać nieobsługiwanymi wyjątkami oraz jak opracowywać aplikacje wykrywające i obsługujące błędy.</span><span class="sxs-lookup"><span data-stu-id="e463d-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="e463d-105">Szczegóły błędów podczas opracowywania</span><span class="sxs-lookup"><span data-stu-id="e463d-105">Detailed errors during development</span></span>

<span data-ttu-id="e463d-106">Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="e463d-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="e463d-107">Gdy wystąpi błąd, Blazor aplikacje wyświetlają złoty pasek u dołu ekranu:</span><span class="sxs-lookup"><span data-stu-id="e463d-107">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="e463d-108">W trakcie programowania złoty pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e463d-108">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="e463d-109">W środowisku produkcyjnym złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e463d-109">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="e463d-110">Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią Blazor szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="e463d-110">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="e463d-111">W Blazor WebAssembly aplikacji Dostosuj środowisko w `wwwroot/index.html` pliku:</span><span class="sxs-lookup"><span data-stu-id="e463d-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="e463d-112">W Blazor Server aplikacji Dostosuj środowisko w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="e463d-112">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="e463d-113">`blazor-error-ui`Element jest ukryty przez style zawarte w Blazor szablonach ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ), a następnie pokazywany w przypadku wystąpienia błędu:</span><span class="sxs-lookup"><span data-stu-id="e463d-113">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="e463d-114">Blazor Server Szczegóły błędów obwodu</span><span class="sxs-lookup"><span data-stu-id="e463d-114">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="e463d-115">Błędy po stronie klienta nie zawierają stosu wywołań i nie zawierają szczegółów przyczyny błędu, ale Dzienniki serwera zawierają takie informacje.</span><span class="sxs-lookup"><span data-stu-id="e463d-115">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="e463d-116">W celach programistycznych informacje o błędzie obwodu poufnego mogą być udostępniane klientowi, włączając szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="e463d-116">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="e463d-117">Włącz Blazor Server szczegółowe błędy przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="e463d-117">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="e463d-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="e463d-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="e463d-119">`DetailedErrors`Klucz konfiguracji ustawiony na `true` , który można ustawić w pliku ustawień programowania aplikacji ( `appsettings.Development.json` ).</span><span class="sxs-lookup"><span data-stu-id="e463d-119">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="e463d-120">Klucz można również ustawić przy użyciu `ASPNETCORE_DETAILEDERRORS` zmiennej środowiskowej o wartości `true` .</span><span class="sxs-lookup"><span data-stu-id="e463d-120">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="e463d-121">[ SignalR Rejestrowanie po stronie serwera](xref:signalr/diagnostics#server-side-logging) ( `Microsoft.AspNetCore.SignalR` ) można ustawić na [debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenie](xref:Microsoft.Extensions.Logging.LogLevel) szczegółowego SignalR rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="e463d-121">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="e463d-122">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="e463d-122">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> [!WARNING]
> <span data-ttu-id="e463d-123">Ujawnienie informacji o błędach klientom w Internecie stanowi zagrożenie bezpieczeństwa, które należy zawsze uniknąć.</span><span class="sxs-lookup"><span data-stu-id="e463d-123">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="e463d-124">Jak Blazor Server Aplikacja reaguje na Nieobsłużone wyjątki</span><span class="sxs-lookup"><span data-stu-id="e463d-124">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="e463d-125">Blazor Server jest strukturą stanową.</span><span class="sxs-lookup"><span data-stu-id="e463d-125">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="e463d-126">Gdy użytkownicy współpracują z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*.</span><span class="sxs-lookup"><span data-stu-id="e463d-126">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="e463d-127">Obwód zawiera aktywne wystąpienia składnika, a także wiele innych aspektów stanu, takich jak:</span><span class="sxs-lookup"><span data-stu-id="e463d-127">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="e463d-128">Najnowsze renderowane dane wyjściowe składników.</span><span class="sxs-lookup"><span data-stu-id="e463d-128">The most recent rendered output of components.</span></span>
* <span data-ttu-id="e463d-129">Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="e463d-129">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="e463d-130">Jeśli użytkownik otworzy aplikację na wielu kartach przeglądarki, mają one wiele niezależnych obwodów.</span><span class="sxs-lookup"><span data-stu-id="e463d-130">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="e463d-131">Blazor traktuje większość nieobsłużonych wyjątków jako krytyczne dla obwodu, w którym występują.</span><span class="sxs-lookup"><span data-stu-id="e463d-131">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="e463d-132">Jeśli obwód zostanie przerwany z powodu nieobsługiwanego wyjątku, użytkownik może nadal korzystać z aplikacji tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-132">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="e463d-133">Nie ma to wpływu na obwody, które zostały przerwane, które są obwody dla innych użytkowników lub kart przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e463d-133">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="e463d-134">Ten scenariusz jest podobny do aplikacji klasycznej, która ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="e463d-134">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="e463d-135">Aplikacja, która uległa awarii, musi zostać ponownie uruchomiona, ale nie ma to wpływu na inne aplikacje.</span><span class="sxs-lookup"><span data-stu-id="e463d-135">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="e463d-136">Obwód jest zakończony, gdy wystąpił nieobsługiwany wyjątek z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="e463d-136">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="e463d-137">Nieobsługiwany wyjątek często pozostawia obwód w niezdefiniowanym stanie.</span><span class="sxs-lookup"><span data-stu-id="e463d-137">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="e463d-138">Nie można zagwarantować normalnej operacji aplikacji po wystąpieniu nieobsłużonego wyjątku.</span><span class="sxs-lookup"><span data-stu-id="e463d-138">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="e463d-139">W przypadku kontynuowania obwodu w aplikacji mogą pojawić się luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="e463d-139">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="e463d-140">Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera</span><span class="sxs-lookup"><span data-stu-id="e463d-140">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="e463d-141">Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="e463d-141">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="e463d-142">W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="e463d-142">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="e463d-143">W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e463d-143">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="e463d-144">Renderowanie komunikatów o wyjątkach lub śladów stosu może:</span><span class="sxs-lookup"><span data-stu-id="e463d-144">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="e463d-145">Ujawnianie poufnych informacji użytkownikom końcowym.</span><span class="sxs-lookup"><span data-stu-id="e463d-145">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="e463d-146">Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.</span><span class="sxs-lookup"><span data-stu-id="e463d-146">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="e463d-147">Rejestrowanie błędów przez dostawcę trwałego</span><span class="sxs-lookup"><span data-stu-id="e463d-147">Log errors with a persistent provider</span></span>

<span data-ttu-id="e463d-148">Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="e463d-148">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="e463d-149">Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli.</span><span class="sxs-lookup"><span data-stu-id="e463d-149">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="e463d-150">Należy rozważyć logowanie do bardziej trwałej lokalizacji z dostawcą, który zarządza rozmiarem dziennika i rotacją dzienników.</span><span class="sxs-lookup"><span data-stu-id="e463d-150">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="e463d-151">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="e463d-151">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="e463d-152">Podczas opracowywania programu Blazor zwykle program wysyła do konsoli przeglądarki pełne szczegóły dotyczące wyjątków, aby pomóc w debugowaniu.</span><span class="sxs-lookup"><span data-stu-id="e463d-152">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="e463d-153">W środowisku produkcyjnym szczegółowe błędy w konsoli przeglądarki są domyślnie wyłączone, co oznacza, że błędy nie są wysyłane do klientów, ale wszystkie szczegóły wyjątku nadal są rejestrowane po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="e463d-153">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="e463d-154">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="e463d-154">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="e463d-155">Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="e463d-155">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="e463d-156">Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy.</span><span class="sxs-lookup"><span data-stu-id="e463d-156">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="e463d-157">Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie.</span><span class="sxs-lookup"><span data-stu-id="e463d-157">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="e463d-158">Nie wszystkie błędy powinny być traktowane jako zdarzenia o wysokiej ważności do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="e463d-158">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="e463d-159">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="e463d-159">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="e463d-160">Miejsca, w których mogą wystąpić błędy</span><span class="sxs-lookup"><span data-stu-id="e463d-160">Places where errors may occur</span></span>

<span data-ttu-id="e463d-161">Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="e463d-161">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="e463d-162">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="e463d-162">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="e463d-163">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="e463d-163">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="e463d-164">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="e463d-164">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="e463d-165">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="e463d-165">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="e463d-166">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="e463d-166">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="e463d-167">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="e463d-167">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="e463d-168">Blazor Server Renderowanie</span><span class="sxs-lookup"><span data-stu-id="e463d-168">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="e463d-169">Poprzednie Nieobsłużone wyjątki zostały opisane w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="e463d-169">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="e463d-170">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="e463d-170">Component instantiation</span></span>

<span data-ttu-id="e463d-171">Podczas Blazor tworzenia wystąpienia składnika:</span><span class="sxs-lookup"><span data-stu-id="e463d-171">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="e463d-172">Konstruktor składnika jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="e463d-172">The component's constructor is invoked.</span></span>
* <span data-ttu-id="e463d-173">Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:mvc/views/razor#inject) dyrektywy lub [ `[Inject]` atrybutu](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="e463d-173">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="e463d-174">Blazor ServerObwód kończy się niepowodzeniem, gdy dowolny wykonany Konstruktor lub setter dla każdej `[Inject]` właściwości zgłasza nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e463d-174">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="e463d-175">Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="e463d-175">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="e463d-176">Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-176">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="e463d-177">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="e463d-177">Lifecycle methods</span></span>

<span data-ttu-id="e463d-178">W okresie istnienia składnika program Blazor wywołuje następujące [metody cyklu życia](xref:blazor/components/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="e463d-178">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="e463d-179">Jeśli jakakolwiek metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-179">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="e463d-180">Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="e463d-180">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="e463d-181">W poniższym przykładzie, gdzie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> wywołuje metodę w celu uzyskania produktu:</span><span class="sxs-lookup"><span data-stu-id="e463d-181">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="e463d-182">Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcję.</span><span class="sxs-lookup"><span data-stu-id="e463d-182">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="e463d-183">Gdy `catch` blok jest wykonywany:</span><span class="sxs-lookup"><span data-stu-id="e463d-183">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="e463d-184">`loadFailed` jest ustawiona na `true` , która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e463d-184">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="e463d-185">Błąd jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="e463d-185">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

### <a name="rendering-logic"></a><span data-ttu-id="e463d-186">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="e463d-186">Rendering logic</span></span>

<span data-ttu-id="e463d-187">Znaczniki deklaratywne w `.razor` pliku składnika są kompilowane do metody języka C# o nazwie <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="e463d-187">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="e463d-188">Gdy składnik renderuje, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.</span><span class="sxs-lookup"><span data-stu-id="e463d-188">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="e463d-189">Logika renderowania może zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e463d-189">Rendering logic can throw an exception.</span></span> <span data-ttu-id="e463d-190">Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null` .</span><span class="sxs-lookup"><span data-stu-id="e463d-190">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="e463d-191">Nieobsługiwany wyjątek zgłoszony przez logikę renderowania jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-191">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="e463d-192">Aby zapobiec wystąpieniu wyjątku odwołania o wartości null w logice renderowania, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem.</span><span class="sxs-lookup"><span data-stu-id="e463d-192">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="e463d-193">W poniższym przykładzie `person.Address` właściwości nie są dostępne, jeśli `person.Address` `null` :</span><span class="sxs-lookup"><span data-stu-id="e463d-193">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="e463d-194">Poprzedni kod założono, że `person` nie jest `null` .</span><span class="sxs-lookup"><span data-stu-id="e463d-194">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="e463d-195">Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="e463d-195">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="e463d-196">W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania.</span><span class="sxs-lookup"><span data-stu-id="e463d-196">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="e463d-197">W poprzednim przykładzie `person` można zagwarantować, że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="e463d-197">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="e463d-198">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="e463d-198">Event handlers</span></span>

<span data-ttu-id="e463d-199">Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="e463d-199">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="e463d-200">Inne `@on...` atrybuty</span><span class="sxs-lookup"><span data-stu-id="e463d-200">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="e463d-201">Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="e463d-201">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="e463d-202">Jeśli procedura obsługi zdarzeń zgłasza nieobsługiwany wyjątek (na przykład kwerenda bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-202">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="e463d-203">Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-203">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="e463d-204">Jeśli kod użytkownika nie jest pułapk i nie obsługuje wyjątku, struktura rejestruje wyjątek i kończy obwód.</span><span class="sxs-lookup"><span data-stu-id="e463d-204">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="e463d-205">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="e463d-205">Component disposal</span></span>

<span data-ttu-id="e463d-206">Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony.</span><span class="sxs-lookup"><span data-stu-id="e463d-206">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="e463d-207">Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose%2A> metodę składnika.</span><span class="sxs-lookup"><span data-stu-id="e463d-207">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="e463d-208">Jeśli `Dispose` Metoda składnika zgłasza nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-208">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="e463d-209">Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-209">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="e463d-210">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="e463d-210">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="e463d-211">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="e463d-211">JavaScript interop</span></span>

<span data-ttu-id="e463d-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e463d-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="e463d-213">Poniższe warunki dotyczą obsługi błędów w programie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="e463d-213">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="e463d-214">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchronicznie zakończy się niepowodzeniem, wystąpi wyjątek programu .NET.</span><span class="sxs-lookup"><span data-stu-id="e463d-214">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="e463d-215">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów.</span><span class="sxs-lookup"><span data-stu-id="e463d-215">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="e463d-216">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e463d-216">Developer code must catch the exception.</span></span> <span data-ttu-id="e463d-217">Jeśli kod aplikacji w obsłudze zdarzeń lub metoda cyklu życia składnika nie obsługuje wyjątku, wynikający z nich wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-217">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="e463d-218">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-218">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="e463d-219">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako `rejected` .</span><span class="sxs-lookup"><span data-stu-id="e463d-219">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="e463d-220">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e463d-220">Developer code must catch the exception.</span></span> <span data-ttu-id="e463d-221">W przypadku użycia [`await`](/dotnet/csharp/language-reference/keywords/await) operatora Rozważ zapakowanie wywołania metody w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-221">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="e463d-222">W przeciwnym razie niepowodzenie kodu spowoduje nieobsłużony wyjątek, który jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-222">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="e463d-223">Domyślnie wywołania programu <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muszą zakończyć się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę.</span><span class="sxs-lookup"><span data-stu-id="e463d-223">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="e463d-224">Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający.</span><span class="sxs-lookup"><span data-stu-id="e463d-224">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="e463d-225">Jeśli wystąpiło przełączenie, wynikiem <xref:System.Threading.Tasks> kończy się niepowodzeniem a <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="e463d-225">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="e463d-226">Zalewka i przetwórz wyjątek z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-226">Trap and process the exception with logging.</span></span>

<span data-ttu-id="e463d-227">Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [ `[JSInvokable]` atrybut](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="e463d-227">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="e463d-228">Jeśli te metody .NET zgłaszają nieobsługiwany wyjątek:</span><span class="sxs-lookup"><span data-stu-id="e463d-228">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="e463d-229">Wyjątek nie jest traktowany jako krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-229">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="e463d-230">Po stronie JavaScript `Promise` jest odrzucany.</span><span class="sxs-lookup"><span data-stu-id="e463d-230">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="e463d-231">Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="e463d-231">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="e463d-232">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="e463d-232">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a><span data-ttu-id="e463d-233">Blazor Server Renderowanie prerenderingu</span><span class="sxs-lookup"><span data-stu-id="e463d-233">Blazor Server prerendering</span></span>

<span data-ttu-id="e463d-234">Blazor składniki mogą być wstępnie renderowane przy użyciu [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , dzięki czemu RENDEROWANE znaczniki HTML są zwracane jako część początkowego żądania HTTP użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e463d-234">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="e463d-235">Działa to w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e463d-235">This works by:</span></span>

* <span data-ttu-id="e463d-236">Tworzenie nowego obwodu dla wszystkich wstępnie renderowanych składników, które są częścią tej samej strony.</span><span class="sxs-lookup"><span data-stu-id="e463d-236">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="e463d-237">Generowanie początkowego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="e463d-237">Generating the initial HTML.</span></span>
* <span data-ttu-id="e463d-238">Przetraktowanie obwodu `disconnected` do momentu, aż przeglądarka użytkownika nawiąże SignalR połączenie z powrotem z tym samym serwerem.</span><span class="sxs-lookup"><span data-stu-id="e463d-238">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="e463d-239">Po nawiązaniu połączenia zostanie wznowione działanie międzydziałające w obwodzie i zostanie zaktualizowane oznaczenie HTML składników.</span><span class="sxs-lookup"><span data-stu-id="e463d-239">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="e463d-240">Jeśli jakikolwiek składnik zgłasza nieobsłużony wyjątek podczas renderowania pre, na przykład podczas wykonywania metody cyklu życia lub logiki renderowania:</span><span class="sxs-lookup"><span data-stu-id="e463d-240">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="e463d-241">Wyjątek jest krytyczny dla obwodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-241">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="e463d-242">Wyjątek jest generowany w stosie wywołań z <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="e463d-242">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="e463d-243">W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie przechwycony przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="e463d-243">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="e463d-244">W normalnych warunkach w przypadku niepowodzenia wstępnego renderowania kontynuowanie kompilowania i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.</span><span class="sxs-lookup"><span data-stu-id="e463d-244">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="e463d-245">Aby tolerować błędy, które mogą wystąpić podczas renderowania prerenderingu, logika obsługi błędów musi być umieszczona wewnątrz składnika, który może zgłaszać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="e463d-245">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="e463d-246">Używaj [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="e463d-246">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="e463d-247">Zamiast zawijać <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji, umieść logikę obsługi błędów w składniku renderowanym przez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="e463d-247">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="e463d-248">Scenariusze zaawansowane</span><span class="sxs-lookup"><span data-stu-id="e463d-248">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="e463d-249">Renderowanie cykliczne</span><span class="sxs-lookup"><span data-stu-id="e463d-249">Recursive rendering</span></span>

<span data-ttu-id="e463d-250">Składniki można cyklicznie zagnieżdżać.</span><span class="sxs-lookup"><span data-stu-id="e463d-250">Components can be nested recursively.</span></span> <span data-ttu-id="e463d-251">Jest to przydatne do reprezentowania struktur danych rekursywnych.</span><span class="sxs-lookup"><span data-stu-id="e463d-251">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="e463d-252">Na przykład `TreeNode` składnik może renderować więcej `TreeNode` składników dla każdego węzła podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="e463d-252">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="e463d-253">W przypadku renderowania cyklicznego należy unikać tworzenia wzorców, które powodują nieskończoną rekursję:</span><span class="sxs-lookup"><span data-stu-id="e463d-253">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="e463d-254">Nie Renderuj rekursywnie struktury danych zawierającej cykl.</span><span class="sxs-lookup"><span data-stu-id="e463d-254">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="e463d-255">Na przykład nie Renderuj węzła drzewa, którego elementy podrzędne zawierają sam siebie.</span><span class="sxs-lookup"><span data-stu-id="e463d-255">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="e463d-256">Nie twórz łańcucha układów zawierających cykl.</span><span class="sxs-lookup"><span data-stu-id="e463d-256">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="e463d-257">Na przykład nie twórz układu, którego układ jest sam.</span><span class="sxs-lookup"><span data-stu-id="e463d-257">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="e463d-258">Nie Zezwalaj użytkownikowi końcowemu na naruszenie nieodmian rekursji (reguł) przy użyciu złośliwego wpisu danych lub wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e463d-258">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="e463d-259">Nieskończone pętle podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="e463d-259">Infinite loops during rendering:</span></span>

* <span data-ttu-id="e463d-260">Powoduje, że proces renderowania kontynuuje działanie zawsze.</span><span class="sxs-lookup"><span data-stu-id="e463d-260">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="e463d-261">Jest równoznaczny z tworzeniem niezakończonej pętli.</span><span class="sxs-lookup"><span data-stu-id="e463d-261">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="e463d-262">W tych scenariuszach obwód, którego to dotyczy, Blazor Server kończy się niepowodzeniem, a wątek zwykle próbuje wykonać:</span><span class="sxs-lookup"><span data-stu-id="e463d-262">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="e463d-263">Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.</span><span class="sxs-lookup"><span data-stu-id="e463d-263">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="e463d-264">Korzystaj z nieograniczonej ilości pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="e463d-264">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="e463d-265">Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.</span><span class="sxs-lookup"><span data-stu-id="e463d-265">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="e463d-266">Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="e463d-266">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="e463d-267">Logika drzewa renderowania niestandardowego</span><span class="sxs-lookup"><span data-stu-id="e463d-267">Custom render tree logic</span></span>

<span data-ttu-id="e463d-268">Większość Blazor składników jest zaimplementowana jako `.razor` pliki i są kompilowane do tworzenia logiki, która działa w <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> celu renderowania danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="e463d-268">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="e463d-269">Deweloper może ręcznie zaimplementować <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logikę przy użyciu procedury kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="e463d-269">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="e463d-270">Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="e463d-270">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="e463d-271">Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.</span><span class="sxs-lookup"><span data-stu-id="e463d-271">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="e463d-272">Jeśli <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu.</span><span class="sxs-lookup"><span data-stu-id="e463d-272">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="e463d-273">Na przykład Deweloper musi upewnić się, że:</span><span class="sxs-lookup"><span data-stu-id="e463d-273">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="e463d-274">Wywołania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> i <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> są prawidłowo zrównoważone.</span><span class="sxs-lookup"><span data-stu-id="e463d-274">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="e463d-275">Atrybuty są dodawane tylko w prawidłowych miejscach.</span><span class="sxs-lookup"><span data-stu-id="e463d-275">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="e463d-276">Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie serwera i luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="e463d-276">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="e463d-277">Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub instrukcji MSIL.</span><span class="sxs-lookup"><span data-stu-id="e463d-277">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
