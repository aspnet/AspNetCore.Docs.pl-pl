---
title: Obsługa Ogólne rozporządzenie o ochronie danych (Rodo) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak uzyskać dostęp do punktów rozszerzenia Rodo w aplikacji sieci Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051008"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Obsługa Ogólne rozporządzenie o ochronie danych UE (Rodo) w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core udostępnia interfejsy API i szablony, które pomagają spełnić niektóre wymagania dotyczące [ogólne rozporządzenie o ochronie danych UE (Rodo)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* Szablony projektu obejmują punkty rozszerzenia i użyto metod zastępczych znaczników, które można zamienić na zasady zachowania poufności i cookie używania zasad.
* Widok Pages */privacy. cshtml* lub *widoki/Home/privacy. cshtml* zawiera stronę zawierającą szczegółowe informacje o zasadach zachowania poufności informacji.

Aby włączyć domyślną cookie funkcję wyrażania zgody, która została znaleziona w szablonach ASP.NET Core 2,2 w aplikacji ASP.NET Core 3,0, wygenerowana przez szablon:

* Dodaj `using Microsoft.AspNetCore.Http` do listy dyrektyw using.
* Dodawanie [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) do `Startup.ConfigureServices` [ Cookie zasad i używanie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ich w celu `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Dodaj cookie częściowo zgodę do pliku *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Dodaj plik *\_ Cookie ConsentPartial. cshtml* do projektu:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Wybierz ASP.NET Core wersję 2,2 tego artykułu, aby przeczytać o cookie funkcji wyrażania zgody.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Szablony projektu obejmują punkty rozszerzenia i użyto metod zastępczych znaczników, które można zamienić na zasady zachowania poufności i cookie używania zasad.
* cookieFunkcja wyrażania zgody umożliwia poproszenie użytkowników o zgodę na przechowywanie informacji osobistych (i śledzenie ich). Jeśli użytkownik nie wyraził zgody na zbieranie danych, a aplikacja ma [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) ustawiony na `true` , nieistotne cookie s nie są wysyłane do przeglądarki.
* Cookies może być oznaczony jako istotny. Podstawowe cookie elementy s są wysyłane do przeglądarki nawet wtedy, gdy użytkownik nie wyraził zgody i śledzenie jest wyłączone.
* [TempData i Session cookie s](#tempdata) nie działają, gdy śledzenie jest wyłączone.
* Strona [ Identity Zarządzanie](#pd) zawiera link umożliwiający pobranie i usunięcie danych użytkownika.

[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) umożliwia przetestowanie większości punktów rozszerzenia Rodo i interfejsów API dodanych do szablonów ASP.NET Core 2,1. Instrukcje dotyczące testowania można znaleźć w pliku [README](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core obsługa Rodo w kodzie wygenerowanym przez szablon

Razor Strony i projekty MVC utworzone przy użyciu szablonów projektu obejmują następujące wsparcie Rodo:

* [ Cookie Zasady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) i use są ustawiane w `Startup` klasie.
* [Widok częściowy](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_ Cookie ConsentPartial. cshtml* . W tym pliku znajduje się przycisk **Akceptuj** . Gdy użytkownik kliknie przycisk **Akceptuj** , wyrażasz zgodę na przechowywanie cookie s.
* Widok Pages */privacy. cshtml* lub *widoki/Home/privacy. cshtml* zawiera stronę zawierającą szczegółowe informacje o zasadach zachowania poufności informacji. Plik *\_ Cookie ConsentPartial. cshtml* generuje link do strony prywatność.
* W przypadku aplikacji utworzonych przy użyciu poszczególnych kont użytkowników Strona Zarządzanie zawiera linki umożliwiające pobranie i usunięcie [osobistych danych użytkownika](#pd).

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookieZasady PolicyOptions i użycia Cookie

[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) są inicjowane w `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Użyj Cookie Zasady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) są wywoływane w `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieWidok częściowy ConsentPartial. cshtml

Widok częściowy *\_ Cookie ConsentPartial. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Ta część częściowa:

* Uzyskuje stan śledzenia dla użytkownika. Jeśli aplikacja jest skonfigurowana do wymagania zgody, użytkownik musi wyrazić zgodę przed rozpoczęciem cookie śledzenia. Jeśli jest wymagana zgoda, cookie panel zgody jest ustalany w górnej części paska nawigacyjnego utworzonego przez plik *\_ Layout. cshtml* .
* Udostępnia element HTML `<p>` do podsumowywania prywatności i cookie zasad użytkowania.
* Zawiera link do strony lub widoku prywatności, w którym można szczegółowo zapoznać się z zasadami zachowania poufności informacji w witrynie.

## <a name="essential-no-loccookies"></a>Podstawowe cookie elementy s

Jeśli cookie nie podano zgody na przechowywanie s, cookie do przeglądarki są wysyłane tylko elementy oznaczone jako podstawowe. Poniższy kod stanowi cookie zasadnicze znaczenie:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>Dostawca TempData i stan sesji cookie s nie są istotne

[Dostawca TempData](xref:fundamentals/app-state#tempdata) cookie nie jest istotny. Jeśli śledzenie jest wyłączone, dostawca TempData nie działa. Aby włączyć dostawcę TempData, gdy śledzenie jest wyłączone, Oznacz TempData cookie jako zasadniczą w `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Stan sesji](xref:fundamentals/app-state) cookie s nie są istotne. Stan sesji nie działa, gdy śledzenie jest wyłączone. Poniższy kod powoduje, że cookie :

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Dane osobowe

