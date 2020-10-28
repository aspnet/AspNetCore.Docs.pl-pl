---
title: 'Zabezpieczanie hostowanej :::no-loc(Blazor WebAssembly)::: aplikacji ASP.NET Core z :::no-loc(Identity)::: serwerem'
author: guardrex
description: 'Dowiedz się, jak zabezpieczyć hostowaną :::no-loc(Blazor WebAssembly)::: aplikację ASP.NET Core z :::no-loc(Identity)::: serwerem.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: a6fd005e19f532089ac1a1914756fb03eabb24c4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690474"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="de79a-103">Zabezpieczanie :::no-loc(Blazor WebAssembly)::: hostowanej aplikacji ASP.NET Core :::no-loc(Identity)::: z serwerem</span><span class="sxs-lookup"><span data-stu-id="de79a-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="de79a-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="de79a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="de79a-105">W tym artykule wyjaśniono, jak utworzyć [hostowaną :::no-loc(Blazor WebAssembly)::: aplikację](xref:blazor/hosting-models#blazor-webassembly) , która używa [ :::no-loc(Identity)::: serwera](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="de79a-105">This article explains how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [:::no-loc(Identity):::Server](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="de79a-106">Aby skonfigurować autonomiczną lub hostowaną :::no-loc(Blazor WebAssembly)::: aplikację do korzystania z istniejącego :::no-loc(Identity)::: wystąpienia serwera zewnętrznego, postępuj zgodnie ze wskazówkami w temacie <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="de79a-106">To configure a standalone or hosted :::no-loc(Blazor WebAssembly)::: app to use an existing, external :::no-loc(Identity)::: Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de79a-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de79a-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de79a-108">Aby utworzyć nowy :::no-loc(Blazor WebAssembly)::: projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="de79a-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="de79a-109">Po wybraniu szablonu **:::no-loc(Blazor WebAssembly)::: aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="de79a-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

