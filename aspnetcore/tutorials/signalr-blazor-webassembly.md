---
title: 'Użyj ASP.NET Core SignalR z Blazor WebAssembly'
author: guardrex
description: 'Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor WebAssembly .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 81cbfb692ffbd0bb6335ccef6dd10ad6c20fb334
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052711"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="1fe59-103">Użyj ASP.NET Core SignalR z Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1fe59-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="1fe59-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1fe59-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1fe59-105">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="1fe59-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="1fe59-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="1fe59-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1fe59-107">Tworzenie Blazor WebAssembly projektu hostowanej aplikacji</span><span class="sxs-lookup"><span data-stu-id="1fe59-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="1fe59-108">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1fe59-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="1fe59-109">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1fe59-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="1fe59-110">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1fe59-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="1fe59-111">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1fe59-111">Add Razor component code for chat</span></span>

<span data-ttu-id="1fe59-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="1fe59-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1fe59-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1fe59-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="1fe59-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1fe59-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe59-115">Visual Studio</span></span>](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* <span data-ttu-id="1fe59-116">[Program Visual Studio 2019 16,8 lub nowszy (w wersji zapoznawczej)](https://visualstudio.microsoft.com/vs/preview/) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="1fe59-116">[Visual Studio 2019 16.8 or later (in preview)](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1fe59-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1fe59-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fe59-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1fe59-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [<span data-ttu-id="1fe59-119">Visual Studio dla komputerów Mac wersja 8,8 lub nowsza (w wersji zapoznawczej)</span><span class="sxs-lookup"><span data-stu-id="1fe59-119">Visual Studio for Mac version 8.8 or later (in preview)</span></span>](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1fe59-120">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fe59-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1fe59-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe59-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1fe59-122">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="1fe59-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1fe59-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1fe59-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fe59-124">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1fe59-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1fe59-125">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="1fe59-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1fe59-126">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fe59-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="1fe59-127">Tworzenie projektu hostowanej Blazor WebAssembly aplikacji</span><span class="sxs-lookup"><span data-stu-id="1fe59-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="1fe59-128">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="1fe59-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fe59-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe59-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1fe59-130">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1fe59-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1fe59-131">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1fe59-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="1fe59-132">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-132">Create a new project.</span></span>

1. <span data-ttu-id="1fe59-133">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-133">Select **Blazor App** and select **Next** .</span></span>

1. <span data-ttu-id="1fe59-134">Wpisz `BlazorSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="1fe59-135">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1fe59-136">Wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-136">Select **Create** .</span></span>

1. <span data-ttu-id="1fe59-137">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="1fe59-138">W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="1fe59-139">Wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-139">Select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1fe59-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1fe59-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1fe59-141">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1fe59-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="1fe59-142">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1fe59-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="1fe59-143">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-143">When the dialog appears to add assets to build and debug the app, select **Yes** .</span></span> <span data-ttu-id="1fe59-144">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="1fe59-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fe59-145">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1fe59-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1fe59-146">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="1fe59-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="1fe59-147">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-147">Select **File** > **New Solution** or create a **New** project from the **Start Window** .</span></span>

1. <span data-ttu-id="1fe59-148">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-148">In the sidebar, select **Web and Console** > **App** .</span></span>

1. <span data-ttu-id="1fe59-149">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1fe59-150">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-150">Select **Next** .</span></span>

1. <span data-ttu-id="1fe59-151">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-151">Confirm that **Authentication** is set to **No Authentication** .</span></span> <span data-ttu-id="1fe59-152">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="1fe59-153">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-153">Select **Next** .</span></span>

1. <span data-ttu-id="1fe59-154">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="1fe59-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="1fe59-155">Wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-155">Select **Create** .</span></span>

   <span data-ttu-id="1fe59-156">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="1fe59-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1fe59-157">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="1fe59-158">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="1fe59-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fe59-159">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fe59-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1fe59-160">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1fe59-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="1fe59-161">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1fe59-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fe59-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe59-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="1fe59-163">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-163">In **Solution Explorer** , right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages** .</span></span>

1. <span data-ttu-id="1fe59-164">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1fe59-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1fe59-165">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1fe59-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1fe59-166">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet i wybierz pozycję **Zainstaluj** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install** .</span></span>

1. <span data-ttu-id="1fe59-167">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-167">If the **Preview Changes** dialog appears, select **OK** .</span></span>

1. <span data-ttu-id="1fe59-168">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1fe59-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1fe59-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1fe59-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="1fe59-170">W **zintegrowanym terminalu** ( **Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1fe59-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fe59-171">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1fe59-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1fe59-172">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages** .</span></span>

1. <span data-ttu-id="1fe59-173">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1fe59-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1fe59-174">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1fe59-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1fe59-175">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu i wybierz polecenie **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="1fe59-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package** .</span></span>

1. <span data-ttu-id="1fe59-176">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1fe59-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fe59-177">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fe59-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1fe59-178">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1fe59-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="1fe59-179">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1fe59-179">Add a SignalR hub</span></span>

<span data-ttu-id="1fe59-180">W `BlazorSignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="1fe59-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="1fe59-181">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1fe59-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="1fe59-182">W `BlazorSignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="1fe59-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="1fe59-183">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="1fe59-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="1fe59-184">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1fe59-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="1fe59-185">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1fe59-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="1fe59-186">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="1fe59-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="1fe59-187">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="1fe59-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="1fe59-188">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1fe59-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="1fe59-189">W `BlazorSignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="1fe59-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="1fe59-190">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1fe59-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="1fe59-191">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1fe59-191">Run the app</span></span>

1. <span data-ttu-id="1fe59-192">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="1fe59-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fe59-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe59-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1fe59-194">W **Eksplorator rozwiązań** wybierz `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="1fe59-194">In **Solution Explorer** , select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="1fe59-195">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1fe59-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1fe59-196">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1fe59-197">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1fe59-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1fe59-198">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1fe59-198">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1fe59-200">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1fe59-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1fe59-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1fe59-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1fe59-202">Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="1fe59-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="1fe59-203">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1fe59-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1fe59-204">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1fe59-205">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1fe59-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1fe59-206">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1fe59-206">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1fe59-208">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1fe59-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fe59-209">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1fe59-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1fe59-210">Na pasku bocznym **rozwiązania** wybierz `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="1fe59-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="1fe59-211">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1fe59-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1fe59-212">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1fe59-213">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1fe59-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1fe59-214">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1fe59-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1fe59-216">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1fe59-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fe59-217">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fe59-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="1fe59-218">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1fe59-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="1fe59-219">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1fe59-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1fe59-220">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1fe59-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1fe59-221">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1fe59-221">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1fe59-223">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1fe59-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="1fe59-224">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="1fe59-224">Next steps</span></span>

<span data-ttu-id="1fe59-225">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="1fe59-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1fe59-226">Tworzenie Blazor WebAssembly projektu hostowanej aplikacji</span><span class="sxs-lookup"><span data-stu-id="1fe59-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="1fe59-227">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1fe59-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="1fe59-228">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1fe59-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="1fe59-229">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1fe59-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="1fe59-230">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1fe59-230">Add Razor component code for chat</span></span>

<span data-ttu-id="1fe59-231">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="1fe59-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="1fe59-232"><xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="1fe59-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1fe59-233">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1fe59-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="1fe59-234">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1fe59-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