ASP.NET Core aplikacje utworzone przy użyciu poszczególnych kont użytkowników zawierają kod umożliwiający pobranie i usunięcie danych osobowych.

Wybierz nazwę użytkownika, a następnie wybierz pozycję **dane osobowe** :

![Strona Zarządzanie danymi osobistymi](gdpr/_static/pd.png)

Uwagi:

* Aby wygenerować `Account/Manage` kod, zobacz [szkielet Identity ](xref:security/authentication/scaffold-identity).
* Linki **usuwania** i **pobierania** działają tylko na domyślnych danych tożsamości. Aplikacje, które tworzą niestandardowe dane użytkownika, muszą zostać rozszerzone w celu usunięcia/pobrania niestandardowych danych użytkownika. Aby uzyskać więcej informacji, zobacz [Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do Identity programu ](xref:security/authentication/add-user-data).
* Zapisane tokeny dla użytkownika, które są przechowywane w Identity tabeli bazy danych, `AspNetUserTokens` są usuwane, gdy użytkownik zostanie usunięty przez kaskadowe zachowanie podczas usuwania ze względu na [klucz obcy](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Uwierzytelnianie dostawcy zewnętrznego](xref:security/authentication/social/index), takie jak Facebook i Google, nie jest dostępne przed cookie zaakceptowaniem zasad.

::: moniker-end

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Niektóre bazy danych i mechanizmy magazynu umożliwiają szyfrowanie w spoczynku. Szyfrowanie w spoczynku:

* Szyfruje przechowywane dane automatycznie.
* Szyfruje bez konfiguracji, programowania lub innej pracy dla oprogramowania, które uzyskuje dostęp do danych.
* Jest najłatwiejszym i najbezpieczniejszą opcją.
* Umożliwia bazie danych zarządzanie kluczami i szyfrowaniem.

Przykład:

* Program Microsoft SQL i usługa Azure SQL zapewniają [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [Usługa SQL Azure domyślnie szyfruje bazę danych](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Obiekty blob, pliki, tabele i queue storage platformy Azure domyślnie są szyfrowane](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

W przypadku baz danych, które nie zapewniają wbudowanego szyfrowania w spoczynku, może być możliwe użycie szyfrowania dysków w celu zapewnienia tej samej ochrony. Przykład:

* [Funkcja BitLocker dla systemu Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* W systemie Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [Rodo — Dodawanie przycisku odwołaj zgodę w ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
