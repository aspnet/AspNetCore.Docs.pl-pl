---
title: Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS
author: rick-anderson
description: Dowiedz się, jak hostować ASP.NET Core obrazy za pomocą platformy Docker za pośrednictwem protokołu HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: security/docker-https
ms.openlocfilehash: f17a3abe1b00b39b7b6787be5b20ce65771190b8
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619712"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="1fff3-103">Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="1fff3-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="1fff3-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1fff3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1fff3-105">ASP.NET Core [domyślnie używa protokołu HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1fff3-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="1fff3-106">[Protokół https](https://en.wikipedia.org/wiki/HTTPS) opiera się [](https://en.wikipedia.org/wiki/Public_key_certificate) na certyfikatach do zaufania, tożsamości i szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="1fff3-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="1fff3-107">W tym dokumencie wyjaśniono, jak uruchamiać wstępnie skompilowane obrazy kontenerów przy użyciu protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1fff3-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="1fff3-108">Zobacz [Tworzenie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="1fff3-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="1fff3-109">Ten przykład wymaga [platformy docker 17,06](https://docs.docker.com/release-notes/docker-ce) lub nowszej wersji [klienta platformy Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="1fff3-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1fff3-110">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="1fff3-110">Prerequisites</span></span>

<span data-ttu-id="1fff3-111">W przypadku niektórych instrukcji przedstawionych w tym dokumencie wymagany jest [zestaw .NET Core 2,2 SDK](https://www.microsoft.com/net/download) lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1fff3-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="1fff3-112">Certyfikaty</span><span class="sxs-lookup"><span data-stu-id="1fff3-112">Certificates</span></span>

<span data-ttu-id="1fff3-113">Certyfikat z [urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority) jest wymagany do hostingu w [środowisku produkcyjnym](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny.</span><span class="sxs-lookup"><span data-stu-id="1fff3-113">A certificate from a [certificate authority](https://en.wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span>  <span data-ttu-id="1fff3-114">[Zaszyfrujmy](https://letsencrypt.org/) to urząd certyfikacji, który oferuje bezpłatne certyfikaty.</span><span class="sxs-lookup"><span data-stu-id="1fff3-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="1fff3-115">W tym dokumencie są stosowane [Certyfikaty deweloperskie](https://en.wikipedia.org/wiki/Self-signed_certificate) z podpisem własnym do obsługi wstępnie `localhost`utworzonych obrazów.</span><span class="sxs-lookup"><span data-stu-id="1fff3-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="1fff3-116">Instrukcje są podobne do korzystania z certyfikatów produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="1fff3-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="1fff3-117">Dla certyfikatów produkcyjnych:</span><span class="sxs-lookup"><span data-stu-id="1fff3-117">For production certs:</span></span>

* <span data-ttu-id="1fff3-118">`dotnet dev-certs` Narzędzie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="1fff3-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="1fff3-119">Certyfikaty nie muszą być przechowywane w lokalizacji używanej w instrukcjach.</span><span class="sxs-lookup"><span data-stu-id="1fff3-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="1fff3-120">Każda lokalizacja powinna funkcjonować, chociaż przechowywanie certyfikatów w katalogu witryn nie jest zalecane.</span><span class="sxs-lookup"><span data-stu-id="1fff3-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="1fff3-121">Instrukcje instalują certyfikaty w kontenerach.</span><span class="sxs-lookup"><span data-stu-id="1fff3-121">The instructions volume mount certificates into containers.</span></span> <span data-ttu-id="1fff3-122">Możesz dodać certyfikaty do obrazów kontenerów za pomocą `COPY` polecenia w pliku dockerfile.</span><span class="sxs-lookup"><span data-stu-id="1fff3-122">You can add certificates into container images with a `COPY` command in a Dockerfile.</span></span> <span data-ttu-id="1fff3-123">Nie zaleca się kopiowania certyfikatów do obrazu:</span><span class="sxs-lookup"><span data-stu-id="1fff3-123">Copying certificates into an image is not recommended:</span></span>

* <span data-ttu-id="1fff3-124">Trudno jest użyć tego samego obrazu do testowania przy użyciu certyfikatów deweloperskich.</span><span class="sxs-lookup"><span data-stu-id="1fff3-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="1fff3-125">Trudno jest użyć tego samego obrazu do hostowania z certyfikatami produkcyjnymi.</span><span class="sxs-lookup"><span data-stu-id="1fff3-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="1fff3-126">Istnieje duże ryzyko ujawnienia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1fff3-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="1fff3-127">Uruchamianie wstępnie skompilowanych obrazów kontenerów za pomocą protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="1fff3-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="1fff3-128">Wykonaj następujące instrukcje dotyczące konfiguracji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="1fff3-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="1fff3-129">System Windows korzystający z kontenerów systemu Linux</span><span class="sxs-lookup"><span data-stu-id="1fff3-129">Windows using Linux containers</span></span>

<span data-ttu-id="1fff3-130">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="1fff3-130">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="1fff3-131">W poprzednich poleceniach Zastąp `{ password here }` ciąg hasłem.</span><span class="sxs-lookup"><span data-stu-id="1fff3-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="1fff3-132">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="1fff3-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="1fff3-133">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1fff3-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="1fff3-134">macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="1fff3-134">macOS or Linux</span></span>

<span data-ttu-id="1fff3-135">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="1fff3-135">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="1fff3-136">`dotnet dev-certs https --trust`jest obsługiwana tylko w systemach macOS i Windows.</span><span class="sxs-lookup"><span data-stu-id="1fff3-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="1fff3-137">Certyfikaty w systemie Linux muszą być zaufane w sposób, który jest obsługiwany przez dystrybucji.</span><span class="sxs-lookup"><span data-stu-id="1fff3-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="1fff3-138">Prawdopodobnie należy zaufać certyfikatowi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="1fff3-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="1fff3-139">W poprzednich poleceniach Zastąp `{ password here }` ciąg hasłem.</span><span class="sxs-lookup"><span data-stu-id="1fff3-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="1fff3-140">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="1fff3-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="1fff3-141">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1fff3-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="1fff3-142">Windows przy użyciu kontenerów systemu Windows</span><span class="sxs-lookup"><span data-stu-id="1fff3-142">Windows using Windows containers</span></span>

<span data-ttu-id="1fff3-143">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="1fff3-143">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="1fff3-144">W poprzednich poleceniach Zastąp `{ password here }` ciąg hasłem.</span><span class="sxs-lookup"><span data-stu-id="1fff3-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="1fff3-145">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="1fff3-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="1fff3-146">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1fff3-146">The password must match the password used for the certificate.</span></span>