1. <span data-ttu-id="de79a-110">Wybierz opcję **konta poszczególnych użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby przechowywać użytkowników w ramach aplikacji przy użyciu [:::no-loc(Identity):::](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de79a-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="de79a-111">Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .</span><span class="sxs-lookup"><span data-stu-id="de79a-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="de79a-112">Visual Studio Code/interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="de79a-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="de79a-113">Aby utworzyć nowy :::no-loc(Blazor WebAssembly)::: projekt z mechanizmem uwierzytelniania w pustym folderze, określ `Individual` mechanizm uwierzytelniania z `-au|--auth` opcją zapisania użytkowników w aplikacji przy użyciu [:::no-loc(Identity):::](xref:security/authentication/identity) systemu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="de79a-113">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="de79a-114">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="de79a-114">Placeholder</span></span>  | <span data-ttu-id="de79a-115">Przykład</span><span class="sxs-lookup"><span data-stu-id="de79a-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="de79a-116">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="de79a-117">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="de79a-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de79a-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de79a-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="de79a-119">Aby utworzyć nowy :::no-loc(Blazor WebAssembly)::: projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="de79a-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="de79a-120">W kroku **Skonfiguruj nową :::no-loc(Blazor WebAssembly)::: aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="de79a-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="de79a-121">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="de79a-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="de79a-122">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="de79a-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="de79a-123">*`Server`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="de79a-123">*`Server`* app configuration</span></span>

<span data-ttu-id="de79a-124">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="de79a-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="de79a-125">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="de79a-125">Startup class</span></span>

<span data-ttu-id="de79a-126">`Startup`Klasa zawiera następujące dodatki.</span><span class="sxs-lookup"><span data-stu-id="de79a-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="de79a-127">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="de79a-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="de79a-128">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="de79a-128">:::no-loc(ASP.NET Core Identity)::::</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="de79a-129">:::no-loc(Identity):::Serwer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocnika, która konfiguruje domyślne konwencje ASP.NET Core na :::no-loc(Identity)::: serwerze:</span><span class="sxs-lookup"><span data-stu-id="de79a-129">:::no-loc(Identity):::Server with an additional <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="de79a-130">Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez :::no-loc(Identity)::: serwer:</span><span class="sxs-lookup"><span data-stu-id="de79a-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="de79a-131">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="de79a-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="de79a-132">:::no-loc(Identity):::Oprogramowanie pośredniczące serwera uwidacznia punkty końcowe OpenID Connect Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="de79a-132">The :::no-loc(Identity):::Server middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

  * <span data-ttu-id="de79a-133">Oprogramowanie pośredniczące uwierzytelniania jest odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="de79a-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="de79a-134">Oprogramowanie pośredniczące autoryzacji włącza funkcje autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="de79a-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="de79a-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="de79a-135">AddApiAuthorization</span></span>

<span data-ttu-id="de79a-136"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Metoda pomocnika konfiguruje [ :::no-loc(Identity)::: serwer](https://identityserver.io/) dla scenariuszy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de79a-136">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [:::no-loc(Identity):::Server](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="de79a-137">:::no-loc(Identity):::Serwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-137">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="de79a-138">:::no-loc(Identity):::Serwer uwidacznia niepotrzebną złożoność dla najbardziej typowych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="de79a-138">:::no-loc(Identity):::Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="de79a-139">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="de79a-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="de79a-140">Gdy uwierzytelnianie wymaga zmiany, :::no-loc(Identity)::: dostępna jest pełna moc serwera, aby dostosować uwierzytelnianie do wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-140">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="de79a-141">Dodaj :::no-loc(Identity)::: ServerJwt</span><span class="sxs-lookup"><span data-stu-id="de79a-141">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="de79a-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="de79a-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="de79a-143">Zasady są skonfigurowane tak, aby zezwalały :::no-loc(Identity)::: na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w :::no-loc(Identity)::: przestrzeni adresów URL `/:::no-loc(Identity):::` .</span><span class="sxs-lookup"><span data-stu-id="de79a-143">The policy is configured to allow :::no-loc(Identity)::: to handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space `/:::no-loc(Identity):::`.</span></span> <span data-ttu-id="de79a-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="de79a-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="de79a-145">Ponadto ta metoda:</span><span class="sxs-lookup"><span data-stu-id="de79a-145">Additionally, this method:</span></span>

* <span data-ttu-id="de79a-146">Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z :::no-loc(Identity)::: serwerem z domyślnym zakresem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="de79a-146">Registers an `{APPLICATION NAME}API` API resource with :::no-loc(Identity):::Server with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="de79a-147">Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez :::no-loc(Identity)::: serwer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-147">Configures the JWT Bearer Token Middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="de79a-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="de79a-148">WeatherForecastController</span></span>

<span data-ttu-id="de79a-149">W `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy.</span><span class="sxs-lookup"><span data-stu-id="de79a-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="de79a-150">Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="de79a-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="de79a-151">Domyślne zasady autoryzacji są skonfigurowane do używania domyślnego schematu uwierzytelniania, który jest konfigurowany przez program <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="de79a-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>.</span></span> <span data-ttu-id="de79a-152">Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="de79a-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="de79a-153">ApplicationDbContext</span></span>

<span data-ttu-id="de79a-154">W programie `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) program <xref:Microsoft.EntityFrameworkCore.DbContext> rozszerza, <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> Aby uwzględnić schemat dla :::no-loc(Identity)::: serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> to include the schema for :::no-loc(Identity):::Server.</span></span> <span data-ttu-id="de79a-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> pochodzi od <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> .</span><span class="sxs-lookup"><span data-stu-id="de79a-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span></span>

<span data-ttu-id="de79a-156">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić :::no-loc(Identity)::: schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="de79a-156">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="de79a-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="de79a-157">OidcConfigurationController</span></span>

<span data-ttu-id="de79a-158">W `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.</span><span class="sxs-lookup"><span data-stu-id="de79a-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="de79a-159">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="de79a-159">App settings</span></span>

<span data-ttu-id="de79a-160">W sekcji Ustawienia aplikacji ( `appsettings.json` ) w katalogu głównym projektu `:::no-loc(Identity):::Server` sekcja zawiera opis listy skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="de79a-160">In the app settings file (`appsettings.json`) at the project root, the `:::no-loc(Identity):::Server` section describes the list of configured clients.</span></span> <span data-ttu-id="de79a-161">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="de79a-161">In the following example, there's a single client.</span></span> <span data-ttu-id="de79a-162">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth.</span><span class="sxs-lookup"><span data-stu-id="de79a-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="de79a-163">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="de79a-164">Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

<span data-ttu-id="de79a-165">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `:::no-loc(Blazor):::Sample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="de79a-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="de79a-166">*`Client`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="de79a-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="de79a-167">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="de79a-167">Authentication package</span></span>

<span data-ttu-id="de79a-168">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="de79a-169">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="de79a-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="de79a-170">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="de79a-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="de79a-171">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="de79a-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="de79a-172">`HttpClient` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="de79a-172">`HttpClient` configuration</span></span>

<span data-ttu-id="de79a-173">W programie `Program.Main` ( `Program.cs` ) nazwa <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) jest skonfigurowana do dostarczania <xref:System.Net.Http.HttpClient> wystąpień, które zawierają tokeny dostępu podczas wykonywania żądań do interfejsu API serwera:</span><span class="sxs-lookup"><span data-stu-id="de79a-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="de79a-174">Jeśli konfigurujesz :::no-loc(Blazor WebAssembly)::: aplikację tak, aby korzystała z istniejącego :::no-loc(Identity)::: wystąpienia serwera, które nie jest częścią rozwiązania hostowanego :::no-loc(Blazor)::: , Zmień <xref:System.Net.Http.HttpClient> rejestrację adresu podstawowego z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adres URL punktu końcowego autoryzacji interfejsu API aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-174">If you're configuring a :::no-loc(Blazor WebAssembly)::: app to use an existing :::no-loc(Identity)::: Server instance that isn't part of a hosted :::no-loc(Blazor)::: solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="de79a-175">Obsługa autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="de79a-175">API authorization support</span></span>

