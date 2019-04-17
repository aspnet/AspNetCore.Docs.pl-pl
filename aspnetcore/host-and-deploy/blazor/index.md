---
title: Hostowanie i wdrażanie Blazor
author: guardrex
description: Dowiedz się, jak udostępniać i wdrażać aplikacje Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: a7739e2b240d7fd6c85e68105892c802228ebeb5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614842"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="8bbe1-103">Hostowanie i wdrażanie Blazor</span><span class="sxs-lookup"><span data-stu-id="8bbe1-103">Host and deploy Blazor</span></span>

<span data-ttu-id="8bbe1-104">Przez [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8bbe1-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="8bbe1-105">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="8bbe1-105">Publish the app</span></span>

<span data-ttu-id="8bbe1-106">Aplikacje są publikowane do wdrożenia w konfiguracji wydania przy użyciu [publikowania dotnet](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="8bbe1-107">Zintegrowanym środowisku programistycznym (IDE) może obsługiwać wykonywanie `dotnet publish` polecenia automatycznie przy użyciu jego wbudowanych funkcji publikowania, dzięki czemu może nie być konieczne ręczne wykonaj polecenie w wierszu polecenia, w zależności od rozwoju narzędzia w użyciu.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="8bbe1-108">`dotnet publish` Wyzwalacze [przywrócić](/dotnet/core/tools/dotnet-restore) zależności projektu i [kompilacje](/dotnet/core/tools/dotnet-build) projekcie przed utworzeniem zasobów dla wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="8bbe1-109">Jako część procesu kompilacji Aby zmniejszyć rozmiar pobierania aplikacji i czasów ładowania są usuwane nieużywane metod i zestawów.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="8bbe1-110">Wdrożenie jest tworzony w */bin/wersji / {struktury docelowej} / publish* folderu.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="8bbe1-111">Zasoby w *publikowania* folderu wdrożonych na serwerze sieci web.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="8bbe1-112">Wdrożenie może być ręcznego lub zautomatyzowanego procesu w zależności od poziomu narzędzi deweloperskich w użyciu.</span><span class="sxs-lookup"><span data-stu-id="8bbe1-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="8bbe1-113">wdrażania</span><span class="sxs-lookup"><span data-stu-id="8bbe1-113">Deployment</span></span>

<span data-ttu-id="8bbe1-114">Aby uzyskać wskazówki dotyczące wdrażania zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="8bbe1-114">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>