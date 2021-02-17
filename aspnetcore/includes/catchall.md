---
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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552613"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="69b0a-101">Parametr **"catch-all"** może być zgodny z niepoprawnymi trasami z powodu [błędu](https://github.com/dotnet/aspnetcore/issues/18677) w routingu.</span><span class="sxs-lookup"><span data-stu-id="69b0a-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="69b0a-102">Aplikacje, na które ma wpływ ta usterka, mają następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="69b0a-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="69b0a-103">Trasa przechwycenia, na przykład `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="69b0a-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="69b0a-104">Trasa catch-all nie będzie zgodna z żądaniami, które powinny być zgodne.</span><span class="sxs-lookup"><span data-stu-id="69b0a-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="69b0a-105">Usunięcie innych tras spowoduje rozpoczęcie pracy z całą trasą.</span><span class="sxs-lookup"><span data-stu-id="69b0a-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="69b0a-106">Zobacz błędy usługi GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) i [16579](https://github.com/dotnet/aspnetcore/issues/16579) , aby zobaczyć przykładowe przypadki, w których trafili ten błąd.</span><span class="sxs-lookup"><span data-stu-id="69b0a-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="69b0a-107">Poprawka dotycząca tej usterki jest zawarta w programie [.NET Core 3.1.301 SDK i nowszych](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="69b0a-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="69b0a-108">Poniższy kod ustawia przełącznik wewnętrzny, który naprawia tę usterkę:</span><span class="sxs-lookup"><span data-stu-id="69b0a-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end