---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Krótki samouczek, który tworzy i uruchamia podstawową aplikację Hello world przy użyciu ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
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
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057794"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="79327-103">Samouczek: wprowadzenie do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79327-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="79327-104">W tym samouczku pokazano, jak utworzyć i uruchomić aplikację sieci Web ASP.NET Core przy użyciu interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="79327-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="79327-105">Omawiane tematy:</span><span class="sxs-lookup"><span data-stu-id="79327-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="79327-106">Utwórz projekt aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="79327-106">Create a web app project.</span></span>
> * <span data-ttu-id="79327-107">Zaufaj certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="79327-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="79327-108">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="79327-108">Run the app.</span></span>
> * <span data-ttu-id="79327-109">Edytuj Razor stronę.</span><span class="sxs-lookup"><span data-stu-id="79327-109">Edit a Razor page.</span></span>

<span data-ttu-id="79327-110">Na końcu będziesz mieć działającą aplikację sieci Web na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="79327-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Strona główna aplikacji internetowej](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="79327-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="79327-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="79327-113">Tworzenie projektu aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="79327-113">Create a web app project</span></span>

<span data-ttu-id="79327-114">Otwórz powłokę poleceń i wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="79327-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="79327-115">Powyższe polecenie ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="79327-115">The preceding command:</span></span>

* <span data-ttu-id="79327-116">Tworzy nową aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="79327-116">Creates a new web app.</span></span>  
* <span data-ttu-id="79327-117">`-o aspnetcoreapp`Parametr tworzy katalog o nazwie *aspnetcoreapp* z plikami źródłowymi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79327-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="79327-118">Ufanie certyfikatowi Deweloperskiemu</span><span class="sxs-lookup"><span data-stu-id="79327-118">Trust the development certificate</span></span>

<span data-ttu-id="79327-119">Ufaj certyfikatowi deweloperskim HTTPS:</span><span class="sxs-lookup"><span data-stu-id="79327-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="79327-120">Windows</span><span class="sxs-lookup"><span data-stu-id="79327-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="79327-121">Poprzednie polecenie wyświetla następujące okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="79327-121">The preceding command displays the following dialog:</span></span>

![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

<span data-ttu-id="79327-123">Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="79327-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="79327-124">macOS</span><span class="sxs-lookup"><span data-stu-id="79327-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="79327-125">Poprzednie polecenie wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="79327-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="79327-126">*Zażądano zaufania certyfikatu deweloperskiego https. Jeśli certyfikat nie jest już zaufany, zostanie uruchomione następujące polecenie:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="79327-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="79327-127">To polecenie może wyświetlać monit o podanie hasła w celu zainstalowania certyfikatu w łańcuchu kluczy systemu.</span><span class="sxs-lookup"><span data-stu-id="79327-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="79327-128">Wprowadź hasło, jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="79327-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="79327-129">Linux</span><span class="sxs-lookup"><span data-stu-id="79327-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="79327-130">Zapoznaj się z dokumentacją dystrybucji systemu Linux dotyczącą sposobu zaufania certyfikatu deweloperskiego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="79327-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="79327-131">Aby uzyskać więcej informacji, zobacz artykuł [ufanie certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="79327-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="79327-132">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="79327-132">Run the app</span></span>

<span data-ttu-id="79327-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="79327-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="79327-134">Po powłoce poleceń wskazuje, że aplikacja została uruchomiona, przejdź do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="79327-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-no-locrazor-page"></a><span data-ttu-id="79327-135">Edytowanie Razor strony</span><span class="sxs-lookup"><span data-stu-id="79327-135">Edit a Razor page</span></span>

<span data-ttu-id="79327-136">Otwórz stronę *Pages/index. cshtml* i zmodyfikuj ją i Zapisz przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="79327-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="79327-137">Przejdź do `https://localhost:5001` strony, Odśwież stronę i sprawdź, czy są wyświetlane zmiany.</span><span class="sxs-lookup"><span data-stu-id="79327-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="79327-138">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="79327-138">Next steps</span></span>

<span data-ttu-id="79327-139">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="79327-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="79327-140">Utwórz projekt aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="79327-140">Create a web app project.</span></span>
> * <span data-ttu-id="79327-141">Zaufaj certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="79327-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="79327-142">Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="79327-142">Run the project.</span></span>
> * <span data-ttu-id="79327-143">Wprowadź zmianę.</span><span class="sxs-lookup"><span data-stu-id="79327-143">Make a change.</span></span>

<span data-ttu-id="79327-144">Aby dowiedzieć się więcej na temat ASP.NET Core, zapoznaj się ze zalecaną ścieżką szkoleniową we wprowadzeniu:</span><span class="sxs-lookup"><span data-stu-id="79327-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