<span data-ttu-id="de79a-176">Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakiecie.</span><span class="sxs-lookup"><span data-stu-id="de79a-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="de79a-177">Ta metoda konfiguruje usługi wymagane przez aplikację do współdziałania z istniejącym systemem autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="de79a-178">Domyślnie konfiguracja aplikacji jest ładowana zgodnie z Konwencją z programu `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="de79a-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="de79a-179">Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="de79a-180">Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.</span><span class="sxs-lookup"><span data-stu-id="de79a-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="de79a-181">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="de79a-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="de79a-182">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="de79a-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="de79a-183">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="de79a-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="de79a-184">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="de79a-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="de79a-185">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="de79a-185">LoginDisplay component</span></span>

<span data-ttu-id="de79a-186">`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="de79a-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="de79a-187">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="de79a-187">For authenticated users:</span></span>
  * <span data-ttu-id="de79a-188">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="de79a-188">Displays the current user name.</span></span>
  * <span data-ttu-id="de79a-189">Oferuje link do strony profilu użytkownika w programie :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="de79a-189">Offers a link to the user profile page in :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="de79a-190">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="de79a-191">Dla użytkowników anonimowych:</span><span class="sxs-lookup"><span data-stu-id="de79a-191">For anonymous users:</span></span>
  * <span data-ttu-id="de79a-192">Oferuje opcję rejestracji.</span><span class="sxs-lookup"><span data-stu-id="de79a-192">Offers the option to register.</span></span>
  * <span data-ttu-id="de79a-193">Oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="de79a-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.:::no-loc(Identity):::.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="de79a-194">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="de79a-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="de79a-195">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="de79a-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="de79a-196">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="de79a-196">Run the app</span></span>

<span data-ttu-id="de79a-197">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-197">Run the app from the Server project.</span></span> <span data-ttu-id="de79a-198">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="de79a-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="de79a-199">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="de79a-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="de79a-200">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="de79a-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="de79a-201">Nazwa i rola roli z autoryzacją interfejsu API</span><span class="sxs-lookup"><span data-stu-id="de79a-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="de79a-202">Niestandardowa fabryka użytkowników</span><span class="sxs-lookup"><span data-stu-id="de79a-202">Custom user factory</span></span>

<span data-ttu-id="de79a-203">W *`Client`* aplikacji Utwórz niestandardową fabrykę użytkowników.</span><span class="sxs-lookup"><span data-stu-id="de79a-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="de79a-204">:::no-loc(Identity)::: Serwer wysyła wiele ról jako tablicę JSON w ramach pojedynczego `role` żądania.</span><span class="sxs-lookup"><span data-stu-id="de79a-204">:::no-loc(Identity)::: Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="de79a-205">Pojedyncza rola jest wysyłana jako wartość ciągu w ramach żądania.</span><span class="sxs-lookup"><span data-stu-id="de79a-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="de79a-206">Fabryka tworzy pojedyncze zgłoszenie `role` dla każdej z ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="de79a-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="de79a-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="de79a-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            var identity = (Claims:::no-loc(Identity):::)user.:::no-loc(Identity):::;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="de79a-208">W *`Client`* aplikacji Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="de79a-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="de79a-209">W *`Server`* aplikacji Zadzwoń do <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> :::no-loc(Identity)::: konstruktora, który dodaje usługi związane z rolami:</span><span class="sxs-lookup"><span data-stu-id="de79a-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> on the :::no-loc(Identity)::: builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::;

...

services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<:::no-loc(Identity):::Role>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="de79a-210">Skonfiguruj :::no-loc(Identity)::: serwer</span><span class="sxs-lookup"><span data-stu-id="de79a-210">Configure :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="de79a-211">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="de79a-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="de79a-212">Opcje autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="de79a-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="de79a-213">Usługa profilowania</span><span class="sxs-lookup"><span data-stu-id="de79a-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="de79a-214">Opcje autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="de79a-214">API authorization options</span></span>

<span data-ttu-id="de79a-215">W *`Server`* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="de79a-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="de79a-216">Skonfiguruj :::no-loc(Identity)::: serwer, aby umieścić `name` oświadczenia i w tokenie `role` dostępu.</span><span class="sxs-lookup"><span data-stu-id="de79a-216">Configure :::no-loc(Identity)::: Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="de79a-217">Zapobiegaj domyślnym mapowaniu ról w programie obsługi tokenów JWT.</span><span class="sxs-lookup"><span data-stu-id="de79a-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Linq;

...

services.Add:::no-loc(Identity):::Server()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="de79a-218">Usługa profilowania</span><span class="sxs-lookup"><span data-stu-id="de79a-218">Profile Service</span></span>

<span data-ttu-id="de79a-219">W *`Server`* aplikacji Utwórz `ProfileService` implementację.</span><span class="sxs-lookup"><span data-stu-id="de79a-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="de79a-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="de79a-220">`ProfileService.cs`:</span></span>

```csharp
using :::no-loc(Identity):::Model;
using :::no-loc(Identity):::Server4.Models;
using :::no-loc(Identity):::Server4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="de79a-221">W *`Server`* aplikacji Zarejestruj usługę profilu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de79a-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using :::no-loc(Identity):::Server4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="de79a-222">Korzystanie z mechanizmów autoryzacji</span><span class="sxs-lookup"><span data-stu-id="de79a-222">Use authorization mechanisms</span></span>

