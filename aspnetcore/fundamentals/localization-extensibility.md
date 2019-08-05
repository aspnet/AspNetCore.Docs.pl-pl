---
title: Rozszerzalność lokalizacji
author: hishamco
description: Dowiedz się, jak rozłożyć interfejsy API lokalizacji w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: 92fe954ea6bf5d0a8f9f62f4da696d197c51af04
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776772"
---
# <a name="localization-extensibility"></a><span data-ttu-id="5a066-103">Rozszerzalność lokalizacji</span><span class="sxs-lookup"><span data-stu-id="5a066-103">Localization Extensibility</span></span>

<span data-ttu-id="5a066-104">Według [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="5a066-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="5a066-105">Ten artykuł:</span><span class="sxs-lookup"><span data-stu-id="5a066-105">This article:</span></span>

* <span data-ttu-id="5a066-106">Wyświetla listę punktów rozszerzalności w interfejsach API lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5a066-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="5a066-107">Zawiera instrukcje dotyczące sposobu rozbudowania lokalizacji aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a066-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="5a066-108">Rozszerzalne punkty w interfejsach API lokalizacji</span><span class="sxs-lookup"><span data-stu-id="5a066-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="5a066-109">Interfejsy API lokalizacji ASP.NET Core są kompilowane do rozszerzalności.</span><span class="sxs-lookup"><span data-stu-id="5a066-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="5a066-110">Rozszerzalność umożliwia deweloperom dostosowanie lokalizacji zgodnie z ich potrzebami.</span><span class="sxs-lookup"><span data-stu-id="5a066-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="5a066-111">Na przykład [OrchardCore](https://github.com/orchardCMS/OrchardCore/) ma `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="5a066-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="5a066-112">`POStringLocalizer`szczegółowo opisano przy użyciu [lokalizacji obiektów przenośnych](xref:fundamentals/portable-object-localization) do `PO` przechowywania zasobów lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5a066-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="5a066-113">W tym artykule wymieniono dwa główne punkty rozszerzalności zapewniane przez interfejsy API lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="5a066-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="5a066-114">Dostawcy kultury lokalizacji</span><span class="sxs-lookup"><span data-stu-id="5a066-114">Localization Culture Providers</span></span>

<span data-ttu-id="5a066-115">Interfejsy API lokalizacji ASP.NET Core mają czterech dostawców domyślnych, którzy mogą określić bieżącą kulturę żądania wykonania:</span><span class="sxs-lookup"><span data-stu-id="5a066-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="5a066-116">Poprzedni dostawcy są szczegółowo opisane w dokumentacji [oprogramowania pośredniczącego](xref:fundamentals/localization) .</span><span class="sxs-lookup"><span data-stu-id="5a066-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="5a066-117">Jeśli dostawcy domyślnie nie spełnią Twoich potrzeb, należy utworzyć niestandardowego dostawcę przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5a066-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="5a066-118">Użyj CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="5a066-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="5a066-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>zapewnia niestandardowy <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> , który używa prostego delegata do określenia bieżącej kultury lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="5a066-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
options.AddInitialRequestCultureProvider(
    new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
options.RequestCultureProviders.Insert(0, 
    new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="5a066-120">Użyj nowego Jawna implementacja RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="5a066-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="5a066-121"><xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> Można utworzyć nową implementację, która określa informacje o kulturze żądania ze źródła niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="5a066-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="5a066-122">Na przykład źródło niestandardowe może być plikiem konfiguracji lub bazą danych.</span><span class="sxs-lookup"><span data-stu-id="5a066-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="5a066-123">Poniższy przykład pokazuje `AppSettingsRequestCultureProvider`, który <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> rozszerza, aby określić informacje o kulturze żądań z pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5a066-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="5a066-124">Zasoby lokalizacji</span><span class="sxs-lookup"><span data-stu-id="5a066-124">Localization resources</span></span>

<span data-ttu-id="5a066-125">Zapewnia <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>ASP.NET Core lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5a066-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="5a066-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>jest implementacją programu <xref:Microsoft.Extensions.Localization.IStringLocalizer> , która jest `resx` stosowana do przechowywania zasobów lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5a066-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="5a066-127">Nie można używać `resx` plików.</span><span class="sxs-lookup"><span data-stu-id="5a066-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="5a066-128">Implementując `IStringLocalized`, można użyć dowolnego źródła danych.</span><span class="sxs-lookup"><span data-stu-id="5a066-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="5a066-129">Następujące przykładowe projekty implementują <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span><span class="sxs-lookup"><span data-stu-id="5a066-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="5a066-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="5a066-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="5a066-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="5a066-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="5a066-132">Sqllokalizowaer</span><span class="sxs-lookup"><span data-stu-id="5a066-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)