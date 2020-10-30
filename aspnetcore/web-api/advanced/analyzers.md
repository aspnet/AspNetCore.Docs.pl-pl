---
title: Korzystanie z analizatorów interfejsu API sieci Web
author: pranavkm
description: Dowiedz się więcej o pakiecie analizatorów interfejsów API sieci Web ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057924"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="2a75f-103">Korzystanie z analizatorów interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a75f-103">Use web API analyzers</span></span>

<span data-ttu-id="2a75f-104">ASP.NET Core 2,2 i nowsze udostępniają pakiet analizatorów MVC przeznaczony do użycia z projektami interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2a75f-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="2a75f-105">Analizatory współpracują z kontrolerami oznaczonymi przy użyciu programu <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> , podczas kompilowania w ramach [Konwencji interfejsu API sieci Web](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="2a75f-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="2a75f-106">Pakiet analizatorów powiadamia użytkownika o każdej akcji kontrolera, która:</span><span class="sxs-lookup"><span data-stu-id="2a75f-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="2a75f-107">Zwraca niezadeklarowany kod stanu.</span><span class="sxs-lookup"><span data-stu-id="2a75f-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="2a75f-108">Zwraca niezadeklarowany wynik sukcesu.</span><span class="sxs-lookup"><span data-stu-id="2a75f-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="2a75f-109">Dokumentuje kod stanu, który nie jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="2a75f-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="2a75f-110">Zawiera jawną kontrolę walidacji modelu.</span><span class="sxs-lookup"><span data-stu-id="2a75f-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="2a75f-111">Odwoływanie się do pakietu analizatora</span><span class="sxs-lookup"><span data-stu-id="2a75f-111">Reference the analyzer package</span></span>

<span data-ttu-id="2a75f-112">W ASP.NET Core 3,0 lub nowszych analizatory są zawarte w zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="2a75f-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="2a75f-113">Aby włączyć analizator w projekcie, należy uwzględnić `IncludeOpenAPIAnalyzers` Właściwość w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="2a75f-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="2a75f-114">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="2a75f-114">Package installation</span></span>

<span data-ttu-id="2a75f-115">Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. API. analizatory](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) z jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2a75f-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="2a75f-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a75f-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a75f-117">W oknie **konsola Menedżera pakietów** :</span><span class="sxs-lookup"><span data-stu-id="2a75f-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="2a75f-118">Przejdź do pozycji **Wyświetl** > **inną** > **konsolę Menedżera pakietów** systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2a75f-118">Go to **View** > **Other Windows** > **Package Manager Console** .</span></span>
  * <span data-ttu-id="2a75f-119">Przejdź do katalogu, w którym znajduje się plik *ApiConventions. csproj* .</span><span class="sxs-lookup"><span data-stu-id="2a75f-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="2a75f-120">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2a75f-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a75f-121">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2a75f-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a75f-122">Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania** > **Dodaj pakiety...** .</span><span class="sxs-lookup"><span data-stu-id="2a75f-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...** .</span></span>
* <span data-ttu-id="2a75f-123">Ustaw listę rozwijaną **Źródło** okna **Dodaj pakiety** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="2a75f-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="2a75f-124">W polu wyszukiwania wprowadź ciąg "Microsoft. AspNetCore. MVC. API. analizatory".</span><span class="sxs-lookup"><span data-stu-id="2a75f-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="2a75f-125">Wybierz pakiet "Microsoft. AspNetCore. MVC. API. analizatory" w okienku wyników, a następnie kliknij pozycję **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="2a75f-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package** .</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="2a75f-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a75f-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a75f-127">Uruchom następujące polecenie w **zintegrowanym terminalu** :</span><span class="sxs-lookup"><span data-stu-id="2a75f-127">Run the following command from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="2a75f-128">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2a75f-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2a75f-129">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2a75f-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="2a75f-130">Analizatory dla Konwencji interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a75f-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="2a75f-131">Dokumenty OpenAPI zawierają kody stanu i typy odpowiedzi, które może zwrócić akcja.</span><span class="sxs-lookup"><span data-stu-id="2a75f-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="2a75f-132">W ASP.NET Core MVC atrybuty takie jak <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> i <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> są używane do dokumentowania akcji.</span><span class="sxs-lookup"><span data-stu-id="2a75f-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="2a75f-133"><xref:tutorials/web-api-help-pages-using-swagger> Szczegółowe informacje na temat dokumentowania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="2a75f-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="2a75f-134">Jeden z analizatorów w pakiecie sprawdza kontrolery z adnotacją <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> i identyfikuje akcje, które nie są w pełni udokumentowane.</span><span class="sxs-lookup"><span data-stu-id="2a75f-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="2a75f-135">Rozpatrzmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="2a75f-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="2a75f-136">Poprzednia akcja powoduje udokumentowanie typu zwracanego przez HTTP 200, ale nie dokumentuje kodu stanu błędu HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="2a75f-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="2a75f-137">Analizator raportuje brakującą dokumentację dla kodu stanu HTTP 404 jako ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="2a75f-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="2a75f-138">Podano opcję rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="2a75f-138">An option to fix the problem is provided.</span></span>

![Analizator raportuje ostrzeżenie](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="2a75f-140">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2a75f-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
