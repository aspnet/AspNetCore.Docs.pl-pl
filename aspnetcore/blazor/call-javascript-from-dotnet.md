---
title: Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak wywoływać funkcje języka JavaScript z metod .NET w aplikacjach Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 7a27b6f1be2ef296d5b2b2a4f566e0cdedbe6480
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659713"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="84321-103">Wywoływanie funkcji języka JavaScript z metod .NET w ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="84321-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="84321-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)i [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="84321-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="84321-105">Aplikacja Blazor może wywoływać funkcje języka JavaScript z metod .NET i metod .NET z funkcji języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84321-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="84321-106">Te scenariusze nazywa się *współdziałaniem JavaScript* (w programie*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="84321-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="84321-107">W tym artykule opisano wywoływanie funkcji języka JavaScript z platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="84321-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="84321-108">Aby uzyskać informacje na temat wywoływania metod .NET w języku JavaScript, zobacz <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="84321-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="84321-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84321-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="84321-110">Aby wywołać kod JavaScript z platformy .NET, użyj abstrakcji `IJSRuntime`.</span><span class="sxs-lookup"><span data-stu-id="84321-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="84321-111">Aby wystawić wywołania w programie JS Interop, wstrzyknąć `IJSRuntime` abstrakcję w składniku.</span><span class="sxs-lookup"><span data-stu-id="84321-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="84321-112">Metoda `InvokeAsync<T>` przyjmuje identyfikator funkcji języka JavaScript, która ma zostać wywołana wraz z dowolną liczbą argumentów z możliwością serializacji JSON.</span><span class="sxs-lookup"><span data-stu-id="84321-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="84321-113">Identyfikator funkcji jest względny w stosunku do zakresu globalnego (`window`).</span><span class="sxs-lookup"><span data-stu-id="84321-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="84321-114">Jeśli chcesz wywołać `window.someScope.someFunction`, identyfikator jest `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="84321-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="84321-115">Nie ma potrzeby rejestrowania funkcji przed jej wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="84321-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="84321-116">Typ zwracany `T` musi również być możliwy do serializacji kod JSON.</span><span class="sxs-lookup"><span data-stu-id="84321-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="84321-117">`T` powinna być zgodna z typem .NET, który najlepiej jest mapowany do zwracanego typu JSON.</span><span class="sxs-lookup"><span data-stu-id="84321-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="84321-118">W przypadku aplikacji Blazor Server z włączoną obsługą prerenderowania Wywoływanie kodu JavaScript nie jest możliwe podczas początkowego wstępnego renderowania.</span><span class="sxs-lookup"><span data-stu-id="84321-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="84321-119">Wywołania międzyoperacyjne języka JavaScript muszą zostać odroczone do momentu ustanowienia połączenia z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="84321-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="84321-120">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja serwera Blazor jest renderowana](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="84321-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="84321-121">Poniższy przykład jest oparty na [dekoderze](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekoder języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84321-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="84321-122">W przykładzie pokazano, jak wywołać funkcję JavaScript z C# metody.</span><span class="sxs-lookup"><span data-stu-id="84321-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="84321-123">Funkcja JavaScript akceptuje tablicę bajtową z C# metody, dekoduje tablicę i zwraca tekst do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="84321-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="84321-124">Wewnątrz elementu `<head>` *wwwroot/index.html* (Blazor webassembly) lub *pages/_Host. cshtml* (Blazor Server) podaj funkcję języka JavaScript, która używa `TextDecoder` do zdekodowania przekazaną tablicę i zwrócenie zdekodowanej wartości:</span><span class="sxs-lookup"><span data-stu-id="84321-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="84321-125">Kod JavaScript, taki jak kod przedstawiony w powyższym przykładzie, można również załadować z pliku JavaScript ( *. js*) z odwołaniem do pliku skryptu:</span><span class="sxs-lookup"><span data-stu-id="84321-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="84321-126">Następujący składnik:</span><span class="sxs-lookup"><span data-stu-id="84321-126">The following component:</span></span>

* <span data-ttu-id="84321-127">Wywołuje funkcję `convertArray` JavaScript przy użyciu `JSRuntime`, gdy zostanie wybrany przycisk składnika (**Konwertuj tablicę**).</span><span class="sxs-lookup"><span data-stu-id="84321-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="84321-128">Po wywołaniu funkcji języka JavaScript przenoszona tablica jest konwertowana na ciąg.</span><span class="sxs-lookup"><span data-stu-id="84321-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="84321-129">Ciąg jest zwracany do składnika do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="84321-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="84321-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="84321-130">IJSRuntime</span></span>

<span data-ttu-id="84321-131">Aby użyć abstrakcji `IJSRuntime`, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="84321-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="84321-132">Wsuń `IJSRuntime` abstrakcję do składnika Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="84321-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="84321-133">Wewnątrz elementu `<head>` *wwwroot/index.html* (Blazor webassembly) lub *pages/_Host. cshtml* (Blazor Server) podaj funkcję JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="84321-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="84321-134">Funkcja jest wywoływana z `IJSRuntime.InvokeVoidAsync` i nie zwraca wartości:</span><span class="sxs-lookup"><span data-stu-id="84321-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="84321-135">Wstrzyknąć `IJSRuntime` abstrakcję do klasy ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="84321-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="84321-136">Wewnątrz elementu `<head>` *wwwroot/index.html* (Blazor webassembly) lub *pages/_Host. cshtml* (Blazor Server) podaj funkcję JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="84321-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="84321-137">Funkcja jest wywoływana z `JSRuntime.InvokeAsync` i zwraca wartość:</span><span class="sxs-lookup"><span data-stu-id="84321-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="84321-138">Aby można było wygenerować zawartość dynamiczną przy użyciu [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), użyj atrybutu `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="84321-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="84321-139">W aplikacji przykładowej po stronie klienta, która jest dołączona do tego tematu, dostępne są dwie funkcje języka JavaScript, które współdziałają z modelem DOM, aby odbierać dane wejściowe użytkownika i wyświetlać komunikat powitalny:</span><span class="sxs-lookup"><span data-stu-id="84321-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="84321-140">`showPrompt` &ndash; generuje monit o zaakceptowanie danych wprowadzonych przez użytkownika (nazwę użytkownika) i zwraca nazwę obiektu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="84321-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="84321-141">`displayWelcome` &ndash; przypisuje Komunikat powitalny od wywołującego do obiektu DOM z `id` `welcome`.</span><span class="sxs-lookup"><span data-stu-id="84321-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="84321-142">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="84321-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="84321-143">Umieść tag `<script>` odwołujący się do pliku JavaScript w pliku *wwwroot/index.html* (Blazor webassembly) lub *pages/_Host. cshtml* (serwerBlazor).</span><span class="sxs-lookup"><span data-stu-id="84321-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="84321-144">*wwwroot/index.html* (Blazor webassembly):</span><span class="sxs-lookup"><span data-stu-id="84321-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="84321-145">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="84321-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="84321-146">Nie umieszczaj znacznika `<script>` w pliku składnika, ponieważ nie można dynamicznie zaktualizować tagu `<script>`.</span><span class="sxs-lookup"><span data-stu-id="84321-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="84321-147">.NET metod współdziałania z funkcjami JavaScript w pliku *exampleJsInterop. js* przez wywołanie `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="84321-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="84321-148">Abstrakcja `IJSRuntime` jest asynchroniczna, aby umożliwić obsługę scenariuszy Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="84321-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="84321-149">Jeśli aplikacja jest aplikacją Blazor webassembly i chcesz wywołać funkcję JavaScript synchronicznie, downcast do `IJSInProcessRuntime` i Wywołaj `Invoke<T>` zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="84321-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="84321-150">Zalecamy, aby większość bibliotek międzyoperacyjnych JS używała asynchronicznych interfejsów API, aby upewnić się, że biblioteki są dostępne we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="84321-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="84321-151">Przykładowa aplikacja zawiera składnik demonstrujący międzyoperacyjność JS.</span><span class="sxs-lookup"><span data-stu-id="84321-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="84321-152">Składnik:</span><span class="sxs-lookup"><span data-stu-id="84321-152">The component:</span></span>

* <span data-ttu-id="84321-153">Odbiera dane wprowadzane przez użytkownika za pośrednictwem wiersza polecenia języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84321-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="84321-154">Zwraca tekst do składnika do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="84321-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="84321-155">Wywołuje drugą funkcję języka JavaScript, która współdziała z modelem DOM, aby wyświetlić komunikat powitalny.</span><span class="sxs-lookup"><span data-stu-id="84321-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="84321-156">*Strony/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="84321-156">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="84321-157">Gdy `TriggerJsPrompt` jest wykonywane, wybierając przycisk **Monituj wyzwalacza JavaScript** składnika, funkcja JavaScript `showPrompt` dostępna w pliku *wwwroot/exampleJsInterop. js* jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="84321-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="84321-158">Funkcja `showPrompt` akceptuje dane wejściowe użytkownika (nazwę użytkownika), które są kodowane w formacie HTML i zwracane do składnika.</span><span class="sxs-lookup"><span data-stu-id="84321-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="84321-159">Składnik przechowuje nazwę użytkownika w zmiennej lokalnej, `name`.</span><span class="sxs-lookup"><span data-stu-id="84321-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="84321-160">Ciąg przechowywany w `name` jest zawarty w komunikacie powitalnym, który jest przesyłany do funkcji języka JavaScript, `displayWelcome`, która renderuje Komunikat powitalny do znacznika nagłówka.</span><span class="sxs-lookup"><span data-stu-id="84321-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="84321-161">Wywoływanie funkcji języka JavaScript typu void</span><span class="sxs-lookup"><span data-stu-id="84321-161">Call a void JavaScript function</span></span>

<span data-ttu-id="84321-162">Funkcje języka JavaScript zwracające [wartość void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) lub [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) są wywoływane z `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="84321-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="84321-163">Wykrywaj, kiedy aplikacja serwera Blazor jest renderowana</span><span class="sxs-lookup"><span data-stu-id="84321-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="84321-164">Przechwyć odwołania do elementów</span><span class="sxs-lookup"><span data-stu-id="84321-164">Capture references to elements</span></span>

<span data-ttu-id="84321-165">Niektóre scenariusze międzyoperacyjności JS wymagają odwołań do elementów HTML.</span><span class="sxs-lookup"><span data-stu-id="84321-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="84321-166">Na przykład Biblioteka interfejsu użytkownika może wymagać odwołania do elementu dla inicjalizacji lub może być konieczne wywoływanie interfejsów API przypominających polecenia na elemencie, takich jak `focus` lub `play`.</span><span class="sxs-lookup"><span data-stu-id="84321-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="84321-167">Przechwyć odwołania do elementów HTML w składniku, korzystając z następującej metody:</span><span class="sxs-lookup"><span data-stu-id="84321-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="84321-168">Dodaj atrybut `@ref` do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="84321-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="84321-169">Zdefiniuj pole typu `ElementReference`, którego nazwa pasuje do wartości atrybutu `@ref`.</span><span class="sxs-lookup"><span data-stu-id="84321-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="84321-170">W poniższym przykładzie pokazano przechwytywanie odwołania do `username` elementu `<input>`:</span><span class="sxs-lookup"><span data-stu-id="84321-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="84321-171">Użyj odwołania do elementu, aby zmodyfikować zawartość pustego elementu, który nie współdziała z Blazor.</span><span class="sxs-lookup"><span data-stu-id="84321-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="84321-172">Ten scenariusz jest przydatny, gdy interfejs API innej firmy dostarcza zawartość do elementu.</span><span class="sxs-lookup"><span data-stu-id="84321-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="84321-173">Ponieważ Blazor nie współdziała z elementem, nie ma możliwości konfliktu między reprezentacją elementu a obiektem DOM przez Blazor.</span><span class="sxs-lookup"><span data-stu-id="84321-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="84321-174">W poniższym przykładzie jest *niebezpieczne* do mutacji zawartości listy nieuporządkowanej (`ul`), ponieważ Blazor współdziała z modelem dom w celu wypełnienia elementów listy elementu (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="84321-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="84321-175">Jeśli program JS Interop przyniesie zawartość elementu `MyList` i Blazor próbuje zastosować różnic do elementu, różnice nie będą zgodne z modelem DOM.</span><span class="sxs-lookup"><span data-stu-id="84321-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="84321-176">W odniesieniu do kodu platformy .NET `ElementReference` jest nieprzezroczystym uchwytem.</span><span class="sxs-lookup"><span data-stu-id="84321-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="84321-177">*Jedyną* czynnością, którą można wykonać za pomocą `ElementReference`, jest przekazanie jej do kodu JavaScript za pośrednictwem międzyoperacyjnego js.</span><span class="sxs-lookup"><span data-stu-id="84321-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="84321-178">Gdy to zrobisz, kod po stronie JavaScript odbiera wystąpienie `HTMLElement`, które może być używane z normalnymi interfejsami API modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="84321-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="84321-179">Na przykład poniższy kod definiuje metodę rozszerzenia .NET, która umożliwia ustawienie fokusu na elemencie:</span><span class="sxs-lookup"><span data-stu-id="84321-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="84321-180">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="84321-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="84321-181">Aby wywołać funkcję języka JavaScript, która nie zwraca wartości, użyj `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="84321-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="84321-182">Poniższy kod ustawia fokus na wejściu do nazwy użytkownika, wywołując poprzednią funkcję JavaScript z przechwyconą `ElementReference`:</span><span class="sxs-lookup"><span data-stu-id="84321-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="84321-183">Aby użyć metody rozszerzenia, Utwórz statyczną metodę rozszerzenia, która odbiera wystąpienie `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="84321-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="84321-184">Metoda `Focus` jest wywoływana bezpośrednio dla obiektu.</span><span class="sxs-lookup"><span data-stu-id="84321-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="84321-185">W poniższym przykładzie przyjęto założenie, że metoda `Focus` jest dostępna z przestrzeni nazw `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="84321-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="84321-186">Zmienna `username` jest wypełniana tylko po wyrenderowaniu składnika.</span><span class="sxs-lookup"><span data-stu-id="84321-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="84321-187">Jeśli niewypełniony `ElementReference` jest przekazywane do kodu JavaScript, kod JavaScript otrzymuje wartość `null`.</span><span class="sxs-lookup"><span data-stu-id="84321-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="84321-188">Aby manipulować odwołaniami do elementów po zakończeniu renderowania składnika (aby ustawić początkowy fokus w elemencie), użyj [metod cyklu życia składnika OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="84321-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="84321-189">Podczas pracy z typami ogólnymi i zwracania wartości należy użyć [ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="84321-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="84321-190">`GenericMethod` jest wywoływana bezpośrednio w obiekcie z typem.</span><span class="sxs-lookup"><span data-stu-id="84321-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="84321-191">W poniższym przykładzie przyjęto założenie, że `GenericMethod` jest dostępny z przestrzeni nazw `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="84321-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="84321-192">Elementy odniesienia między składnikami</span><span class="sxs-lookup"><span data-stu-id="84321-192">Reference elements across components</span></span>

<span data-ttu-id="84321-193">`ElementReference` jest gwarantowany tylko w metodzie `OnAfterRender` składnika (a odwołanie elementu jest `struct`), dlatego nie można przekazywać odwołania do elementu między składnikami.</span><span class="sxs-lookup"><span data-stu-id="84321-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="84321-194">Aby składnik nadrzędny mógł udostępnić odwołanie do elementu innym składnikom, składnik nadrzędny może:</span><span class="sxs-lookup"><span data-stu-id="84321-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="84321-195">Zezwalaj składnikom podrzędnym na rejestrowanie wywołań zwrotnych.</span><span class="sxs-lookup"><span data-stu-id="84321-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="84321-196">Wywołaj zarejestrowane wywołania zwrotne podczas zdarzenia `OnAfterRender` z odwołaniem do elementu.</span><span class="sxs-lookup"><span data-stu-id="84321-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="84321-197">Pośrednio takie podejście umożliwia składnikom podrzędnym współdziałanie z odwołaniem do elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="84321-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="84321-198">Poniższy przykład Blazor webassembly ilustruje podejście.</span><span class="sxs-lookup"><span data-stu-id="84321-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="84321-199">W `<head>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="84321-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="84321-200">W `<body>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="84321-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="84321-201">*Pages/index. Razor* (składnik nadrzędny):</span><span class="sxs-lookup"><span data-stu-id="84321-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="84321-202">*Pages/index. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="84321-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="84321-203">*Shared/SurveyPrompt. Razor* (składnik podrzędny):</span><span class="sxs-lookup"><span data-stu-id="84321-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="84321-204">*Shared/SurveyPrompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="84321-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a><span data-ttu-id="84321-205">Zabezpieczenia wywołań międzyoperacyjnych w ramach funkcjonalności JS</span><span class="sxs-lookup"><span data-stu-id="84321-205">Harden JS interop calls</span></span>

<span data-ttu-id="84321-206">Usługa JS Interop może zakończyć się niepowodzeniem z powodu błędów sieci i powinna być traktowana jako niezawodna.</span><span class="sxs-lookup"><span data-stu-id="84321-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="84321-207">Domyślnie aplikacja serwerowa Blazor przeprowadziła limit czasu wywołań międzyoperacyjnych JS na serwerze po jednej minucie.</span><span class="sxs-lookup"><span data-stu-id="84321-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="84321-208">Jeśli aplikacja może tolerować bardziej agresywny limit czasu, na przykład 10 sekund, należy ustawić limit czasu przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="84321-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="84321-209">Globalnie w `Startup.ConfigureServices`Określ limit czasu:</span><span class="sxs-lookup"><span data-stu-id="84321-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="84321-210">Dla wywołania w kodzie składnika pojedyncze wywołanie może określać limit czasu:</span><span class="sxs-lookup"><span data-stu-id="84321-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="84321-211">Aby uzyskać więcej informacji na temat wyczerpania zasobów, zobacz <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="84321-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="84321-212">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="84321-212">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="84321-213">InteropComponent. Razor — przykład (repozytorium dotnet/AspNetCore w witrynie GitHub, 3,1 gałąź wydania)</span><span class="sxs-lookup"><span data-stu-id="84321-213">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="84321-214">[Wykonywanie dużych transferów danych w aplikacjach Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="84321-214">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>