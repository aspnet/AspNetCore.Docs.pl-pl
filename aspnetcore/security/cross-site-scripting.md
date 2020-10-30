---
title: Zapobiegaj skryptom między lokacjami (XSS) w ASP.NET Core
author: rick-anderson
description: Więcej informacji na temat skryptów między lokacjami (XSS) i techniki rozwiązywania tych luk w zabezpieczeniach aplikacji ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: 1c90a786efe8c3c205a729a2da9d3a99d0222012
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053088"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="0e2c6-103">Zapobiegaj skryptom między lokacjami (XSS) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e2c6-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="0e2c6-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e2c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0e2c6-105">Skrypt między lokacjami (XSS) stanowi lukę w zabezpieczeniach, która umożliwia atakującemu umieszczenie skryptów po stronie klienta (zazwyczaj JavaScript) na stronach sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="0e2c6-106">Gdy inni użytkownicy ładują strony, których dotyczy skrypt osoby atakującej, co umożliwi atakującemu kradzież :::no-loc(cookie)::: tokenów s i sesji, należy zmienić zawartość strony sieci Web za pomocą manipulowania dom lub przekierować przeglądarkę na inną stronę.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal :::no-loc(cookie):::s and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="0e2c6-107">Luki w zabezpieczeniach XSS są zwykle wykonywane, gdy aplikacja pobiera dane wejściowe użytkownika i wyprowadza ją na stronę bez sprawdzania poprawności, kodowania lub ucieczki.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="0e2c6-108">Ochrona aplikacji przed XSS</span><span class="sxs-lookup"><span data-stu-id="0e2c6-108">Protecting your application against XSS</span></span>

<span data-ttu-id="0e2c6-109">Na poziomie podstawowym ataki polegają na zalewaniu aplikacji do wstawienia `<script>` tagów do renderowanej strony lub przez wstawienie `On*` zdarzenia do elementu.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="0e2c6-110">Deweloperzy powinni używać następujących kroków zapobiegania, aby uniknąć wprowadzenia XSS do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="0e2c6-111">Nigdy nie umieszczaj niezaufanych danych w danych wejściowych HTML, chyba że wykonujesz poniższą procedurę.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="0e2c6-112">Niezaufane dane to wszelkie dane, które mogą być kontrolowane przez atakującego, dane wejściowe formularza HTML, ciągi zapytań, nagłówki HTTP, nawet źródła danych z bazy danych, ponieważ osoba atakująca może mieć możliwość naruszenia bazy danych, nawet jeśli nie mogą naruszyć aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="0e2c6-113">Przed umieszczeniem niezaufanych danych wewnątrz elementu HTML upewnij się, że jest on zakodowany w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="0e2c6-114">Kodowanie HTML przyjmuje takie znaki, jak &lt; i zmienia je na bezpieczną formę, taką jak &amp; lt;</span><span class="sxs-lookup"><span data-stu-id="0e2c6-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="0e2c6-115">Przed umieszczeniem niezaufanych danych w atrybucie HTML upewnij się, że jest on zakodowany w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="0e2c6-116">Kodowanie atrybutu HTML jest nadzbiorem kodowania HTML i koduje dodatkowe znaki takie jak "i".</span><span class="sxs-lookup"><span data-stu-id="0e2c6-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="0e2c6-117">Przed umieszczeniem niezaufanych danych w języku JavaScript Umieść dane w elemencie HTML, którego zawartość jest pobierana w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="0e2c6-118">Jeśli to nie jest możliwe, upewnij się, że dane są kodowane w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="0e2c6-119">Kodowanie JavaScript pobiera niebezpieczne znaki dla języka JavaScript i zastępuje je szesnastkową, na przykład będzie &lt; kodowane jako `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="0e2c6-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="0e2c6-120">Przed umieszczeniem niezaufanych danych w ciągu zapytania adresu URL upewnij się, że adres URL został zakodowany.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="0e2c6-121">Kodowanie HTML przy użyciu :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="0e2c6-121">HTML Encoding using :::no-loc(Razor):::</span></span>

<span data-ttu-id="0e2c6-122">:::no-loc(Razor):::Aparat używany w MVC automatycznie koduje wszystkie dane wyjściowe pochodzące ze zmiennych, chyba że naprawdę nie zadziała to w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-122">The :::no-loc(Razor)::: engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="0e2c6-123">Używa reguł kodowania atrybutu języka HTML za każdym razem, gdy używasz *@* dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="0e2c6-124">Ponieważ kodowanie atrybutu HTML jest nadzbiorem kodowania HTML, oznacza to, że nie trzeba niczego zachodzić, niezależnie od tego, czy należy użyć kodowania HTML czy kodowania atrybutów HTML.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="0e2c6-125">Musisz się upewnić, że w kontekście HTML użyto tylko @, a nie przy próbie wstawienia niezaufanych danych wejściowych bezpośrednio do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="0e2c6-126">Pomocnicy tagów również kodują dane wejściowe, które są używane w parametrach tagów.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="0e2c6-127">Wykonaj następujące czynności :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="0e2c6-127">Take the following :::no-loc(Razor)::: view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="0e2c6-128">Ten widok wyprowadza zawartość zmiennej *untrustedInput* .</span><span class="sxs-lookup"><span data-stu-id="0e2c6-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="0e2c6-129">Ta zmienna zawiera znaki, które są używane w atakach XSS, mianowicie &lt; "i &gt; .</span><span class="sxs-lookup"><span data-stu-id="0e2c6-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="0e2c6-130">Badanie źródła pokazuje renderowane dane wyjściowe kodowane jako:</span><span class="sxs-lookup"><span data-stu-id="0e2c6-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="0e2c6-131">ASP.NET Core MVC udostępnia `HtmlString` klasę, która nie jest automatycznie zakodowana w danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="0e2c6-132">Tego elementu nie należy używać w połączeniu z niezaufanymi danymi wejściowymi, ponieważ spowoduje to ujawnienie luki w zabezpieczeniach XSS.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="0e2c6-133">Kodowanie JavaScript przy użyciu :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="0e2c6-133">JavaScript Encoding using :::no-loc(Razor):::</span></span>

<span data-ttu-id="0e2c6-134">Czasami może się okazać, że chcesz wstawić wartość do języka JavaScript, aby przetworzyć ją w widoku.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="0e2c6-135">Istnieją dwa sposoby, aby to zrobić.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-135">There are two ways to do this.</span></span> <span data-ttu-id="0e2c6-136">Najbezpieczniejszym sposobem wstawiania wartości jest umieszczenie wartości w atrybucie danych tagu i pobranie go w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="0e2c6-137">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e2c6-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="0e2c6-138">Powyższy znacznik generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="0e2c6-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="0e2c6-139">Poprzedni kod generuje następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="0e2c6-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="0e2c6-140">Do \* **nie** można łączyć niezaufanych danych wejściowych w języku JavaScript w celu tworzenia elementów dom lub korzystania `document.write()` z dynamicznie generowanej zawartości.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-140">Do \* **NOT** _ concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="0e2c6-141">Użyj jednego z poniższych metod, aby zapobiec ujawnieniu kodu w oparciu o dane z modelu DOM: _ `createElement()` i przypisz wartości właściwości z odpowiednimi metodami lub właściwościami, takimi jak `node.textContent=` lub węzłem. InnerText = ".</span><span class="sxs-lookup"><span data-stu-id="0e2c6-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS: _ `createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="0e2c6-142">`document.CreateTextNode()` i Dołącz je do odpowiedniej lokalizacji DOM.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-142">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="0e2c6-143">Uzyskiwanie dostępu do koderów w kodzie</span><span class="sxs-lookup"><span data-stu-id="0e2c6-143">Accessing encoders in code</span></span>

