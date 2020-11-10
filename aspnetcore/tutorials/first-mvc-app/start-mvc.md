---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422772"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Wprowadzenie do ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.

Aplikacja zarządza bazą danych tytułów filmu. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodawanie i tworzenie szkieletu modelu.
> * Pracuj z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.
1. W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `MvcMovie` w polu **Nazwa projektu**. Ważne jest, aby użyć tej dokładnej nazwy, z uwzględnieniem wielkich liter, więc każdy pasuje do tego, `namespace` kiedy kod jest kopiowany.
1. Wybierz przycisk **Utwórz**.
1. W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:
    1. **Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.
    1. **ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.
    1. **Utwórz**

![Tworzenie nowej aplikacji sieci Web ASP.NET Core ](start-mvc/_static/5/mvc.png)

Aby zapoznać się z alternatywnymi metodami tworzenia projektu, zobacz [Tworzenie nowego projektu w programie Visual Studio](/visualstudio/ide/create-new-project).

Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony. Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji. Jest to podstawowy projekt startowy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

W samouczku założono znajomość VS Code. Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**  Wybierz pozycję **Tak**

  * `dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**. W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli jest prezentowana opcja wyboru **platformy docelowej** , należy wybrać najnowszą wersję 5. x.

  Wybierz opcję **Dalej**.

* Nazwij projekt **MvcMovie** , a następnie wybierz pozycję **Utwórz**.

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uruchamianie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację. Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` . To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
* Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.
* Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* Możesz debugować aplikację, wybierając przycisk **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  Na poniższej ilustracji przedstawiono aplikację:

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` . Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

  Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację. Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port. Po uruchomieniu aplikacji zobaczysz inny numer portu.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).

  Na poniższej ilustracji przedstawiono aplikację:

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.

> [!div class="step-by-step"]
> [Dalej](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.

Aplikacja zarządza bazą danych tytułów filmu. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodawanie i tworzenie szkieletu modelu.
> * Pracuj z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

* Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**. Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)

* Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**. Z listy rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,1** , a następnie wybierz pozycję **Utwórz**.

![Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony. Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji. Jest to podstawowy projekt startowy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Samouczek założono familarity z VS Code. Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**  Wybierz pozycję **Tak**

  * `dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**. W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli jest prezentowana opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 3. x.

  Wybierz opcję **Dalej**.

* Nazwij projekt **MvcMovie** , a następnie wybierz pozycję **Utwórz**.

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uruchamianie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację. Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` . To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
* Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.
* Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* Możesz debugować aplikację, wybierając przycisk **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  Na poniższej ilustracji przedstawiono aplikację:

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` . Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

  Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację. Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port. Po uruchomieniu aplikacji zobaczysz inny numer portu.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).

  Na poniższej ilustracji przedstawiono aplikację:

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.

> [!div class="step-by-step"]
> [Dalej](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.

Aplikacja zarządza bazą danych tytułów filmu. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodawanie i tworzenie szkieletu modelu.
> * Pracuj z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

* Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**. Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)


* Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)** , a następnie wybierz pozycję **Utwórz**.

![Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web ](start-mvc/_static/new_project22-21.png)

Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony. Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji. Jest to podstawowy projekt początkowy i jest dobrym miejscem do rozpoczęcia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Samouczek założono familarity z VS Code. Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**  Wybierz pozycję **Tak**

  * `dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**. W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli jest prezentowana opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 2. x.

  Wybierz opcję **Dalej**.

* Nazwij projekt **MvcMovie** , a następnie wybierz pozycję **Utwórz**.

---

### <a name="run-the-app"></a>Uruchamianie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację. Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` . To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
* Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.
* Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* Możesz debugować aplikację, wybierając przycisk **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

* Wybierz pozycję **Zaakceptuj** , aby wyrazić zgodę na śledzenie. Ta aplikacja nie śledzi informacji osobistych. Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` . Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

  Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.

* Wybierz pozycję **Zaakceptuj** , aby wyrazić zgodę na śledzenie. Ta aplikacja nie śledzi informacji osobistych. Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację. Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port. Po uruchomieniu aplikacji zobaczysz inny numer portu.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).

* Wybierz pozycję **Zaakceptuj** , aby wyrazić zgodę na śledzenie. Ta aplikacja nie śledzi informacji osobistych. Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).

  ![Strona główna lub indeks](./start-mvc/_static/output_privacy_macos.png)

  Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.

> [!div class="step-by-step"]
> [Dalej](adding-controller.md)

::: moniker-end
