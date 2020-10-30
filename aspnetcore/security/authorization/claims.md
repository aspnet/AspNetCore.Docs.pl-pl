---
title: Autoryzacja oparta na oświadczeniach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać sprawdzanie oświadczeń pod kątem autoryzacji w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060277"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="8e286-103">Autoryzacja oparta na oświadczeniach w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e286-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="8e286-104">Po utworzeniu tożsamości może zostać przypisane jedno lub więcej oświadczeń wystawionych przez zaufaną stronę.</span><span class="sxs-lookup"><span data-stu-id="8e286-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="8e286-105">Jest to para wartości Nazwa, która reprezentuje temat, a nie co może zrobić.</span><span class="sxs-lookup"><span data-stu-id="8e286-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="8e286-106">Na przykład użytkownik może mieć licencję sterownika wydaną przez Urząd lokalnej licencji na kierowanie.</span><span class="sxs-lookup"><span data-stu-id="8e286-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="8e286-107">Licencja sterownika ma swoją datę urodzenia.</span><span class="sxs-lookup"><span data-stu-id="8e286-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="8e286-108">W takim przypadku nazwa żądania powinna być równa `DateOfBirth` dacie urodzenia, na przykład, `8th June 1970` a wystawca będzie urzędem licencjonowania.</span><span class="sxs-lookup"><span data-stu-id="8e286-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="8e286-109">Autoryzacja oparta na oświadczeniach, w najprostszy sposób, sprawdza wartość oświadczenia i zezwala na dostęp do zasobu na podstawie tej wartości.</span><span class="sxs-lookup"><span data-stu-id="8e286-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="8e286-110">Na przykład jeśli chcesz uzyskać dostęp do klubu nocnego, proces autoryzacji może być:</span><span class="sxs-lookup"><span data-stu-id="8e286-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="8e286-111">Specjalista ds. zabezpieczeń analizuje wartość daty wystąpienia urodzenia i czy ufa wystawcy (urząd certyfikacji kierowania) przed udzieleniem dostępu.</span><span class="sxs-lookup"><span data-stu-id="8e286-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="8e286-112">Tożsamość może zawierać wiele oświadczeń z wieloma wartościami i może zawierać wiele oświadczeń tego samego typu.</span><span class="sxs-lookup"><span data-stu-id="8e286-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="8e286-113">Dodawanie sprawdzania oświadczeń</span><span class="sxs-lookup"><span data-stu-id="8e286-113">Adding claims checks</span></span>

<span data-ttu-id="8e286-114">Kontrola autoryzacji oparta na oświadczeniach jest deklaratywna — deweloperzy są osadzani w kodzie, względem kontrolera lub akcji w ramach kontrolera, określając oświadczenia, których bieżący użytkownik musi posiadać, i opcjonalnie wartość, którą oświadczenie musi przechowywać, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="8e286-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="8e286-115">Wymagania dotyczące oświadczeń są oparte na zasadach, Deweloper musi skompilować i zarejestrować zasady wyrażające wymagania dotyczące oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="8e286-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="8e286-116">Najprostszy typ zasad dotyczących roszczeń szuka obecności roszczeń i nie sprawdza wartości.</span><span class="sxs-lookup"><span data-stu-id="8e286-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="8e286-117">Najpierw należy skompilować i zarejestrować zasady.</span><span class="sxs-lookup"><span data-stu-id="8e286-117">First you need to build and register the policy.</span></span> <span data-ttu-id="8e286-118">Odbywa się to w ramach konfiguracji usługi autoryzacji, która zwykle uczestniczy w `ConfigureServices()` pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="8e286-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="8e286-119">W takim przypadku `EmployeeOnly` zasady sprawdzają obecność `EmployeeNumber` roszczeń w bieżącej tożsamości.</span><span class="sxs-lookup"><span data-stu-id="8e286-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="8e286-120">Następnie należy zastosować zasady przy użyciu `Policy` właściwości w atrybucie, `AuthorizeAttribute` Aby określić nazwę zasad;</span><span class="sxs-lookup"><span data-stu-id="8e286-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="8e286-121">Ten `AuthorizeAttribute` atrybut może być stosowany do całego kontrolera, w tym wystąpieniu tylko tożsamości pasujące do zasad będą mieć dostęp do dowolnej akcji na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="8e286-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="8e286-122">Jeśli masz kontroler, który jest chroniony przez `AuthorizeAttribute` atrybut, ale chcesz zezwolić na dostęp anonimowy do określonych akcji, należy zastosować `AllowAnonymousAttribute` atrybut.</span><span class="sxs-lookup"><span data-stu-id="8e286-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="8e286-123">Większość oświadczeń ma wartość.</span><span class="sxs-lookup"><span data-stu-id="8e286-123">Most claims come with a value.</span></span> <span data-ttu-id="8e286-124">Podczas tworzenia zasad można określić listę dozwolonych wartości.</span><span class="sxs-lookup"><span data-stu-id="8e286-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="8e286-125">Poniższy przykład powiedzie się tylko dla pracowników, których numer pracownika to 1, 2, 3, 4 lub 5.</span><span class="sxs-lookup"><span data-stu-id="8e286-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="8e286-126">Dodawanie ogólnego sprawdzania roszczeń</span><span class="sxs-lookup"><span data-stu-id="8e286-126">Add a generic claim check</span></span>

<span data-ttu-id="8e286-127">Jeśli wartość oświadczenia nie jest pojedynczą wartością lub wymagana jest transformacja, użyj [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="8e286-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="8e286-128">Aby uzyskać więcej informacji, zobacz [Używanie funkcji Func do realizacji zasad](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="8e286-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="8e286-129">Obliczanie wielu zasad</span><span class="sxs-lookup"><span data-stu-id="8e286-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="8e286-130">W przypadku zastosowania wielu zasad do kontrolera lub akcji wszystkie zasady muszą zostać przekazane przed udzieleniem dostępu.</span><span class="sxs-lookup"><span data-stu-id="8e286-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="8e286-131">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8e286-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="8e286-132">W powyższym przykładzie Każda tożsamość, która spełnia `EmployeeOnly` zasady, może uzyskać dostęp do `Payslip` akcji, ponieważ te zasady są wymuszane na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="8e286-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="8e286-133">Jednak w celu wywołania `UpdateSalary` akcji tożsamość musi spełniać *zarówno* `EmployeeOnly` zasady, jak i `HumanResources` zasady.</span><span class="sxs-lookup"><span data-stu-id="8e286-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="8e286-134">Jeśli potrzebujesz bardziej skomplikowanych zasad, takich jak pobieranie daty wystąpienia urodzenia, obliczanie wieku od IT, sprawdzenie wieku wynosi 21 lub starsze, należy napisać [niestandardowe programy obsługi zasad](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="8e286-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
