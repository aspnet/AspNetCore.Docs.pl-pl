---
title: 'ASP.NET Core :::no-loc(SignalR)::: obsługiwane platformy'
author: bradygaster
description: 'Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051021"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="99659-103">ASP.NET Core :::no-loc(SignalR)::: obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="99659-103">ASP.NET Core :::no-loc(SignalR)::: supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="99659-104">Wymagania systemowe serwera</span><span class="sxs-lookup"><span data-stu-id="99659-104">Server system requirements</span></span>

<span data-ttu-id="99659-105">:::no-loc(SignalR)::: w przypadku ASP.NET Core obsługuje dowolną platformę serwera, która ASP.NET Core obsługiwana przez program.</span><span class="sxs-lookup"><span data-stu-id="99659-105">:::no-loc(SignalR)::: for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="99659-106">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="99659-106">JavaScript client</span></span>

<span data-ttu-id="99659-107">[Klient JavaScript](xref:signalr/javascript-client) działa w NodeJS 8 i nowszych wersjach oraz w następujących przeglądarkach:</span><span class="sxs-lookup"><span data-stu-id="99659-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="99659-108">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="99659-108">Browser</span></span>                          | <span data-ttu-id="99659-109">Wersja</span><span class="sxs-lookup"><span data-stu-id="99659-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="99659-110">Apple Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="99659-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="99659-111">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="99659-111">Current&dagger;</span></span> |
| <span data-ttu-id="99659-112">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="99659-112">Google Chrome, including Android</span></span> | <span data-ttu-id="99659-113">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="99659-113">Current&dagger;</span></span> |
| <span data-ttu-id="99659-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="99659-114">Microsoft Edge</span></span>                   | <span data-ttu-id="99659-115">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="99659-115">Current&dagger;</span></span> |
| <span data-ttu-id="99659-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="99659-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="99659-117">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="99659-117">Current&dagger;</span></span> |

<span data-ttu-id="99659-118">&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="99659-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="99659-119">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="99659-119">.NET client</span></span>

<span data-ttu-id="99659-120">[Klient platformy .NET](xref:signalr/dotnet-client) działa na dowolnej platformie obsługiwanej przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99659-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="99659-121">Na przykład [Deweloperzy platformy Xamarin mogą używać :::no-loc(SignalR)::: ](https://github.com/aspnet/Announcements/issues/305) do tworzenia aplikacji dla systemu Android za pomocą platformy Xamarin. Android 8.4.0.1 i nowszych oraz aplikacji systemu iOS przy użyciu platformy Xamarin. iOS 11.14.0.4 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="99659-121">For example, [Xamarin developers can use :::no-loc(SignalR):::](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="99659-122">Jeśli na serwerze są uruchomione usługi IIS, transport gniazd internetowych wymaga usług IIS 8,0 lub nowszych w systemie Windows Server 2012 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="99659-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="99659-123">Inne transporty są obsługiwane na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="99659-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="99659-124">Klient Java</span><span class="sxs-lookup"><span data-stu-id="99659-124">Java client</span></span>

<span data-ttu-id="99659-125">[Klient Java](xref:signalr/java-client) obsługuje język Java 8 i nowsze wersje.</span><span class="sxs-lookup"><span data-stu-id="99659-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="99659-126">Nieobsługiwane klienci</span><span class="sxs-lookup"><span data-stu-id="99659-126">Unsupported clients</span></span>

<span data-ttu-id="99659-127">Następujący klienci są dostępni, ale są eksperymentalni lub nieoficjalni.</span><span class="sxs-lookup"><span data-stu-id="99659-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="99659-128">Nie są one obecnie obsługiwane i mogą nie być dostępne.</span><span class="sxs-lookup"><span data-stu-id="99659-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="99659-129">[Klient C++](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="99659-129">[C++ client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span></span>

* <span data-ttu-id="99659-130">[Klient SWIFT](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="99659-130">[Swift client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span></span>
