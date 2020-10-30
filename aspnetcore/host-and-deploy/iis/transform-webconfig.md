---
title: Przekształcanie pliku web.config
author: rick-anderson
description: Dowiedz się, jak przekształcić plik web.config podczas publikowania aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 259b5bf9bf2a6de987494b5771897355e3ea67db
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057316"
---
# <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Autor [Vijay Ramakrishnan](https://github.com/vijayrkn)

Przekształcenia do pliku *web.config* mogą być stosowane automatycznie, gdy aplikacja zostanie opublikowana w oparciu o:

* [Konfiguracja kompilacji](#build-configuration)
* [Profil](#profile)
* [Środowisko](#environment)
* [Niestandardowe](#custom)

Te przekształcenia występują dla jednego z następujących scenariuszy generacji *web.config* :

* Generowane automatycznie przez `Microsoft.NET.Sdk.Web` zestaw SDK.
* Udostępnione przez dewelopera w [katalogu głównym zawartości](xref:fundamentals/index#content-root) aplikacji.

## <a name="build-configuration"></a>Konfiguracja kompilacji

Przekształcenia konfiguracji kompilacji są uruchamiane jako pierwsze.

Uwzględnij *Sieć Web. { Konfiguracja}* plik konfiguracyjny dla każdej [konfiguracji kompilacji (Debuguj | Wydanie)](/dotnet/core/tools/dotnet-publish#options) wymagające przekształcenia *web.config* .

W poniższym przykładzie zmienna środowiskowa specyficzna dla konfiguracji jest ustawiana w *web.Release.config* :

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Przekształcenie jest stosowane, gdy konfiguracja jest ustawiona na *Release* :

```dotnetcli
dotnet publish --configuration Release
```

Właściwość programu MSBuild dla konfiguracji ma wartość `$(Configuration)` .

## <a name="profile"></a>Profil

Przekształcenia profilu są uruchamiane po drugiej, po przeprowadzeniu [konfiguracji kompilacji](#build-configuration) .

Uwzględnij *Sieć Web. { PROFIL}. config* dla każdej konfiguracji profilu wymagającej przekształcenia *web.config* .

W poniższym przykładzie zmienna środowiskowa specyficzna dla profilu jest ustawiana w *web.FolderProfile.config* dla profilu publikowania folderu:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Przekształcenie jest stosowane, gdy profil jest *FolderProfile* :

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Właściwość programu MSBuild dla nazwy profilu to `$(PublishProfile)` .

Jeśli profil nie zostanie przekazana, domyślną nazwą profilu jest **system plików** , a *web.FileSystem.config* zostanie zastosowana, jeśli plik znajduje się w katalogu głównym zawartości aplikacji.

## <a name="environment"></a>Środowisko

Przekształcenia środowiska są uruchamiane trzecią po zakończeniu [konfiguracji kompilacji](#build-configuration) i przekształceń [profilu](#profile) .

Uwzględnij *Sieć Web. { ŚRODOWISKO} plik konfiguracyjny* dla każdego [środowiska](xref:fundamentals/environments) wymagającego transformacji *web.config* .

W poniższym przykładzie zmienna środowiskowa specyficzna dla środowiska jest ustawiana w *web.Production.config* dla środowiska produkcyjnego:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Transformacja jest stosowana, gdy środowisko jest *produkcyjne* :

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Właściwość programu MSBuild dla środowiska to `$(EnvironmentName)` .

Przy publikowaniu z programu Visual Studio i przy użyciu profilu publikowania, zobacz <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment> .

`ASPNETCORE_ENVIRONMENT`Zmienna środowiskowa jest automatycznie dodawana do pliku *web.config* po określeniu nazwy środowiska.

## <a name="custom"></a>Niestandardowy

Niestandardowe przekształcenia są uruchamiane jako ostatnie, po przeprowadzeniu [konfiguracji kompilacji](#build-configuration), [profilu](#profile)i [środowiska](#environment) .

Uwzględnij plik *{CUSTOM_NAME}. Transform* dla każdej konfiguracji niestandardowej wymagającej przekształcenia *web.config* .

W poniższym przykładzie zmienna środowiskowa transformacji niestandardowej jest ustawiana w *Custom. Transform* :

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Transformacja jest stosowana, gdy `CustomTransformFileName` Właściwość jest przenoszona do [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia:

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Właściwość programu MSBuild dla nazwy profilu to `$(CustomTransformFileName)` .

## <a name="prevent-webconfig-transformation"></a>Zapobiegaj transformacji web.config

Aby zapobiec przekształceń pliku *web.config* , ustaw właściwość MSBuild `$(IsWebConfigTransformDisabled)` :

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [ Składnia transformacjiWeb.config dla wdrożenia projektu aplikacji sieci Web](/previous-versions/dd465326(v=vs.100))
* [Web.config składni transformacji dla wdrożenia projektu sieci Web przy użyciu programu Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