<span data-ttu-id="0e2c6-144">Kodery HTML, JavaScript i URL są dostępne dla kodu na dwa sposoby, można je wstrzyknąć za pośrednictwem [iniekcji zależności](xref:fundamentals/dependency-injection) lub użyć domyślnych koderów zawartych w `System.Text.Encodings.Web` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="0e2c6-145">Jeśli używasz koderów domyślnych, wszystkie zastosowane do zakresów znaków, które mają być traktowane jako bezpieczne, nie zaczną obowiązywać — domyślne kodery będą używać najbezpieczniejszych reguł kodowania.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-145">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="0e2c6-146">Aby użyć konfigurowalnych koderów za pośrednictwem narzędzi, konstruktory powinny przyjmować parametr *HtmlEncode* , *JavaScriptEncoder* i *UrlEncoder* , zgodnie z potrzebami.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder* , *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="0e2c6-147">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="0e2c6-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="0e2c6-148">Parametry kodowania adresu URL</span><span class="sxs-lookup"><span data-stu-id="0e2c6-148">Encoding URL Parameters</span></span>

<span data-ttu-id="0e2c6-149">Jeśli chcesz skompilować ciąg zapytania URL z niezaufanymi danymi wejściowymi jako wartość, użyj `UrlEncoder` do kodowania wartości.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="0e2c6-150">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e2c6-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="0e2c6-151">Po kodowaniu zmienna encodedValue będzie zawierać `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="0e2c6-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="0e2c6-152">Spacje, cudzysłowy, interpunkcja i inne niebezpieczne znaki będą zakodowane procentowo na ich wartość szesnastkową, na przykład znak spacji stanie się %20.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="0e2c6-153">Nie używaj niezaufanych danych wejściowych jako części ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-153">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="0e2c6-154">Zawsze przekazuj niezaufane dane wejściowe jako wartość ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="0e2c6-155">Dostosowywanie koderów</span><span class="sxs-lookup"><span data-stu-id="0e2c6-155">Customizing the Encoders</span></span>

<span data-ttu-id="0e2c6-156">Domyślnie kodery wykorzystują bezpieczną listę ograniczoną do podstawowego zakresu Unicode w języku łacińskim i kodują wszystkie znaki poza tym zakresem jako odpowiedniki kodu znaku.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="0e2c6-157">To zachowanie ma także wpływ na :::no-loc(Razor)::: renderowanie TagHelper i HtmlHelper, ponieważ użyje koderów do wyprowadzania ciągów.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-157">This behavior also affects :::no-loc(Razor)::: TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="0e2c6-158">W związku z tym należy chronić przed nieznanymi lub przyszłymi błędami przeglądarki (poprzednie błędy przeglądarki wyzwolenie analizy na podstawie przetwarzania znaków innych niż angielski).</span><span class="sxs-lookup"><span data-stu-id="0e2c6-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="0e2c6-159">Jeśli witryna sieci Web wykonuje duże użycie znaków innych niż łacińskie, takich jak chińskie, cyrylica lub inne, to prawdopodobnie zachowanie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="0e2c6-160">Można dostosować listy bezpiecznych koderów, aby uwzględnić zakresy Unicode odpowiednie dla aplikacji podczas uruchamiania, w programie `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="0e2c6-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="0e2c6-161">Na przykład przy użyciu konfiguracji domyślnej można użyć :::no-loc(Razor)::: HtmlHelper podobnej do tego;</span><span class="sxs-lookup"><span data-stu-id="0e2c6-161">For example, using the default configuration you might use a :::no-loc(Razor)::: HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="0e2c6-162">Po wyświetleniu źródła strony sieci Web zobaczysz, że został on wyrenderowany w następujący sposób, z zakodowanym tekstem w języku chińskim.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="0e2c6-163">Aby rozszerzyć znaki traktowane jako bezpieczne przez koder, należy wstawić następujący wiersz do `ConfigureServices()` metody w `startup.cs` ;</span><span class="sxs-lookup"><span data-stu-id="0e2c6-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="0e2c6-164">Ten przykład rozszerza bezpieczną listę w celu uwzględnienia zakresu Unicode CjkUnifiedIdeographs.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="0e2c6-165">Renderowane dane wyjściowe staną się teraz</span><span class="sxs-lookup"><span data-stu-id="0e2c6-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="0e2c6-166">Zakresy bezpiecznych list są określone jako wykresy kodu Unicode, a nie Języki.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="0e2c6-167">[Standard Unicode](https://unicode.org/) ma listę [wykresów kodu](https://www.unicode.org/charts/index.html) , których można użyć do znalezienia wykresu zawierającego znaki.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-167">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="0e2c6-168">Każdy koder, kod HTML, JavaScript i adres URL, muszą być skonfigurowane osobno.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="0e2c6-169">Dostosowanie listy bezpiecznych ma wpływ tylko na kodery, które są źródłem przez DI.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="0e2c6-170">Jeśli użytkownik uzyskuje bezpośredni dostęp do kodera przy użyciu `System.Text.Encodings.Web.*Encoder.Default` domyślnego, zostanie użyty tylko podstawowy safelist.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="0e2c6-171">Gdzie ma nastąpić kodowanie?</span><span class="sxs-lookup"><span data-stu-id="0e2c6-171">Where should encoding take place?</span></span>

