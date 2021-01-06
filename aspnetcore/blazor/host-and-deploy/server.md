---
title: Hostowanie i wdrażanie ASP.NET Core Blazor Server
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor Server aplikację przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "94595444"
---
# <a name="host-and-deploy-no-locblazor-server"></a>Hostowanie i wdrażanie Blazor Server

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[ Blazor Server aplikacje](xref:blazor/hosting-models#blazor-server) mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Wdrażanie

Przy użyciu [ Blazor Server modelu hostingu](xref:blazor/hosting-models#blazor-server)program Blazor jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.

Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core. Program Visual Studio zawiera szablon projektu **Blazor Server aplikacji** ( `blazorserverside` szablon podczas korzystania z [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia).

## <a name="scalability"></a>Skalowalność

Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla Blazor Server aplikacji. Aby rozwiązać skalowalność aplikacji, zobacz następujące zasoby Blazor Server :

* [Podstawowe Blazor Server aplikacje](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Serwer wdrażania

Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika. Dostępna pamięć na serwerze ma wpływ na:

* Liczba aktywnych obwodów obsługiwanych przez serwer.
* Opóźnienie interfejsu użytkownika na kliencie.

Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i skalowalnych Blazor aplikacji serwerowych, zobacz <xref:blazor/security/server/threat-mitigation> .

Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*. Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami. Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 współbieżnych użytkowników, należy rozważyć budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).

### <a name="no-locsignalr-configuration"></a>SignalR skonfigurować

Blazor Server aplikacje używają ASP.NET Core SignalR do komunikowania się z przeglądarką. [ SignalR warunki hostingu i skalowania](xref:signalr/publish-to-azure-web-app) mają zastosowanie do Blazor Server aplikacji.

Blazor najlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako SignalR transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security). Długie sondowanie jest używane przez, SignalR gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długotrwałego sondowania. Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi. Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-no-locsignalr-service"></a>Usługa platformy Azure SignalR

Zalecamy korzystanie z [ SignalR usługi platformy Azure](xref:signalr/scale#azure-signalr-service) dla Blazor Server aplikacji. Usługa umożliwia skalowanie Blazor Server aplikacji w górę do dużej liczby jednoczesnych SignalR połączeń. Ponadto SignalR globalne zasięgi i wysokiej wydajności centra danych usługi znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.

> [!IMPORTANT]
> Gdy usługi [WebSockets](https://wikipedia.org/wiki/WebSocket) są wyłączone, Azure App Service symuluje połączenie w czasie rzeczywistym przy użyciu długich sondowania protokołu HTTP. Długotrwałe sondowanie HTTP jest znacznie wolniejsze niż uruchamianie z włączonymi składnikami WebSockets, które nie używają sondowania w celu symulowania połączenia klienta z serwerem.
>
> Zalecamy używanie funkcji WebSockets dla Blazor Server aplikacji wdrożonych w programie Azure App Service. [ SignalR Usługa platformy Azure](xref:signalr/scale#azure-signalr-service) domyślnie używa obiektów WebSockets. Jeśli aplikacja nie korzysta z usługi platformy Azure SignalR , zobacz <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .
>
> Aby uzyskać więcej informacji, zobacz:
>
> * [Co to jest SignalR usługa Azure?](/azure/azure-signalr/signalr-overview)
> * [Przewodnik dotyczący wydajności SignalR usługi platformy Azure](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a>Konfigurowanie

Aby skonfigurować aplikację dla usługi platformy Azure SignalR , aplikacja musi obsługiwać sesje programu *Sticky Notes*, w przypadku których klienci są [przekierowywani z powrotem do tego samego serwera podczas renderowania](xref:blazor/hosting-models#connection-to-the-server). `ServerStickyMode`Wartość opcji lub konfiguracji jest ustawiona na `Required` . Zazwyczaj aplikacja tworzy konfigurację przy użyciu **_jednej_** z następujących metod:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfiguracja (Użyj **_jednej_** z następujących metod):
  
     * W pliku `appsettings.json`:

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     *   >  **Ustawienia aplikacji** konfiguracji usługi App Service w Azure Portal (**Nazwa**: `Azure__SignalR__StickyServerMode` , **wartość**: `Required` ). Takie podejście jest stosowane automatycznie w przypadku aprowizacji [ SignalR usługi platformy Azure](#provision-the-azure-signalr-service).

### <a name="provision-the-azure-no-locsignalr-service"></a>Inicjowanie obsługi administracyjnej SignalR usługi platformy Azure

Aby zainicjować obsługę administracyjną usługi platformy Azure SignalR dla aplikacji w programie Visual Studio:

1. Utwórz profil publikowania aplikacji platformy Azure w programie Visual Studio dla Blazor Server aplikacji.
1. Dodaj zależność **SignalR usługi platformy Azure** do profilu. Jeśli subskrypcja platformy Azure nie ma istniejącego wystąpienia usługi platformy Azure SignalR , które ma zostać przypisane do aplikacji, wybierz opcję **Utwórz nowe SignalR wystąpienie usługi platformy Azure** , aby udostępnić nowe wystąpienie usługi.
1. Publikowanie aplikacji na platformie Azure

Inicjowanie obsługi administracyjnej usługi platformy Azure SignalR w programie Visual Studio powoduje automatyczne [włączenie *sesji programu Sticky Notes*](#configuration) i dodanie SignalR parametrów połączenia do konfiguracji usługi App Service.

#### <a name="iis"></a>IIS

W przypadku korzystania z usług IIS Włącz:

* Obiekty [WebSockets w usługach IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sesje programu Sticky w ramach routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Utwórz definicję transferu danych przychodzących z następującymi [adnotacjami Kubernetes dla sesji programu Sticky Notes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>System Linux z serwerem Nginx

Aby SignalR elementy WebSockets działały prawidłowo, upewnij się, że na serwerze proxy `Upgrade` i w `Connection` nagłówkach zostały ustawione następujące wartości, które `$connection_upgrade` są mapowane na:

* Domyślnie wartość nagłówka uaktualnienia.
* `close` gdy brakuje nagłówka uaktualnienia lub jest on pusty.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [NGINX jako serwer proxy protokołu WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Serwer proxy protokołu WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>System Linux z serwerem Apache

Aby hostować Blazor aplikację za Apache w systemie Linux, skonfiguruj `ProxyPass` dla ruchu HTTP i WebSockets.

W poniższym przykładzie:

* Serwer Kestrel jest uruchomiony na komputerze-hoście.
* Aplikacja nasłuchuje ruchu na porcie 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Włącz następujące moduły:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Sprawdź, czy w konsoli przeglądarki występują błędy usługi WebSockets. Przykładowe błędy:

* Firefox nie może nawiązać połączenia z serwerem w ws://the-domain-name.tld/_blazor?id=XXX.
* Błąd: nie można uruchomić transportu "WebSockets": błąd: Wystąpił błąd podczas transportu.
* Błąd: nie można uruchomić transportu "LongPolling": TypeError: this. transport jest niezdefiniowany
* Błąd: nie można nawiązać połączenia z serwerem za pomocą dowolnych dostępnych transportów. Niepowodzenie obiektów WebSockets
* Błąd: nie można wysłać danych, jeśli połączenie nie jest w stanie "podłączone".

Aby uzyskać więcej informacji, zobacz [dokumentację platformy Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Mierzenie opóźnienia sieci

Przy użyciu kodu [js Interop](xref:blazor/call-javascript-from-dotnet) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.
