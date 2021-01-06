---
title: Wywoływanie usług gRPC przy użyciu klienta platformy .NET
author: jamesnk
description: Dowiedz się, jak wywoływać usługi gRPC Services za pomocą programu .NET gRPC Client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/18/2020
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
uid: grpc/client
ms.openlocfilehash: 39f9b3fde19e31ca970668552e5829308705f513
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97699134"
---
# <a name="call-grpc-services-with-the-net-client"></a>Wywoływanie usług gRPC przy użyciu klienta platformy .NET

Biblioteka klienta .NET gRPC jest dostępna w pakiecie NuGet [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) . W tym dokumencie wyjaśniono, jak:

* Skonfiguruj klienta gRPC do wywoływania usług gRPC.
* GRPC wywołania jednoargumentowe, przesyłania strumieniowego serwera, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.

## <a name="configure-grpc-client"></a>Konfigurowanie klienta gRPC

gRPC klienci są konkretnymi typami klientów, które są [generowane z plików *\* . proto*](xref:grpc/basics#generated-c-assets). Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku *\* . proto* . Na przykład wywołana usługa `Greeter` generuje `GreeterClient` Typ z metodami wywołania usługi.

Klient gRPC jest tworzony na podstawie kanału. Uruchom `GrpcChannel.ForAddress` polecenie, aby utworzyć kanał, a następnie użyj kanału, aby utworzyć klienta gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanał reprezentuje długotrwałe połączenie z usługą gRPC. Po utworzeniu kanału zostaje on skonfigurowany z opcjami związanymi z wywoływaniem usługi. Na przykład `HttpClient` używane do wykonywania wywołań, maksymalnego rozmiaru wysyłania i odbierania wiadomości oraz rejestrowania można określić `GrpcChannelOptions` i użyć z `GrpcChannel.ForAddress` . Aby uzyskać pełną listę opcji, zobacz [Opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>Konfigurowanie protokołu TLS

Klient gRPC musi używać tego samego zabezpieczenia na poziomie połączenia co nazwana usługa. gRPC Client Transport Layer Security (TLS) jest konfigurowany podczas tworzenia kanału gRPC. Klient gRPC zgłasza błąd podczas wywoływania usługi, a zabezpieczenia na poziomie połączenia kanału i usługi nie są zgodne.

Aby skonfigurować kanał gRPC do korzystania z protokołu TLS, upewnij się, że adres serwera zaczyna się od `https` . Na przykład `GrpcChannel.ForAddress("https://localhost:5001")` używa protokołu HTTPS. Kanał gRPC automatycznie negocjuje połączenie zabezpieczone przez protokół TLS i używa bezpiecznego połączenia do wykonywania wywołań gRPC.

> [!TIP]
> gRPC obsługuje uwierzytelnianie certyfikatu klienta za pośrednictwem protokołu TLS. Informacje dotyczące konfigurowania certyfikatów klienta przy użyciu kanału gRPC można znaleźć w temacie <xref:grpc/authn-and-authz#client-certificate-authentication> .

Aby wywoływać niezabezpieczone usługi gRPC, upewnij się, że adres serwera zaczyna się od `http` . Na przykład `GrpcChannel.ForAddress("http://localhost:5000")` używa protokołu HTTP. W przypadku platformy .NET Core 3,1 lub nowszej dodatkowa konfiguracja jest wymagana do [wywołania niezabezpieczonych usług gRPC z klientem platformy .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

### <a name="client-performance"></a>Wydajność klienta

Wydajność i użycie kanału i klienta:

* Tworzenie kanału może być kosztowną operacją. Użycie kanału dla wywołań gRPC zapewnia korzyści wynikające z wydajności.
* gRPC klienci są tworzone za pomocą kanałów. gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.
* Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.
* Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.
* Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.

`GrpcChannel.ForAddress` nie jest jedyną opcją tworzenia klienta gRPC. W przypadku wywoływania usług gRPC z poziomu aplikacji ASP.NET Core należy wziąć pod uwagę [integrację klienta gRPC](xref:grpc/clientfactory). Integracja gRPC z programem `HttpClientFactory` oferuje scentralizowaną alternatywę do tworzenia klientów gRPC.

> [!NOTE]
> Wywoływanie gRPC za pośrednictwem protokołu HTTP/2 z `Grpc.Net.Client` nie jest obecnie obsługiwane w oprogramowaniu Xamarin. Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin. [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) i [GRPC-Web](xref:grpc/browser) to żywotne alternatywy, które działają dzisiaj.

## <a name="make-grpc-calls"></a>Utwórz wywołania gRPC

Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie. Klient gRPC będzie obsługiwał serializację komunikatów i odnoszący się do wywołania gRPC do prawidłowej usługi.

gRPC ma różne typy metod. Sposób, w jaki klient jest używany do wykonywania wywołania gRPC, zależy od typu metody o nazwie. Typy metod gRPC są następujące:

* Jednoargumentowy
* Przesyłanie strumieniowe serwera
* Przesyłanie strumieniowe klienta
* Dwukierunkowe przesyłanie strumieniowe

### <a name="unary-call"></a>Wywołanie jednoargumentowe

Wywołanie jednoargumentowe rozpoczyna się od klienta wysyłającego komunikat żądania. Komunikat odpowiedzi jest zwracany po zakończeniu działania usługi.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Każda Jednoargumentowa metoda usługi w pliku *\* . proto* powoduje dwie metody .NET na konkretnym typie klienta gRPC do wywoływania metody: Metoda asynchroniczna i Metoda blokująca. Na przykład istnieją `GreeterClient` dwa sposoby wywoływania `SayHello` :

* `GreeterClient.SayHelloAsync` -wywołuje `Greeter.SayHello` usługę asynchronicznie. Można oczekiwać.
* `GreeterClient.SayHello` -wywołuje `Greeter.SayHello` usługę i bloki do momentu ukończenia. Nie używaj w kodzie asynchronicznym.

### <a name="server-streaming-call"></a>Wywołanie przesyłania strumieniowego serwera

Wywołanie przesyłania strumieniowego serwera rozpoczyna się od klienta wysyłającego komunikat żądania. `ResponseStream.MoveNext()` odczytuje komunikaty przesyłane strumieniowo z usługi. Wywołanie przesyłania strumieniowego serwera jest kompletne, gdy `ResponseStream.MoveNext()` zwraca `false` .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

W przypadku korzystania z języka C# 8 lub nowszego `await foreach` składnia może być używana do odczytywania wiadomości. `IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozszerzająca odczytuje wszystkie komunikaty ze strumienia odpowiedzi:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Wywołanie przesyłania strumieniowego klienta

Wywołanie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania komunikatu przez klienta. Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` . Gdy klient zakończył wysyłanie komunikatów, `RequestStream.CompleteAsync()` powinien zostać wywołany w celu powiadomienia usługi. Wywołanie jest zakończone, gdy usługa zwróci komunikat odpowiedzi.

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a>Dwukierunkowe wywołanie przesyłania strumieniowego

Dwukierunkowe wywołanie przesyłania strumieniowego rozpoczyna się *bez* wysyłania komunikatu przez klienta. Klient może wysyłać wiadomości za pomocą polecenia `RequestStream.WriteAsync` . Komunikaty przesyłane strumieniowo z usługi są dostępne z `ResponseStream.MoveNext()` lub `ResponseStream.ReadAllAsync()` . Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy `ResponseStream` nie ma więcej komunikatów.

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

Aby uzyskać najlepszą wydajność i uniknąć niepotrzebnych błędów w kliencie i usłudze, spróbuj bezpiecznie wykonać wywołania przesyłania strumieniowego dwukierunkowe. Wywołanie dwukierunkowe kończy się bezpiecznie, gdy serwer zakończył odczytywanie strumienia żądań, a klient zakończył odczytywanie strumienia odpowiedzi. Poprzednie przykładowe wywołanie to jeden przykład dwukierunkowego wywołania, które się zakończyło. W wywołaniu klient:

1. Uruchamia nowe dwukierunkowe wywołanie przesyłania strumieniowego przez wywołanie `EchoClient.Echo` .
2. Tworzy zadanie w tle do odczytywania wiadomości z usługi przy użyciu programu `ResponseStream.ReadAllAsync()` .
3. Wysyła komunikaty do serwera przy użyciu programu `RequestStream.WriteAsync` .
4. Powiadamia serwer, który zakończył wysyłanie komunikatów z programu `RequestStream.CompleteAsync()` .
5. Czeka, aż zadanie w tle odczytaje wszystkie wiadomości przychodzące.

W trakcie dwukierunkowego wywołania przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie. Najlepsza logika klienta do współpracy z dwukierunkowym wywołaniem różni się w zależności od logiki usługi.

## <a name="access-grpc-headers"></a>Dostęp do nagłówków gRPC

gRPC wywołania zwracają nagłówki odpowiedzi. Nagłówki odpowiedzi HTTP przechodzą metadane nazwy/wartości dotyczące wywołania, które nie odnosi się do zwracanego komunikatu.

Nagłówki są dostępne przy użyciu `ResponseHeadersAsync` , która zwraca kolekcję metadanych. Nagłówki są zwykle zwracane z komunikatem odpowiedzi; w związku z tym należy je oczekiwać.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

`ResponseHeadersAsync` wykorzystywani

* Musi oczekiwać na wynik `ResponseHeadersAsync` pobrania kolekcji nagłówków.
* Nie musi być dostępny przed `ResponseAsync` (ani strumieniem odpowiedzi podczas przesyłania strumieniowego). Jeśli odpowiedź została zwrócona, `ResponseHeadersAsync` Funkcja zwraca nagłówki natychmiast.
* Zgłosi wyjątek, jeśli wystąpił błąd połączenia lub serwera, a nagłówki nie zostały zwrócone dla wywołania gRPC.

## <a name="access-grpc-trailers"></a>Dostęp do przyczep gRPC

wywołania gRPC mogą zwracać przyczepy z odpowiedzią. Przyczepy są używane do dostarczania metadanych nazw/wartości dotyczących wywołania. Przyczepy zapewniają podobną funkcjonalność do nagłówków HTTP, ale są odbierane na końcu wywołania.

Przyczepy są dostępne przy użyciu `GetTrailers()` , która zwraca kolekcję metadanych. Po zakończeniu odpowiedzi są zwracane przyczepy. W związku z tym przed uzyskaniem dostępu do przyczep należy oczekiwać wszystkich komunikatów odpowiedzi.

Wywołania jednoargumentowe i wywołanie przesyłania strumieniowego klienta muszą oczekiwać `ResponseAsync` przed wywołaniem `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Wywołania przesyłania strumieniowego serwera i dwukierunkowe muszą kończyć się oczekiwaniem przed wywołaniem strumienia odpowiedzi `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Przyczepy są również dostępne z `RpcException` . Usługa może zwracać przyczepy ze stanem gRPC innym niż OK. W takiej sytuacji przyczepy są pobierane z wyjątku zgłoszonego przez klienta gRPC:

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a>Konfigurowanie terminu ostatecznego

Zaleca się skonfigurowanie ostatecznego terminu wywołania gRPC, ponieważ zawiera on górny limit czasu, w którym można uruchomić wywołanie. Usługa błędna uniemożliwia uruchamianie nieograniczonego i wyczerpania zasobów serwera. Terminy są przydatnym narzędziem do tworzenia niezawodnych aplikacji.

Skonfiguruj, `CallOptions.Deadline` Aby ustawić ostateczny termin wywołania gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Aby uzyskać więcej informacji, zobacz <xref:grpc/deadlines-cancellation#deadlines>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