<span data-ttu-id="0e2c6-172">Ogólna przyjęta Metoda polega na tym, że kodowanie ma miejsce w punkcie wyjścia, a zakodowane wartości nigdy nie powinny być przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="0e2c6-173">Kodowanie w punkcie wyjścia umożliwia zmianę użycia danych, na przykład z HTML do wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="0e2c6-174">Umożliwia również łatwe wyszukiwanie danych bez konieczności kodowania wartości przed wyszukiwaniem i pozwala korzystać ze wszystkich zmian lub poprawek błędów w koderach.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="0e2c6-175">Weryfikacja jako technika zapobiegania XSS</span><span class="sxs-lookup"><span data-stu-id="0e2c6-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="0e2c6-176">Walidacja może być przydatnym narzędziem ograniczającym ataki XSS.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="0e2c6-177">Na przykład ciąg liczbowy zawierający tylko znaki 0-9 nie wywoła ataku typu XSS.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-177">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="0e2c6-178">Walidacja jest bardziej skomplikowana przy akceptowaniu kodu HTML w danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-178">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="0e2c6-179">Analiza danych wejściowych HTML jest trudna, jeśli nie jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-179">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="0e2c6-180">Zaawansowana, powiązana z analizatorem, który oddziela osadzony kod HTML, jest bezpieczniejsza opcja przyjmowania danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-180">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="0e2c6-181">Nigdy nie należy polegać wyłącznie na walidacji.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-181">Never rely on validation alone.</span></span> <span data-ttu-id="0e2c6-182">Zawsze Koduj niezaufane dane wejściowe przed wyjściem, niezależnie od tego, jakie sprawdzanie poprawności lub oczyszczanie zostało wykonane.</span><span class="sxs-lookup"><span data-stu-id="0e2c6-182">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
