---
title: Link przeglądarki w ASP.NET Core
author: ncarandini
description: Wyjaśnia, w jaki sposób łącze przeglądarki jest funkcją programu Visual Studio, która łączy środowisko programistyczne z co najmniej jedną przeglądarką sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054492"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="67fe4-103">Link przeglądarki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67fe4-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="67fe4-104">Autorzy [Nicolò Carandini](https://github.com/ncarandini), [Jan Wasson](https://github.com/MikeWasson)i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="67fe4-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="67fe4-105">Link przeglądarki to funkcja programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="67fe4-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="67fe4-106">Tworzy kanał komunikacyjny między środowiskiem deweloperskim i jedną lub wieloma przeglądarkami sieci Web.</span><span class="sxs-lookup"><span data-stu-id="67fe4-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="67fe4-107">Możesz użyć linku przeglądarki, aby odświeżyć aplikację sieci Web w kilku przeglądarkach jednocześnie, co jest przydatne w przypadku testowania między przeglądarkami.</span><span class="sxs-lookup"><span data-stu-id="67fe4-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="67fe4-108">Konfiguracja linku przeglądarki</span><span class="sxs-lookup"><span data-stu-id="67fe4-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67fe4-109">Dodaj pakiet [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="67fe4-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="67fe4-110">W przypadku :::no-loc(Razor)::: stron ASP.NET Core lub projektów MVC należy również włączyć kompilację :::no-loc(Razor)::: plików ( *. cshtml* ) środowiska uruchomieniowego zgodnie z opisem w temacie <xref:mvc/views/view-compilation> .</span><span class="sxs-lookup"><span data-stu-id="67fe4-110">For ASP.NET Core :::no-loc(Razor)::: Pages or MVC projects, also enable runtime compilation of :::no-loc(Razor)::: ( *.cshtml* ) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="67fe4-111">:::no-loc(Razor)::: zmiany składni są stosowane tylko wtedy, gdy Kompilacja środowiska uruchomieniowego została włączona.</span><span class="sxs-lookup"><span data-stu-id="67fe4-111">:::no-loc(Razor)::: syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="67fe4-112">Podczas konwertowania projektu ASP.NET Core 2,0 na ASP.NET Core 2,1 i przejścia do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), zainstaluj pakiet [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) dla funkcji linku do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="67fe4-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="67fe4-113">Szablony projektów ASP.NET Core 2,1 korzystają domyślnie z `Microsoft.AspNetCore.App` pakietu.</span><span class="sxs-lookup"><span data-stu-id="67fe4-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="67fe4-114">Szablony projektu **aplikacji sieci Web** , **pustej** i **internetowego interfejsu API** ASP.NET Core 2,0 używają [pakietu Microsoft. AspNetCore. All](xref:fundamentals/metapackage), który zawiera odwołanie do pakietu dla [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="67fe4-114">The ASP.NET Core 2.0 **Web Application** , **Empty** , and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="67fe4-115">W związku z tym używanie `Microsoft.AspNetCore.All` pakietu nie wymaga żadnych dalszych działań w celu udostępnienia linku przeglądarki do użycia.</span><span class="sxs-lookup"><span data-stu-id="67fe4-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="67fe4-116">Szablon projektu **aplikacji sieci Web** ASP.NET Core 1. x zawiera odwołanie do pakietu dla pakietu [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="67fe4-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="67fe4-117">Inne typy projektów wymagają dodania odwołania do pakietu do `Microsoft.VisualStudio.Web.BrowserLink` .</span><span class="sxs-lookup"><span data-stu-id="67fe4-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="67fe4-118">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="67fe4-118">Configuration</span></span>

<span data-ttu-id="67fe4-119">Wywołanie `UseBrowserLink` `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="67fe4-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="67fe4-120">`UseBrowserLink`Wywołanie jest zwykle umieszczane wewnątrz `if` bloku, który umożliwia tylko łącze przeglądarki w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="67fe4-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="67fe4-121">Przykład:</span><span class="sxs-lookup"><span data-stu-id="67fe4-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="67fe4-122">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="67fe4-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="67fe4-123">Jak używać linku przeglądarki</span><span class="sxs-lookup"><span data-stu-id="67fe4-123">How to use Browser Link</span></span>

<span data-ttu-id="67fe4-124">Gdy masz otwarty projekt ASP.NET Core, program Visual Studio wyświetli kontrolkę pasek narzędzi linku przeglądarki obok kontrolki paska narzędzi **elementu docelowego debugowania** :</span><span class="sxs-lookup"><span data-stu-id="67fe4-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu rozwijane linku przeglądarki](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="67fe4-126">Za pomocą kontrolki paska narzędzi łącza przeglądarki można:</span><span class="sxs-lookup"><span data-stu-id="67fe4-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="67fe4-127">Odśwież aplikację sieci Web w kilku przeglądarkach jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="67fe4-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="67fe4-128">Otwórz **pulpit nawigacyjny linku do przeglądarki** .</span><span class="sxs-lookup"><span data-stu-id="67fe4-128">Open the **Browser Link Dashboard** .</span></span>
* <span data-ttu-id="67fe4-129">Włącza lub wyłącza **link przeglądarki** .</span><span class="sxs-lookup"><span data-stu-id="67fe4-129">Enable or disable **Browser Link** .</span></span> <span data-ttu-id="67fe4-130">Uwaga: łącze przeglądarki jest domyślnie wyłączone w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="67fe4-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="67fe4-131">Włączać lub wyłączać funkcję [autosynchronizacji CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="67fe4-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="67fe4-132">Odśwież aplikację sieci Web w kilku przeglądarkach jednocześnie</span><span class="sxs-lookup"><span data-stu-id="67fe4-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="67fe4-133">Aby wybrać jedną przeglądarkę sieci Web, która ma być uruchamiana podczas uruchamiania projektu, użyj menu rozwijanego w formancie paska narzędzi **elementu docelowego debugowania** :</span><span class="sxs-lookup"><span data-stu-id="67fe4-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu rozwijane F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="67fe4-135">Aby otworzyć wiele przeglądarek jednocześnie, wybierz pozycję **Przeglądaj za pomocą...** z tego samego listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="67fe4-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="67fe4-136">Naciśnij i przytrzymaj klawisz <kbd>Ctrl</kbd> , aby wybrać przeglądarki, a następnie kliknij przycisk **Przeglądaj** :</span><span class="sxs-lookup"><span data-stu-id="67fe4-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse** :</span></span>

![Otwórz wiele przeglądarek jednocześnie](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="67fe4-138">Poniższy zrzut ekranu przedstawia program Visual Studio z otwartym widokiem indeksu i dwiema otwartymi przeglądarkami:</span><span class="sxs-lookup"><span data-stu-id="67fe4-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Przykład synchronizacji z dwoma przeglądarkami](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="67fe4-140">Umieść kursor nad kontrolką paska narzędzi łącza przeglądarki, aby wyświetlić przeglądarki, które są połączone z projektem:</span><span class="sxs-lookup"><span data-stu-id="67fe4-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Wskazówki dotyczące aktywowania](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="67fe4-142">Zmień widok indeksu, a wszystkie połączone przeglądarki są aktualizowane po kliknięciu przycisku Odśwież linku do przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="67fe4-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![przeglądarki — synchronizacja ze zmianami](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="67fe4-144">Łącze przeglądarki współpracuje również z przeglądarkami uruchamianymi spoza programu Visual Studio i przejdź do adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="67fe4-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="67fe4-145">Pulpit nawigacyjny linków przeglądarki</span><span class="sxs-lookup"><span data-stu-id="67fe4-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="67fe4-146">Otwórz okno **pulpit nawigacyjny link do przeglądarki** z menu rozwijanego linku do przeglądarki, aby zarządzać połączeniem przy użyciu otwartych przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="67fe4-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Open-browserslink — pulpit nawigacyjny](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="67fe4-148">Jeśli żadna przeglądarka nie jest połączona, możesz uruchomić sesję niedebugowania, wybierając **Widok w przeglądarce** link:</span><span class="sxs-lookup"><span data-stu-id="67fe4-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink — pulpit nawigacyjny — brak połączeń](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="67fe4-150">W przeciwnym razie połączone przeglądarki są wyświetlane ze ścieżką do strony, którą pokazuje Każda przeglądarka:</span><span class="sxs-lookup"><span data-stu-id="67fe4-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink — pulpit nawigacyjny — dwa połączenia](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="67fe4-152">Możesz również kliknąć nazwę konkretnej przeglądarki w celu odświeżenia tylko tej przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="67fe4-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="67fe4-153">Włącz lub Wyłącz link przeglądarki</span><span class="sxs-lookup"><span data-stu-id="67fe4-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="67fe4-154">Po ponownym włączeniu linku przeglądarki po jego wyłączeniu należy odświeżyć przeglądarki w celu ponownego nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="67fe4-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="67fe4-155">Włączanie lub wyłączanie autosynchronizacji CSS</span><span class="sxs-lookup"><span data-stu-id="67fe4-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="67fe4-156">Gdy automatyczna synchronizacja CSS jest włączona, podłączane przeglądarki są automatycznie odświeżane po wprowadzeniu jakichkolwiek zmian w plikach CSS.</span><span class="sxs-lookup"><span data-stu-id="67fe4-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="67fe4-157">Jak to działa</span><span class="sxs-lookup"><span data-stu-id="67fe4-157">How it works</span></span>

<span data-ttu-id="67fe4-158">Link do przeglądarki używa [:::no-loc(SignalR):::](xref:signalr/introduction) do tworzenia kanału komunikacyjnego między programem Visual Studio i przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="67fe4-158">Browser Link uses [:::no-loc(SignalR):::](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="67fe4-159">Gdy łącze przeglądarki jest włączone, program Visual Studio działa jako :::no-loc(SignalR)::: serwer, z którym mogą się łączyć wielu klientów (przeglądarki).</span><span class="sxs-lookup"><span data-stu-id="67fe4-159">When Browser Link is enabled, Visual Studio acts as a :::no-loc(SignalR)::: server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="67fe4-160">Link przeglądarki rejestruje również składnik pośredniczący w potoku żądania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67fe4-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="67fe4-161">Ten składnik `<script>` wprowadza odwołania specjalne do każdego żądania strony z serwera.</span><span class="sxs-lookup"><span data-stu-id="67fe4-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="67fe4-162">Odwołania do skryptu można zobaczyć, wybierając opcję **Wyświetl źródło** w przeglądarce i przewijając do końca `<body>` zawartości tagu:</span><span class="sxs-lookup"><span data-stu-id="67fe4-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="67fe4-163">Pliki źródłowe nie są modyfikowane.</span><span class="sxs-lookup"><span data-stu-id="67fe4-163">Your source files aren't modified.</span></span> <span data-ttu-id="67fe4-164">Składnik pośredniczący wprowadza odwołania do skryptów dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="67fe4-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="67fe4-165">Ponieważ kod po stronie przeglądarki to wszystkie skrypty JavaScript, działa on we wszystkich przeglądarkach, które :::no-loc(SignalR)::: obsługują nie wymaga wtyczki przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="67fe4-165">Because the browser-side code is all JavaScript, it works on all browsers that :::no-loc(SignalR)::: supports without requiring a browser plug-in.</span></span>
