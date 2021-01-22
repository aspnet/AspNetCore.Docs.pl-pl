## <a name="troubleshoot"></a><span data-ttu-id="c0880-101">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="c0880-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="c0880-102">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="c0880-102">Common errors</span></span>

* <span data-ttu-id="c0880-103">Nieautoryzowany klient dla usługi AAD</span><span class="sxs-lookup"><span data-stu-id="c0880-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="c0880-104">Informacja: uwierzytelnianie Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2] nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="c0880-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="c0880-105">Te wymagania nie zostały spełnione: DenyAnonymousAuthorizationRequirement: wymaga uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c0880-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="c0880-106">Błąd wywołania zwrotnego logowania z usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="c0880-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="c0880-107">Błąd: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="c0880-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="c0880-108">Zharmonizowan `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="c0880-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="c0880-109">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="c0880-109">To resolve the error:</span></span>

  1. <span data-ttu-id="c0880-110">W Azure Portal uzyskaj dostęp do [manifestu aplikacji](/azure/active-directory/develop/reference-app-manifest).</span><span class="sxs-lookup"><span data-stu-id="c0880-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="c0880-111">Ustaw [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) atrybut na `null` lub `true` .</span><span class="sxs-lookup"><span data-stu-id="c0880-111">Set the [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) attribute to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="c0880-112">Pliki cookie i dane lokacji</span><span class="sxs-lookup"><span data-stu-id="c0880-112">Cookies and site data</span></span>

<span data-ttu-id="c0880-113">Pliki cookie i dane lokacji mogą być utrwalane między aktualizacjami aplikacji i zakłócają testowanie i rozwiązywanie problemów.</span><span class="sxs-lookup"><span data-stu-id="c0880-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="c0880-114">W przypadku wprowadzania zmian w kodzie aplikacji należy wyczyścić następujące zmiany dotyczące konta użytkownika i konfiguracji aplikacji dostawcy:</span><span class="sxs-lookup"><span data-stu-id="c0880-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="c0880-115">Pliki cookie logowania użytkownika</span><span class="sxs-lookup"><span data-stu-id="c0880-115">User sign-in cookies</span></span>
* <span data-ttu-id="c0880-116">Pliki cookie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c0880-116">App cookies</span></span>
* <span data-ttu-id="c0880-117">Buforowane i przechowywane dane lokacji</span><span class="sxs-lookup"><span data-stu-id="c0880-117">Cached and stored site data</span></span>

<span data-ttu-id="c0880-118">Jednym z metod zapobiegania występowaniu plików cookie i danych lokacji z zakłócania testowania i rozwiązywania problemów jest:</span><span class="sxs-lookup"><span data-stu-id="c0880-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="c0880-119">Konfigurowanie przeglądarki</span><span class="sxs-lookup"><span data-stu-id="c0880-119">Configure a browser</span></span>
  * <span data-ttu-id="c0880-120">Użyj przeglądarki do testowania, który można skonfigurować, aby usunąć wszystkie pliki cookie i dane z lokacji za każdym razem, gdy przeglądarka zostanie zamknięta.</span><span class="sxs-lookup"><span data-stu-id="c0880-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="c0880-121">Upewnij się, że przeglądarka została ZAMKNIĘTA ręcznie lub przez IDE w celu dowolnych zmian w konfiguracji aplikacji, użytkownika testowego lub dostawcy.</span><span class="sxs-lookup"><span data-stu-id="c0880-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="c0880-122">Użyj polecenia niestandardowego, aby otworzyć przeglądarkę w trybie incognito lub prywatnym w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c0880-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="c0880-123">Otwórz okno dialogowe **Przeglądaj z programem** Visual Studio. </span><span class="sxs-lookup"><span data-stu-id="c0880-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="c0880-124">Wybierz przycisk **Add** (Dodaj).</span><span class="sxs-lookup"><span data-stu-id="c0880-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="c0880-125">Podaj ścieżkę do przeglądarki w polu **program** .</span><span class="sxs-lookup"><span data-stu-id="c0880-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="c0880-126">Następujące ścieżki plików wykonywalnych są typowymi lokalizacjami instalacji dla systemu Windows 10.</span><span class="sxs-lookup"><span data-stu-id="c0880-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="c0880-127">Jeśli przeglądarka jest zainstalowana w innej lokalizacji lub nie korzystasz z systemu Windows 10, podaj ścieżkę do pliku wykonywalnego przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c0880-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="c0880-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="c0880-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="c0880-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="c0880-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="c0880-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="c0880-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="c0880-131">W polu **argumenty** Podaj opcję wiersza polecenia, która jest wykorzystywana przez przeglądarkę do otwierania w trybie incognito lub prywatnym.</span><span class="sxs-lookup"><span data-stu-id="c0880-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="c0880-132">Niektóre przeglądarki wymagają adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0880-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="c0880-133">Microsoft Edge: Użyj `-inprivate` .</span><span class="sxs-lookup"><span data-stu-id="c0880-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="c0880-134">Google Chrome: Użyj `--incognito --new-window {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="c0880-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="c0880-135">Mozilla Firefox: Użyj `-private -url {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="c0880-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="c0880-136">Podaj nazwę w polu **przyjazna nazwa** .</span><span class="sxs-lookup"><span data-stu-id="c0880-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="c0880-137">Na przykład `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="c0880-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="c0880-138">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="c0880-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="c0880-139">Aby uniknąć konieczności wybierania profilu przeglądarki dla każdej iteracji testowania w aplikacji, Ustaw profil jako domyślny przy użyciu przycisku **Ustaw jako domyślny** .</span><span class="sxs-lookup"><span data-stu-id="c0880-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="c0880-140">Upewnij się, że w środowisku IDE jest ZAMKNIĘTA przeglądarka, aby wprowadzić zmiany w konfiguracji aplikacji, użytkownika testowego lub dostawcy.</span><span class="sxs-lookup"><span data-stu-id="c0880-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="c0880-141">Uruchom aplikację serwera</span><span class="sxs-lookup"><span data-stu-id="c0880-141">Run the Server app</span></span>

<span data-ttu-id="c0880-142">Podczas testowania i rozwiązywania problemów z hostowaną aplikacją Blazor upewnij się, że aplikacja jest uruchamiana z **`Server`** projektu.</span><span class="sxs-lookup"><span data-stu-id="c0880-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="c0880-143">Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="c0880-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="c0880-144">Wybierz przycisk **Run** (Uruchom).</span><span class="sxs-lookup"><span data-stu-id="c0880-144">Select the **Run** button.</span></span>
* <span data-ttu-id="c0880-145">Użyj **debugowania**  >  **Rozpocznij debugowanie** z menu.</span><span class="sxs-lookup"><span data-stu-id="c0880-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="c0880-146">Naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="c0880-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="c0880-147">Sprawdzanie zawartości tokenu sieci Web JSON (JWT)</span><span class="sxs-lookup"><span data-stu-id="c0880-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="c0880-148">Aby zdekodować token sieci Web JSON (JWT), użyj narzędzia [JWT.MS](https://jwt.ms/) firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c0880-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="c0880-149">Wartości w interfejsie użytkownika nigdy nie opuszczają przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c0880-149">Values in the UI never leave your browser.</span></span>