<span data-ttu-id="de79a-223">W *`Client`* aplikacji metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="de79a-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="de79a-224">Każdy mechanizm autoryzacji w składnikach może używać roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="de79a-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="de79a-225">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="de79a-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="de79a-226">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="de79a-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="de79a-227">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="de79a-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="de79a-228">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="de79a-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="de79a-229">`User.:::no-loc(Identity):::.Name` jest wypełniana w *`Client`* aplikacji nazwą użytkownika, która jest zazwyczaj ich adresem e-mail logowania.</span><span class="sxs-lookup"><span data-stu-id="de79a-229">`User.:::no-loc(Identity):::.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="de79a-230">Host w Azure App Service z domeną niestandardową</span><span class="sxs-lookup"><span data-stu-id="de79a-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="de79a-231">Poniższe wskazówki wyjaśniają sposób wdrażania hostowanej :::no-loc(Blazor WebAssembly)::: aplikacji na :::no-loc(Identity)::: serwerze w celu [Azure App Service](https://azure.microsoft.com/services/app-service/) z domeną niestandardową.</span><span class="sxs-lookup"><span data-stu-id="de79a-231">The following guidance explains how to deploy a hosted :::no-loc(Blazor WebAssembly)::: app with :::no-loc(Identity)::: Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="de79a-232">W tym scenariuszu hostingu **nie** należy używać tego samego certyfikatu dla [ :::no-loc(Identity)::: klucza podpisywania tokenu serwera](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) i bezpiecznej komunikacji protokołu HTTPS lokacji z przeglądarkami:</span><span class="sxs-lookup"><span data-stu-id="de79a-232">For this hosting scenario, do **not** use the same certificate for [:::no-loc(Identity)::: Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="de79a-233">Korzystanie z różnych certyfikatów dla tych dwóch wymagań jest dobrym sposobem na bezpieczeństwo, ponieważ izoluje klucze prywatne do każdego celu.</span><span class="sxs-lookup"><span data-stu-id="de79a-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="de79a-234">Certyfikaty TLS do komunikacji z przeglądarkami są zarządzane niezależnie bez wpływu na :::no-loc(Identity)::: Podpisywanie tokenu serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-234">TLS certificates for communication with browsers is managed independently without affecting :::no-loc(Identity)::: Server's token signing.</span></span>
* <span data-ttu-id="de79a-235">Gdy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dostarcza certyfikat do aplikacji App Service dla niestandardowego powiązania domeny, :::no-loc(Identity)::: serwer nie może uzyskać tego samego certyfikatu z Azure Key Vault na potrzeby podpisywania tokenu.</span><span class="sxs-lookup"><span data-stu-id="de79a-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, :::no-loc(Identity)::: Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="de79a-236">Chociaż skonfigurowanie :::no-loc(Identity)::: serwera do korzystania z tego samego certyfikatu TLS z ścieżki fizycznej jest możliwe, umieszczenie certyfikatów zabezpieczeń w kontroli źródła jest **słabo praktyczne i należy je unikać w większości scenariuszy** .</span><span class="sxs-lookup"><span data-stu-id="de79a-236">Although configuring :::no-loc(Identity)::: Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios** .</span></span>

<span data-ttu-id="de79a-237">W poniższych wskazówkach certyfikat z podpisem własnym jest tworzony w Azure Key Vault wyłącznie dla :::no-loc(Identity)::: podpisywania tokenu serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="de79a-238">:::no-loc(Identity):::Konfiguracja serwera używa certyfikatu magazynu kluczy za pośrednictwem `My`  >  `CurrentUser` magazynu certyfikatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-238">The :::no-loc(Identity)::: Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="de79a-239">Inne certyfikaty używane na potrzeby ruchu HTTPS z domenami niestandardowymi są tworzone i konfigurowane niezależnie od :::no-loc(Identity)::: certyfikatu podpisywania serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the :::no-loc(Identity)::: Server signing certificate.</span></span>

<span data-ttu-id="de79a-240">Aby skonfigurować aplikację, Azure App Service i Azure Key Vault do hostowania przy użyciu domeny niestandardowej i protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="de79a-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="de79a-241">Utwórz [plan App Service](/azure/app-service/overview-hosting-plans) z poziomem planu `Basic B1` lub wyższym.</span><span class="sxs-lookup"><span data-stu-id="de79a-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="de79a-242">`Basic B1`Aby można było używać domen niestandardowych, App Service wymaga lub wyższej warstwy usług.</span><span class="sxs-lookup"><span data-stu-id="de79a-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="de79a-243">Utwórz certyfikat PFX dla bezpiecznej komunikacji w przeglądarce (protokołu HTTPS) dla witryny o wspólnej nazwie w pełni kwalifikowanej nazwie domeny (FQDN) witryny, która kontroluje Twoja organizacja (na przykład `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="de79a-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="de79a-244">Utwórz certyfikat przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="de79a-244">Create the certificate with:</span></span>
   * <span data-ttu-id="de79a-245">Użycie klucza</span><span class="sxs-lookup"><span data-stu-id="de79a-245">Key uses</span></span>
     * <span data-ttu-id="de79a-246">Weryfikacja podpisu cyfrowego ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="de79a-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="de79a-247">Szyfrowanie klucza ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="de79a-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="de79a-248">Ulepszone/rozszerzone użycie klucza</span><span class="sxs-lookup"><span data-stu-id="de79a-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="de79a-249">Uwierzytelnianie klienta (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="de79a-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="de79a-250">Uwierzytelnianie serwera (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="de79a-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="de79a-251">Aby utworzyć certyfikat, należy użyć jednej z następujących metod lub innego odpowiedniego narzędzia lub usługi online:</span><span class="sxs-lookup"><span data-stu-id="de79a-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="de79a-252">Usługa Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="de79a-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="de79a-253">MakeCert w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="de79a-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="de79a-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="de79a-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="de79a-255">Zanotuj hasło, które zostanie użyte później do zaimportowania certyfikatu do Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="de79a-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="de79a-256">Aby uzyskać więcej informacji na temat Azure Key Vault certyfikatów, zobacz [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="de79a-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="de79a-257">Utwórz nowy Azure Key Vault lub Użyj istniejącego magazynu kluczy w subskrypcji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="de79a-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="de79a-258">W obszarze **Certyfikaty** magazynu kluczy zaimportuj certyfikat lokacji PFX.</span><span class="sxs-lookup"><span data-stu-id="de79a-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="de79a-259">Zanotuj odcisk palca certyfikatu, który jest używany później w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="de79a-260">W Azure Key Vault Wygeneruj nowy certyfikat z podpisem własnym na potrzeby :::no-loc(Identity)::: podpisywania tokenu serwera.</span><span class="sxs-lookup"><span data-stu-id="de79a-260">In Azure Key Vault, generate a new self-signed certificate for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="de79a-261">Nadaj certyfikatowi nazwę i **temat** **certyfikatu** .</span><span class="sxs-lookup"><span data-stu-id="de79a-261">Give the certificate a **Certificate Name** and **Subject** .</span></span> <span data-ttu-id="de79a-262">**Podmiot** jest określony jako `CN={COMMON NAME}` , gdzie `{COMMON NAME}` symbol zastępczy jest nazwą pospolitą certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="de79a-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="de79a-263">Nazwa pospolita może być dowolnym ciągiem alfanumerycznym.</span><span class="sxs-lookup"><span data-stu-id="de79a-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="de79a-264">Na przykład `CN=:::no-loc(Identity):::ServerSigning` jest prawidłowy **podmiot** certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="de79a-264">For example, `CN=:::no-loc(Identity):::ServerSigning` is a valid certificate **Subject** .</span></span> <span data-ttu-id="de79a-265">Użyj domyślnych ustawień **konfiguracji zaawansowanych zasad** .</span><span class="sxs-lookup"><span data-stu-id="de79a-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="de79a-266">Zanotuj odcisk palca certyfikatu, który jest używany później w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="de79a-267">Przejdź do Azure App Service w Azure Portal i Utwórz nowy App Service z następującą konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="de79a-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="de79a-268">**Publikuj** ustawiony na `Code` .</span><span class="sxs-lookup"><span data-stu-id="de79a-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="de79a-269">**Stos środowiska uruchomieniowego** został ustawiony na środowisko uruchomieniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="de79a-270">W polu **jednostka SKU i rozmiar** upewnij się, że warstwa App Service jest `Basic B1` lub wyższa.</span><span class="sxs-lookup"><span data-stu-id="de79a-270">For **Sku and size** , confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="de79a-271">`Basic B1`Aby można było używać domen niestandardowych, App Service wymaga lub wyższej warstwy usług.</span><span class="sxs-lookup"><span data-stu-id="de79a-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="de79a-272">Po utworzeniu App Service przez platformę Azure Otwórz **konfigurację** aplikacji i Dodaj nowe ustawienie aplikacji określające wcześniej zarejestrowane odciski palców certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="de79a-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="de79a-273">Klucz ustawienia aplikacji to `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="de79a-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="de79a-274">Oddziel odciski palców certyfikatu w wartości ustawienia aplikacji na przecinek, jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="de79a-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="de79a-275">Klucz: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="de79a-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="de79a-276">Wartość: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="de79a-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="de79a-277">W Azure Portal Zapisywanie ustawień aplikacji jest procesem dwuetapowym: Zapisz `WEBSITE_LOAD_CERTIFICATES` ustawienie klucz-wartość, a następnie wybierz przycisk **Zapisz** w górnej części bloku.</span><span class="sxs-lookup"><span data-stu-id="de79a-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="de79a-278">Wybierz **Ustawienia protokołu TLS/SSL** aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de79a-278">Select the app's **TLS/SSL settings** .</span></span> <span data-ttu-id="de79a-279">Wybierz pozycję **certyfikaty kluczy prywatnych (pfx)** .</span><span class="sxs-lookup"><span data-stu-id="de79a-279">Select **Private Key Certificates (.pfx)** .</span></span> <span data-ttu-id="de79a-280">W celu zaimportowania certyfikatu lokacji na potrzeby komunikacji przy użyciu protokołu HTTPS i certyfikatu podpisywania serwera z podpisem własnym należy użyć dwa razy **zaimportuj certyfikat Key Vault** :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="de79a-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed :::no-loc(Identity)::: Server token signing certificate.</span></span>
1. <span data-ttu-id="de79a-281">Przejdź do bloku **domeny niestandardowe** .</span><span class="sxs-lookup"><span data-stu-id="de79a-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="de79a-282">W witrynie sieci Web rejestratora domen Użyj **adresu IP** i **identyfikatora weryfikacji domeny niestandardowej** w celu skonfigurowania domeny.</span><span class="sxs-lookup"><span data-stu-id="de79a-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="de79a-283">Typowa konfiguracja domeny obejmuje:</span><span class="sxs-lookup"><span data-stu-id="de79a-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="de79a-284">**Rekord a** **hosta** `@` i wartość adresu IP z Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="de79a-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="de79a-285">**Rekord TXT** z **hostem** `asuid` i wartością identyfikatora weryfikacyjnego wygenerowaną przez platformę Azure i udostępnianą przez Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="de79a-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="de79a-286">Upewnij się, że poprawnie Zapisano zmiany w witrynie sieci Web rejestratora domen.</span><span class="sxs-lookup"><span data-stu-id="de79a-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="de79a-287">Niektóre witryny sieci Web rejestratorów wymagają dwuetapowego procesu zapisywania rekordów domeny: co najmniej jeden rekord jest zapisywany osobno, a następnie przez aktualizację rejestracji domeny za pomocą oddzielnego przycisku.</span><span class="sxs-lookup"><span data-stu-id="de79a-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="de79a-288">Wróć do bloku **domen niestandardowych** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="de79a-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="de79a-289">Wybierz pozycję **Dodaj domenę niestandardową** .</span><span class="sxs-lookup"><span data-stu-id="de79a-289">Select **Add custom domain** .</span></span> <span data-ttu-id="de79a-290">Wybierz opcję **rekordu A** .</span><span class="sxs-lookup"><span data-stu-id="de79a-290">Select the **A Record** option.</span></span> <span data-ttu-id="de79a-291">Podaj domenę i wybierz pozycję **Weryfikuj** .</span><span class="sxs-lookup"><span data-stu-id="de79a-291">Provide the domain and select **Validate** .</span></span> <span data-ttu-id="de79a-292">Jeśli rekordy domeny są poprawne i propagowane przez Internet, Portal umożliwia wybranie przycisku **Dodaj domenę niestandardową** .</span><span class="sxs-lookup"><span data-stu-id="de79a-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="de79a-293">Zmiana rejestracji domeny w ramach serwerów nazw domen internetowych (DNS) po ich przetworzeniu przez rejestrator domen może potrwać kilka dni.</span><span class="sxs-lookup"><span data-stu-id="de79a-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="de79a-294">Jeśli rekordy domeny nie są aktualizowane w ciągu trzech dni roboczych, upewnij się, że rekordy są poprawnie ustawione przy użyciu rejestratora domen i skontaktuj się z działem obsługi klienta.</span><span class="sxs-lookup"><span data-stu-id="de79a-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="de79a-295">W bloku **domeny niestandardowe** **stan protokołu SSL** dla domeny jest oznaczony `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="de79a-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="de79a-296">Wybierz łącze **Dodaj powiązanie** .</span><span class="sxs-lookup"><span data-stu-id="de79a-296">Select the **Add binding** link.</span></span> <span data-ttu-id="de79a-297">Wybierz certyfikat HTTPS lokacji z magazynu kluczy dla niestandardowego powiązania domeny.</span><span class="sxs-lookup"><span data-stu-id="de79a-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="de79a-298">W programie Visual Studio Otwórz plik ustawień aplikacji projektu *serwera* ( `appsettings.json` lub `appsettings.Production.json` ).</span><span class="sxs-lookup"><span data-stu-id="de79a-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="de79a-299">W :::no-loc(Identity)::: konfiguracji serwera Dodaj następującą `Key` sekcję.</span><span class="sxs-lookup"><span data-stu-id="de79a-299">In the :::no-loc(Identity)::: Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="de79a-300">Określ **podmiot** certyfikatu z podpisem własnym dla `Name` klucza.</span><span class="sxs-lookup"><span data-stu-id="de79a-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="de79a-301">W poniższym przykładzie nazwa pospolita certyfikatu przypisana w magazynie kluczy to `:::no-loc(Identity):::ServerSigning` , która daje **temat** `CN=:::no-loc(Identity):::ServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="de79a-301">In the following example, the certificate's common name assigned in the key vault is `:::no-loc(Identity):::ServerSigning`, which yields a **Subject** of `CN=:::no-loc(Identity):::ServerSigning`:</span></span>

   ```json
   ":::no-loc(Identity):::Server": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=:::no-loc(Identity):::ServerSigning"
     }
   },
   ```

1. <span data-ttu-id="de79a-302">W programie Visual Studio Utwórz [profil publikowania](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Azure App Service dla projektu *serwera* .</span><span class="sxs-lookup"><span data-stu-id="de79a-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="de79a-303">Na pasku menu wybierz kolejno opcje: **Kompiluj**  >  **Publikuj**  >  **nowe**  >  **Azure App Service platformy Azure**  >  **Azure App Service** (system Windows lub Linux).</span><span class="sxs-lookup"><span data-stu-id="de79a-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="de79a-304">Gdy program Visual Studio jest połączony z subskrypcją platformy Azure, można ustawić **Widok** zasobów platformy Azure według **typu zasobu** .</span><span class="sxs-lookup"><span data-stu-id="de79a-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type** .</span></span> <span data-ttu-id="de79a-305">Przejdź na listę **aplikacji sieci Web** , aby znaleźć App Service aplikacji i wybierz ją.</span><span class="sxs-lookup"><span data-stu-id="de79a-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="de79a-306">Wybierz pozycję **Zakończ** .</span><span class="sxs-lookup"><span data-stu-id="de79a-306">Select **Finish** .</span></span>
1. <span data-ttu-id="de79a-307">Gdy program Visual Studio powróci do okna **publikowania** , automatycznie wykrywane są zależności usługi key i SQL Server Database.</span><span class="sxs-lookup"><span data-stu-id="de79a-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="de79a-308">W przypadku usługi magazynu kluczy nie są wymagane żadne zmiany w konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="de79a-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="de79a-309">W celach testowych lokalna baza danych programu [SQLite](https://www.sqlite.org/index.html) , która jest konfigurowana domyślnie przez :::no-loc(Blazor)::: szablon, może być wdrażana z aplikacją bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="de79a-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the :::no-loc(Blazor)::: template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="de79a-310">Konfigurowanie innej bazy danych :::no-loc(Identity)::: serwera w środowisku produkcyjnym wykracza poza zakres tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="de79a-310">Configuring a different database for :::no-loc(Identity)::: Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="de79a-311">Aby uzyskać więcej informacji, zobacz zasoby bazy danych w następujących zestawach dokumentacji:</span><span class="sxs-lookup"><span data-stu-id="de79a-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="de79a-312">App Service</span><span class="sxs-lookup"><span data-stu-id="de79a-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="de79a-313">:::no-loc(Identity)::: Server</span><span class="sxs-lookup"><span data-stu-id="de79a-313">:::no-loc(Identity)::: Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="de79a-314">Wybierz łącze **Edytuj** pod nazwą profilu wdrożenia w górnej części okna.</span><span class="sxs-lookup"><span data-stu-id="de79a-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="de79a-315">Zmień docelowy adres URL na adres URL domeny niestandardowej witryny (na przykład `https://www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="de79a-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="de79a-316">Zapisz ustawienia.</span><span class="sxs-lookup"><span data-stu-id="de79a-316">Save the settings.</span></span>
1. <span data-ttu-id="de79a-317">Opublikuj aplikację.</span><span class="sxs-lookup"><span data-stu-id="de79a-317">Publish the app.</span></span> <span data-ttu-id="de79a-318">Program Visual Studio otwiera okno przeglądarki i żąda lokacji w swojej domenie niestandardowej.</span><span class="sxs-lookup"><span data-stu-id="de79a-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="de79a-319">Dokumentacja platformy Azure zawiera dodatkowe szczegóły dotyczące korzystania z usług platformy Azure i domen niestandardowych z powiązaniem TLS w App Service, w tym informacje na temat używania rekordów CNAME zamiast rekordów.</span><span class="sxs-lookup"><span data-stu-id="de79a-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="de79a-320">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="de79a-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="de79a-321">Dokumentacja App Service</span><span class="sxs-lookup"><span data-stu-id="de79a-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="de79a-322">Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="de79a-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="de79a-323">Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="de79a-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="de79a-324">Usługa Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="de79a-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="de79a-325">Zalecamy używanie nowego okna przeglądarki w trybie prywatnym lub incognito dla każdego przebiegu testu aplikacji po zmianie aplikacji, konfiguracji aplikacji lub usług platformy Azure w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="de79a-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="de79a-326">:::no-loc(cookie):::Wyzwolenie z poprzedniego przebiegu testowego może spowodować niepowodzenie uwierzytelniania lub autoryzacji podczas testowania lokacji nawet wtedy, gdy konfiguracja lokacji jest poprawna.</span><span class="sxs-lookup"><span data-stu-id="de79a-326">Lingering :::no-loc(cookie):::s from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="de79a-327">Aby uzyskać więcej informacji na temat sposobu konfigurowania programu Visual Studio, aby otworzyć nowe okno przeglądarki w trybie prywatnym lub incognito dla każdego przebiegu testowego, zobacz sekcję [ :::no-loc(Cookie)::: s i dane lokacji](#:::no-loc(cookie):::s-and-site-data) .</span><span class="sxs-lookup"><span data-stu-id="de79a-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [:::no-loc(Cookie):::s and site data](#:::no-loc(cookie):::s-and-site-data) section.</span></span>

<span data-ttu-id="de79a-328">Gdy App Service konfiguracja zostanie zmieniona w Azure Portal, aktualizacje zazwyczaj zaczną obowiązywać szybko, ale nie będą natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="de79a-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="de79a-329">Czasami przed ponownym uruchomieniem App Service należy zaczekać krótki okres, aby zmiany konfiguracji zaczęły obowiązywać.</span><span class="sxs-lookup"><span data-stu-id="de79a-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="de79a-330">W przypadku rozwiązywania problemów z ładowaniem certyfikatu wykonaj następujące polecenie w powłoce poleceń Azure Portal [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="de79a-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="de79a-331">Polecenie zawiera listę certyfikatów, do których aplikacja może uzyskać dostęp z `My`  >  `CurrentUser` magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="de79a-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="de79a-332">Dane wyjściowe obejmują tematy certyfikatów i odciski palców przydatne podczas debugowania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="de79a-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="de79a-333">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="de79a-333">Additional resources</span></span>

* [<span data-ttu-id="de79a-334">Wdrożenie do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="de79a-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="de79a-335">Importowanie certyfikatu z Key Vault (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="de79a-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="de79a-336">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="de79a-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
