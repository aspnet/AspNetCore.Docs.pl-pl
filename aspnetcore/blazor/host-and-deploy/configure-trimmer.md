---
title: Skonfiguruj element dostosowujący dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055805"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Skonfiguruj element dostosowujący dla ASP.NET Core Blazor

Autor [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly wykonuje przycinanie [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby zmniejszyć rozmiar opublikowanych danych wyjściowych.

Przycinanie aplikacji jest zoptymalizowane pod kątem rozmiaru, ale mogą one mieć szkodliwe skutki. Aplikacje korzystające z odbicia lub powiązane funkcje dynamiczne mogą być przerywane po przycięciu, ponieważ element dostosowujący nie wie o zachowaniu dynamicznego i nie może w ogóle określić, które typy są wymagane do odbicia w czasie wykonywania. Aby przyciąć takie aplikacje, należy uzyskać informacje o wszelkich typach wymaganych przez odbicie w kodzie oraz w pakietach lub strukturach, od których zależy aplikacja.

Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, ważne jest, aby regularnie testować publikowane dane wyjściowe.

Przycinanie aplikacji .NET można wyłączyć, ustawiając właściwość programu `PublishTrimmed` MSBuild na wartość `false` w pliku projektu aplikacji:

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
Dodatkowe opcje konfigurowania elementu dostosowującego można znaleźć w [opcjach przycinania](/dotnet/core/deploying/trimming-options).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przycinanie samodzielnych wdrożeń i plików wykonywalnych](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
