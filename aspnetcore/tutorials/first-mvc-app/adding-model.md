---
title: Część 4. Dodawanie modelu do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 4 samouczków z serii ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422727"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="de33f-103">Część 4. Dodawanie modelu do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="de33f-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="de33f-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="de33f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="de33f-105">W tej sekcji dodasz klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="de33f-106">Te klasy będą częścią " **m** odelu" aplikacji **m** VC.</span><span class="sxs-lookup"><span data-stu-id="de33f-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="de33f-107">Te klasy są używane z [Entity Framework Core](/ef/core) (Ef Core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="de33f-108">EF Core to struktura obiektu mapowania relacyjnego (ORM), która upraszcza kod dostępu do danych, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="de33f-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="de33f-109">Klasy modelu, które tworzysz, są nazywane klasami POCO ( **z Lain** **P** LR **C** **o** biekty), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="de33f-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="de33f-110">Po prostu definiują właściwości danych, które będą przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="de33f-111">W tym samouczku najpierw napiszesz klasy modelu, a EF Core tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="de33f-112">Dodaj klasę modelu danych</span><span class="sxs-lookup"><span data-stu-id="de33f-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-114">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="de33f-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="de33f-115">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="de33f-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="de33f-117">Dodaj plik o nazwie *Movie.cs* do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="de33f-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="de33f-119">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **nową klasę**  >  **pustą** klasy.</span><span class="sxs-lookup"><span data-stu-id="de33f-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="de33f-120">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="de33f-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="de33f-121">Zaktualizuj plik *Movie.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="de33f-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="de33f-122">`Movie`Klasa zawiera `Id` pole, które jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="de33f-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="de33f-123"><xref:System.ComponentModel.DataAnnotations.DataType>Atrybut on `ReleaseDate` określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="de33f-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="de33f-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="de33f-124">With this attribute:</span></span>

* <span data-ttu-id="de33f-125">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="de33f-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="de33f-126">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="de33f-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="de33f-127">[Adnotacje DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="de33f-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="de33f-128">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="de33f-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-130">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="de33f-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="de33f-132">W obszarze PMC Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="de33f-133">Poprzednie polecenie dodaje dostawcę SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="de33f-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="de33f-134">Pakiet dostawcy instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="de33f-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="de33f-135">Dodatkowe pakiety są instalowane automatycznie w kroku tworzenia szkieletu w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="de33f-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-137">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="de33f-138">W menu **projekt** wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="de33f-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="de33f-139">W polu **wyszukiwania** w prawym górnym rogu wprowadź `Microsoft.EntityFrameworkCore.SQLite` i naciśnij klawisz **Return** , aby wyszukać.</span><span class="sxs-lookup"><span data-stu-id="de33f-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="de33f-140">Wybierz pasujący pakiet NuGet i naciśnij przycisk **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="de33f-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Dodaj Entity Framework Core pakiet NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="de33f-142">Zostanie wyświetlone okno dialogowe **Wybieranie projektów** z `MvcMovie` wybranym projektem.</span><span class="sxs-lookup"><span data-stu-id="de33f-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="de33f-143">Naciśnij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="de33f-143">Press the **Ok** button.</span></span>

<span data-ttu-id="de33f-144">Zostanie wyświetlone okno dialogowe **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="de33f-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="de33f-145">Przejrzyj odpowiednie licencje, a następnie kliknij przycisk **Akceptuj** .</span><span class="sxs-lookup"><span data-stu-id="de33f-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="de33f-146">Powtórz powyższe kroki, aby zainstalować następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="de33f-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="de33f-147">Tworzenie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="de33f-147">Create a database context class</span></span>

<span data-ttu-id="de33f-148">Klasa kontekstu bazy danych jest wymagana do koordynowania funkcji EF Core (tworzenie, odczytywanie, aktualizowanie, usuwanie) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="de33f-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="de33f-149">Kontekst bazy danych pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i określa jednostki, które mają zostać uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="de33f-150">Utwórz folder *danych* .</span><span class="sxs-lookup"><span data-stu-id="de33f-150">Create a *Data* folder.</span></span>

<span data-ttu-id="de33f-151">Dodaj plik *Data/MvcMovieContext. cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="de33f-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="de33f-152">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="de33f-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="de33f-153">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="de33f-154">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="de33f-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="de33f-155">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="de33f-155">Register the database context</span></span>

<span data-ttu-id="de33f-156">ASP.NET Core jest skompilowany przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="de33f-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="de33f-157">Usługi (takie jak kontekst EF Core DB) muszą być zarejestrowane przy użyciu funkcji "DI" podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de33f-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="de33f-158">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="de33f-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="de33f-159">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="de33f-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="de33f-160">W tej sekcji rejestrujesz kontekst bazy danych przy użyciu funkcji DI Container.</span><span class="sxs-lookup"><span data-stu-id="de33f-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="de33f-161">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="de33f-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="de33f-162">Dodaj następujący wyróżniony kod w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de33f-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-163">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-164">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="de33f-165">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="de33f-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="de33f-166">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="de33f-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="de33f-167">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="de33f-167">Add a database connection string</span></span>

<span data-ttu-id="de33f-168">Dodaj parametry połączenia do *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="de33f-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-169">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-170">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="de33f-171">Kompiluj projekt jako sprawdzenie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="de33f-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="de33f-172">Strony z filmem szkieletu</span><span class="sxs-lookup"><span data-stu-id="de33f-172">Scaffold movie pages</span></span>

<span data-ttu-id="de33f-173">Użyj narzędzia do tworzenia szkieletu, aby utworzyć strony z przykładem tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="de33f-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-174">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-175">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *controllers* , **> Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="de33f-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Widok powyżej kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="de33f-177">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC z widokami przy użyciu Entity Framework > Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="de33f-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="de33f-179">Ukończ okno dialogowe **Dodawanie kontrolera** :</span><span class="sxs-lookup"><span data-stu-id="de33f-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="de33f-180">**Model Class:** *Movie (MvcMovie. models)*</span><span class="sxs-lookup"><span data-stu-id="de33f-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="de33f-181">**Klasa kontekstu danych:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="de33f-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Dodaj kontekst danych](adding-model/_static/dc5.png)

* <span data-ttu-id="de33f-183">**Widoki:** Zachowaj wartość domyślną dla każdej zaznaczonej opcji</span><span class="sxs-lookup"><span data-stu-id="de33f-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="de33f-184">**Nazwa kontrolera:** Zachowaj domyślną *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="de33f-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="de33f-185">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="de33f-185">Select **Add**</span></span>

<span data-ttu-id="de33f-186">Program Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="de33f-186">Visual Studio creates:</span></span>

* <span data-ttu-id="de33f-187">Kontroler filmów ( *controllers/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="de33f-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="de33f-188">Razor Wyświetlanie plików na potrzeby tworzenia, usuwania, szczegółów, edytowania i indeksowania stron ( *widoki/filmy/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="de33f-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="de33f-189">Automatyczne tworzenie tych plików jest znane jako *rusztowania*.</span><span class="sxs-lookup"><span data-stu-id="de33f-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="de33f-191">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs* , *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="de33f-192">W systemie Linux wyeksportuj ścieżkę narzędzia do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="de33f-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="de33f-193">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-194">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="de33f-195">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs* , *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="de33f-196">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="de33f-197">Nie można jeszcze użyć stron szkieletowych, ponieważ baza danych nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="de33f-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="de33f-198">Jeśli uruchomisz aplikację i klikniesz link **aplikacji filmowej** , uzyskasz dostęp do *otwartej bazy danych* lub *nie ma takiej tabeli:* komunikat o błędzie filmu.</span><span class="sxs-lookup"><span data-stu-id="de33f-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="de33f-199">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="de33f-199">Initial migration</span></span>

<span data-ttu-id="de33f-200">Użyj funkcji [migracji](xref:data/ef-mvc/migrations) EF Core, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="de33f-201">Migracje to zestaw narzędzi umożliwiających tworzenie i aktualizowanie bazy danych w celu dopasowania jej do modelu danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-202">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-203">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="de33f-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="de33f-204">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="de33f-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="de33f-205">`Add-Migration InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="de33f-206">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="de33f-207">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="de33f-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="de33f-208">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="de33f-209">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie.</span><span class="sxs-lookup"><span data-stu-id="de33f-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="de33f-210">`Update-Database`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="de33f-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="de33f-211">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="de33f-212">Polecenie aktualizacji bazy danych generuje następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="de33f-213">Nie określono typu dla kolumny dziesiętnej "price" w typie jednostki "Movie".</span><span class="sxs-lookup"><span data-stu-id="de33f-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="de33f-214">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="de33f-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="de33f-215">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="de33f-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="de33f-216">Możesz zignorować to ostrzeżenie. zostanie on rozwiązany w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="de33f-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-217">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="de33f-218">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="de33f-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="de33f-219">`ef migrations add InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="de33f-220">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="de33f-221">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="de33f-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="de33f-222">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="de33f-223">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie (w pliku *Data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="de33f-224">`ef database update`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="de33f-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="de33f-225">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="de33f-226">Klasa InitialCreate</span><span class="sxs-lookup"><span data-stu-id="de33f-226">The InitialCreate class</span></span>

<span data-ttu-id="de33f-227">Przejrzyj *migracje/{timestamp} _InitialCreate* pliku migracji CS:</span><span class="sxs-lookup"><span data-stu-id="de33f-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="de33f-228">`Up`Metoda tworzy tabelę filmów i konfiguruje `Id` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="de33f-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="de33f-229">`Down`Metoda przywraca zmiany schematu wykonane podczas `Up` migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="de33f-230">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="de33f-230">Test the app</span></span>

* <span data-ttu-id="de33f-231">Uruchom aplikację i kliknij link **aplikacji filmowej** .</span><span class="sxs-lookup"><span data-stu-id="de33f-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="de33f-232">Jeśli zostanie wyświetlony wyjątek podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="de33f-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-233">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-234">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="de33f-235">Prawdopodobnie pominięto [krok migracji](#migration).</span><span class="sxs-lookup"><span data-stu-id="de33f-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="de33f-236">Przetestuj stronę **Tworzenie** .</span><span class="sxs-lookup"><span data-stu-id="de33f-236">Test the **Create** page.</span></span> <span data-ttu-id="de33f-237">Wprowadź i prześlij dane.</span><span class="sxs-lookup"><span data-stu-id="de33f-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="de33f-238">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="de33f-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="de33f-239">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="de33f-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="de33f-240">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="de33f-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="de33f-241">Przetestuj strony **Edytuj** , **szczegóły** i **Usuń** .</span><span class="sxs-lookup"><span data-stu-id="de33f-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="de33f-242">Wstrzykiwanie zależności w kontrolerze</span><span class="sxs-lookup"><span data-stu-id="de33f-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-243">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-244">Otwórz plik *controllers/MoviesController. cs* i zapoznaj się z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="de33f-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="de33f-245">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="de33f-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="de33f-246">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="de33f-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-247">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="de33f-248">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="de33f-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="de33f-249">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="de33f-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="de33f-250">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="de33f-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="de33f-251">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="de33f-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="de33f-252">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="de33f-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="de33f-253">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="de33f-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="de33f-254">Modele silnie wpisane i @model słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="de33f-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="de33f-255">Wcześniej w tym samouczku pokazano, jak kontroler może przekazać dane lub obiekty do widoku przy użyciu `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="de33f-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="de33f-256">`ViewData`Słownik jest obiektem dynamicznym, który zapewnia wygodny, późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="de33f-257">MVC oferuje również możliwość przekazywania obiektów modelu silnie typu do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="de33f-258">Takie silnie wpisane podejście umożliwia sprawdzanie kodu czasu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="de33f-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="de33f-259">Mechanizm tworzenia szkieletu używa tego podejścia (czyli przekazania silnie określonego modelu) z `MoviesController` klasą i widokami.</span><span class="sxs-lookup"><span data-stu-id="de33f-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="de33f-260">Przejrzyj wygenerowaną `Details` metodę w pliku *controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="de33f-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="de33f-261">`id`Parametr jest zazwyczaj przesyłany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="de33f-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="de33f-262">Na przykład `https://localhost:5001/movies/details/1` zestawy:</span><span class="sxs-lookup"><span data-stu-id="de33f-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="de33f-263">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="de33f-264">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="de33f-265">Identyfikator 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="de33f-266">Można również przekazać `id` za pomocą ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="de33f-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="de33f-267">`id`Parametr jest zdefiniowany jako [typ dopuszczający wartość null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) w przypadku, gdy nie podano wartości identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="de33f-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="de33f-268">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przesyłane do, `FirstOrDefaultAsync` Aby wybrać jednostki filmu, które pasują do wartości danych trasy lub ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="de33f-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="de33f-269">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest przesyłane do `Details` widoku:</span><span class="sxs-lookup"><span data-stu-id="de33f-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="de33f-270">Zapoznaj się z zawartością pliku *widoki/filmy/szczegóły. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="de33f-271">`@model`Instrukcja w górnej części pliku widoku określa typ obiektu, który jest oczekiwany przez widok.</span><span class="sxs-lookup"><span data-stu-id="de33f-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="de33f-272">Po utworzeniu kontrolera filmu `@model` uwzględniono następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="de33f-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="de33f-273">Ta `@model` dyrektywa zezwala na dostęp do filmu, który kontroler przeszedł do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="de33f-274">`Model`Obiekt ma silną wartość.</span><span class="sxs-lookup"><span data-stu-id="de33f-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="de33f-275">Na przykład w widoku *details. cshtml* kod przekazuje każde pole filmu do `DisplayNameFor` `DisplayFor` pomocników HTML z obiektem o jednoznacznie określonym typie `Model` .</span><span class="sxs-lookup"><span data-stu-id="de33f-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="de33f-276">`Create`Metody i `Edit` i widoki również przekazują `Movie` obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="de33f-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="de33f-277">Sprawdź widok *index. cshtml* i `Index` metodę w kontrolerze filmów.</span><span class="sxs-lookup"><span data-stu-id="de33f-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="de33f-278">Zwróć uwagę, jak kod tworzy `List` obiekt, gdy wywołuje `View` metodę.</span><span class="sxs-lookup"><span data-stu-id="de33f-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="de33f-279">Kod przekazuje tę `Movies` listę z `Index` metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="de33f-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="de33f-280">Gdy kontroler filmów został utworzony, szkielet zawiera następujące `@model` instrukcje w górnej części pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="de33f-281">`@model`Dyrektywa pozwala uzyskać dostęp do listy filmów przekazaną przez kontroler do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="de33f-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="de33f-282">Na przykład w widoku *index. cshtml* kod przechodzi przez filmy z `foreach` instrukcją względem obiektu silnie wpisanego `Model` :</span><span class="sxs-lookup"><span data-stu-id="de33f-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="de33f-283">Ponieważ `Model` obiekt jest silnie określony (jako `IEnumerable<Movie>` obiekt), każdy element w pętli jest wpisywany jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="de33f-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="de33f-284">Dzięki temu możesz uzyskać kontrolę czasu kompilowania kodu.</span><span class="sxs-lookup"><span data-stu-id="de33f-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de33f-285">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="de33f-285">Additional resources</span></span>

* [<span data-ttu-id="de33f-286">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="de33f-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="de33f-287">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="de33f-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="de33f-288">[Poprzednie dodanie widoku](adding-view.md) 
>  [Następna praca z SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="de33f-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="de33f-289">Dodaj klasę modelu danych</span><span class="sxs-lookup"><span data-stu-id="de33f-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-290">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-291">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="de33f-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="de33f-292">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="de33f-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="de33f-294">Dodaj plik o nazwie *Movie.cs* do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="de33f-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-295">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="de33f-296">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **nową klasę**  >  **pustą** klasy.</span><span class="sxs-lookup"><span data-stu-id="de33f-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="de33f-297">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="de33f-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="de33f-298">Zaktualizuj plik *Movie.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="de33f-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="de33f-299">`Movie`Klasa zawiera `Id` pole, które jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="de33f-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="de33f-300"><xref:System.ComponentModel.DataAnnotations.DataType>Atrybut on `ReleaseDate` określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="de33f-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="de33f-301">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="de33f-301">With this attribute:</span></span>

* <span data-ttu-id="de33f-302">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="de33f-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="de33f-303">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="de33f-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="de33f-304">[Adnotacje DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="de33f-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="de33f-305">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="de33f-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-306">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-307">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="de33f-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="de33f-309">W obszarze PMC Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="de33f-310">Poprzednie polecenie dodaje dostawcę SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="de33f-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="de33f-311">Pakiet dostawcy instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="de33f-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="de33f-312">Dodatkowe pakiety są instalowane automatycznie w kroku tworzenia szkieletu w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="de33f-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-314">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="de33f-315">W menu **projekt** wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="de33f-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="de33f-316">W polu **wyszukiwania** w prawym górnym rogu wprowadź `Microsoft.EntityFrameworkCore.SQLite` i naciśnij klawisz **Return** , aby wyszukać.</span><span class="sxs-lookup"><span data-stu-id="de33f-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="de33f-317">Wybierz pasujący pakiet NuGet i naciśnij przycisk **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="de33f-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Dodaj Entity Framework Core pakiet NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="de33f-319">Zostanie wyświetlone okno dialogowe **Wybieranie projektów** z `MvcMovie` wybranym projektem.</span><span class="sxs-lookup"><span data-stu-id="de33f-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="de33f-320">Naciśnij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="de33f-320">Press the **Ok** button.</span></span>

<span data-ttu-id="de33f-321">Zostanie wyświetlone okno dialogowe **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="de33f-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="de33f-322">Przejrzyj odpowiednie licencje, a następnie kliknij przycisk **Akceptuj** .</span><span class="sxs-lookup"><span data-stu-id="de33f-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="de33f-323">Powtórz powyższe kroki, aby zainstalować następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="de33f-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="de33f-324">Tworzenie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="de33f-324">Create a database context class</span></span>

<span data-ttu-id="de33f-325">Klasa kontekstu bazy danych jest wymagana do koordynowania funkcji EF Core (tworzenie, odczytywanie, aktualizowanie, usuwanie) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="de33f-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="de33f-326">Kontekst bazy danych pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i określa jednostki, które mają zostać uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="de33f-327">Utwórz folder *danych* .</span><span class="sxs-lookup"><span data-stu-id="de33f-327">Create a *Data* folder.</span></span>

<span data-ttu-id="de33f-328">Dodaj plik *Data/MvcMovieContext. cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="de33f-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="de33f-329">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="de33f-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="de33f-330">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="de33f-331">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="de33f-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="de33f-332">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="de33f-332">Register the database context</span></span>

<span data-ttu-id="de33f-333">ASP.NET Core jest skompilowany przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="de33f-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="de33f-334">Usługi (takie jak kontekst EF Core DB) muszą być zarejestrowane przy użyciu funkcji "DI" podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de33f-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="de33f-335">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="de33f-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="de33f-336">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="de33f-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="de33f-337">W tej sekcji rejestrujesz kontekst bazy danych przy użyciu funkcji DI Container.</span><span class="sxs-lookup"><span data-stu-id="de33f-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="de33f-338">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="de33f-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="de33f-339">Dodaj następujący wyróżniony kod w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de33f-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-340">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-341">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="de33f-342">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="de33f-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="de33f-343">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="de33f-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="de33f-344">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="de33f-344">Add a database connection string</span></span>

<span data-ttu-id="de33f-345">Dodaj parametry połączenia do *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="de33f-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-346">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-347">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="de33f-348">Kompiluj projekt jako sprawdzenie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="de33f-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="de33f-349">Strony z filmem szkieletu</span><span class="sxs-lookup"><span data-stu-id="de33f-349">Scaffold movie pages</span></span>

<span data-ttu-id="de33f-350">Użyj narzędzia do tworzenia szkieletu, aby utworzyć strony z przykładem tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="de33f-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-351">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-352">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *controllers* , **> Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="de33f-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Widok powyżej kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="de33f-354">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC z widokami przy użyciu Entity Framework > Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="de33f-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="de33f-356">Ukończ okno dialogowe **Dodawanie kontrolera** :</span><span class="sxs-lookup"><span data-stu-id="de33f-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="de33f-357">**Model Class:** *Movie (MvcMovie. models)*</span><span class="sxs-lookup"><span data-stu-id="de33f-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="de33f-358">**Klasa kontekstu danych:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="de33f-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Dodaj kontekst danych](adding-model/_static/dc3.png)

* <span data-ttu-id="de33f-360">**Widoki:** Zachowaj wartość domyślną dla każdej zaznaczonej opcji</span><span class="sxs-lookup"><span data-stu-id="de33f-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="de33f-361">**Nazwa kontrolera:** Zachowaj domyślną *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="de33f-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="de33f-362">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="de33f-362">Select **Add**</span></span>

<span data-ttu-id="de33f-363">Program Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="de33f-363">Visual Studio creates:</span></span>

* <span data-ttu-id="de33f-364">Kontroler filmów ( *controllers/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="de33f-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="de33f-365">Razor Wyświetlanie plików na potrzeby tworzenia, usuwania, szczegółów, edytowania i indeksowania stron ( *widoki/filmy/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="de33f-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="de33f-366">Automatyczne tworzenie tych plików jest znane jako *rusztowania*.</span><span class="sxs-lookup"><span data-stu-id="de33f-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="de33f-368">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs* , *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="de33f-369">W systemie Linux wyeksportuj ścieżkę narzędzia do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="de33f-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="de33f-370">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-371">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="de33f-372">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs* , *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="de33f-373">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="de33f-374">Nie można jeszcze użyć stron szkieletowych, ponieważ baza danych nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="de33f-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="de33f-375">Jeśli uruchomisz aplikację i klikniesz link **aplikacji filmowej** , uzyskasz dostęp do *otwartej bazy danych* lub *nie ma takiej tabeli:* komunikat o błędzie filmu.</span><span class="sxs-lookup"><span data-stu-id="de33f-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="de33f-376">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="de33f-376">Initial migration</span></span>

<span data-ttu-id="de33f-377">Użyj funkcji [migracji](xref:data/ef-mvc/migrations) EF Core, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="de33f-378">Migracje to zestaw narzędzi umożliwiających tworzenie i aktualizowanie bazy danych w celu dopasowania jej do modelu danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-379">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-380">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="de33f-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="de33f-381">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="de33f-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="de33f-382">`Add-Migration InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="de33f-383">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="de33f-384">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="de33f-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="de33f-385">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="de33f-386">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie.</span><span class="sxs-lookup"><span data-stu-id="de33f-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="de33f-387">`Update-Database`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="de33f-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="de33f-388">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="de33f-389">Polecenie aktualizacji bazy danych generuje następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="de33f-390">Nie określono typu dla kolumny dziesiętnej "price" w typie jednostki "Movie".</span><span class="sxs-lookup"><span data-stu-id="de33f-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="de33f-391">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="de33f-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="de33f-392">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="de33f-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="de33f-393">Możesz zignorować to ostrzeżenie. zostanie on rozwiązany w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="de33f-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-394">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="de33f-395">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="de33f-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="de33f-396">`ef migrations add InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="de33f-397">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="de33f-398">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="de33f-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="de33f-399">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="de33f-400">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie (w pliku *Data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="de33f-401">`ef database update`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="de33f-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="de33f-402">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="de33f-403">Klasa InitialCreate</span><span class="sxs-lookup"><span data-stu-id="de33f-403">The InitialCreate class</span></span>

<span data-ttu-id="de33f-404">Przejrzyj *migracje/{timestamp} _InitialCreate* pliku migracji CS:</span><span class="sxs-lookup"><span data-stu-id="de33f-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="de33f-405">`Up`Metoda tworzy tabelę filmów i konfiguruje `Id` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="de33f-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="de33f-406">`Down`Metoda przywraca zmiany schematu wykonane podczas `Up` migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="de33f-407">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="de33f-407">Test the app</span></span>

* <span data-ttu-id="de33f-408">Uruchom aplikację i kliknij link **aplikacji filmowej** .</span><span class="sxs-lookup"><span data-stu-id="de33f-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="de33f-409">Jeśli zostanie wyświetlony wyjątek podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="de33f-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-410">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-411">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="de33f-412">Prawdopodobnie pominięto [krok migracji](#migration).</span><span class="sxs-lookup"><span data-stu-id="de33f-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="de33f-413">Przetestuj stronę **Tworzenie** .</span><span class="sxs-lookup"><span data-stu-id="de33f-413">Test the **Create** page.</span></span> <span data-ttu-id="de33f-414">Wprowadź i prześlij dane.</span><span class="sxs-lookup"><span data-stu-id="de33f-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="de33f-415">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="de33f-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="de33f-416">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="de33f-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="de33f-417">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="de33f-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="de33f-418">Przetestuj strony **Edytuj** , **szczegóły** i **Usuń** .</span><span class="sxs-lookup"><span data-stu-id="de33f-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="de33f-419">Wstrzykiwanie zależności w kontrolerze</span><span class="sxs-lookup"><span data-stu-id="de33f-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-420">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-421">Otwórz plik *controllers/MoviesController. cs* i zapoznaj się z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="de33f-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="de33f-422">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="de33f-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="de33f-423">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="de33f-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-424">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="de33f-425">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="de33f-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="de33f-426">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="de33f-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="de33f-427">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="de33f-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="de33f-428">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="de33f-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="de33f-429">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="de33f-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="de33f-430">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="de33f-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="de33f-431">Modele silnie wpisane i @model słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="de33f-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="de33f-432">Wcześniej w tym samouczku pokazano, jak kontroler może przekazać dane lub obiekty do widoku przy użyciu `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="de33f-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="de33f-433">`ViewData`Słownik jest obiektem dynamicznym, który zapewnia wygodny, późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="de33f-434">MVC oferuje również możliwość przekazywania obiektów modelu silnie typu do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="de33f-435">Takie silnie wpisane podejście umożliwia sprawdzanie kodu czasu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="de33f-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="de33f-436">Mechanizm tworzenia szkieletu używa tego podejścia (czyli przekazania silnie określonego modelu) z `MoviesController` klasą i widokami.</span><span class="sxs-lookup"><span data-stu-id="de33f-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="de33f-437">Przejrzyj wygenerowaną `Details` metodę w pliku *controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="de33f-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="de33f-438">`id`Parametr jest zazwyczaj przesyłany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="de33f-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="de33f-439">Na przykład `https://localhost:5001/movies/details/1` zestawy:</span><span class="sxs-lookup"><span data-stu-id="de33f-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="de33f-440">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="de33f-441">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="de33f-442">Identyfikator 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="de33f-443">Można również przekazać `id` za pomocą ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="de33f-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="de33f-444">`id`Parametr jest zdefiniowany jako [typ dopuszczający wartość null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) w przypadku, gdy nie podano wartości identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="de33f-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="de33f-445">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przesyłane do, `FirstOrDefaultAsync` Aby wybrać jednostki filmu, które pasują do wartości danych trasy lub ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="de33f-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="de33f-446">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest przesyłane do `Details` widoku:</span><span class="sxs-lookup"><span data-stu-id="de33f-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="de33f-447">Zapoznaj się z zawartością pliku *widoki/filmy/szczegóły. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="de33f-448">`@model`Instrukcja w górnej części pliku widoku określa typ obiektu, który jest oczekiwany przez widok.</span><span class="sxs-lookup"><span data-stu-id="de33f-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="de33f-449">Po utworzeniu kontrolera filmu `@model` uwzględniono następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="de33f-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="de33f-450">Ta `@model` dyrektywa zezwala na dostęp do filmu, który kontroler przeszedł do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="de33f-451">`Model`Obiekt ma silną wartość.</span><span class="sxs-lookup"><span data-stu-id="de33f-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="de33f-452">Na przykład w widoku *details. cshtml* kod przekazuje każde pole filmu do `DisplayNameFor` `DisplayFor` pomocników HTML z obiektem o jednoznacznie określonym typie `Model` .</span><span class="sxs-lookup"><span data-stu-id="de33f-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="de33f-453">`Create`Metody i `Edit` i widoki również przekazują `Movie` obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="de33f-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="de33f-454">Sprawdź widok *index. cshtml* i `Index` metodę w kontrolerze filmów.</span><span class="sxs-lookup"><span data-stu-id="de33f-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="de33f-455">Zwróć uwagę, jak kod tworzy `List` obiekt, gdy wywołuje `View` metodę.</span><span class="sxs-lookup"><span data-stu-id="de33f-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="de33f-456">Kod przekazuje tę `Movies` listę z `Index` metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="de33f-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="de33f-457">Gdy kontroler filmów został utworzony, szkielet zawiera następujące `@model` instrukcje w górnej części pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="de33f-458">`@model`Dyrektywa pozwala uzyskać dostęp do listy filmów przekazaną przez kontroler do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="de33f-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="de33f-459">Na przykład w widoku *index. cshtml* kod przechodzi przez filmy z `foreach` instrukcją względem obiektu silnie wpisanego `Model` :</span><span class="sxs-lookup"><span data-stu-id="de33f-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="de33f-460">Ponieważ `Model` obiekt jest silnie określony (jako `IEnumerable<Movie>` obiekt), każdy element w pętli jest wpisywany jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="de33f-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="de33f-461">Dzięki temu możesz uzyskać kontrolę czasu kompilowania kodu.</span><span class="sxs-lookup"><span data-stu-id="de33f-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de33f-462">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="de33f-462">Additional resources</span></span>

* [<span data-ttu-id="de33f-463">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="de33f-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="de33f-464">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="de33f-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="de33f-465">[Poprzednie dodanie widoku](adding-view.md) 
>  [Następna praca z SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="de33f-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="de33f-466">Dodaj klasę modelu danych</span><span class="sxs-lookup"><span data-stu-id="de33f-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-467">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-468">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="de33f-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="de33f-469">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="de33f-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-470">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="de33f-471">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="de33f-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="de33f-472">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="de33f-472">Scaffold the movie model</span></span>

<span data-ttu-id="de33f-473">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="de33f-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="de33f-474">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="de33f-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-475">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-476">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *controllers* , **> Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="de33f-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Widok powyżej kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="de33f-478">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC z widokami przy użyciu Entity Framework > Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="de33f-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="de33f-480">Ukończ okno dialogowe **Dodawanie kontrolera** :</span><span class="sxs-lookup"><span data-stu-id="de33f-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="de33f-481">**Model Class:** *Movie (MvcMovie. models)*</span><span class="sxs-lookup"><span data-stu-id="de33f-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="de33f-482">**Klasa kontekstu danych:** Wybierz **+** ikonę i Dodaj domyślną **MvcMovie. models. MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="de33f-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Dodaj kontekst danych](adding-model/_static/dc.png)

* <span data-ttu-id="de33f-484">**Widoki:** Zachowaj wartość domyślną dla każdej zaznaczonej opcji</span><span class="sxs-lookup"><span data-stu-id="de33f-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="de33f-485">**Nazwa kontrolera:** Zachowaj domyślną *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="de33f-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="de33f-486">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="de33f-486">Select **Add**</span></span>

![Okno dialogowe Dodawanie kontrolera](adding-model/_static/add_controller2.png)

<span data-ttu-id="de33f-488">Program Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="de33f-488">Visual Studio creates:</span></span>

* <span data-ttu-id="de33f-489">[Klasa kontekstu bazy danych](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core ( *Data/MvcMovieContext. cs* )</span><span class="sxs-lookup"><span data-stu-id="de33f-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="de33f-490">Kontroler filmów ( *controllers/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="de33f-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="de33f-491">Razor Wyświetlanie plików na potrzeby tworzenia, usuwania, szczegółów, edytowania i indeksowania stron ( *widoki/filmy/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="de33f-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="de33f-492">Automatyczne tworzenie kontekstu bazy danych i metod akcji [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (tworzenie, odczytywanie, aktualizowanie i usuwanie) jest znane jako *rusztowanie*.</span><span class="sxs-lookup"><span data-stu-id="de33f-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de33f-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de33f-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="de33f-494">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs* , *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="de33f-495">Zainstaluj narzędzie do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="de33f-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="de33f-496">W systemie Linux wyeksportuj ścieżkę narzędzia do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="de33f-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="de33f-497">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de33f-498">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="de33f-499">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs* , *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="de33f-500">Zainstaluj narzędzie do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="de33f-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="de33f-501">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="de33f-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="de33f-502">Jeśli uruchomisz aplikację i klikniesz link do **filmu MVC** , zostanie wyświetlony komunikat o błędzie podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="de33f-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-503">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-504">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="de33f-505">Należy utworzyć bazę danych i użyć funkcji [migracji](xref:data/ef-mvc/migrations) EF Core, aby to zrobić.</span><span class="sxs-lookup"><span data-stu-id="de33f-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="de33f-506">Migracja umożliwia utworzenie bazy danych zgodnej z modelem danych i zaktualizowanie schematu bazy danych, gdy zmieni się model danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="de33f-507">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="de33f-507">Initial migration</span></span>

<span data-ttu-id="de33f-508">W tej sekcji zostały wykonane następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="de33f-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="de33f-509">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-509">Add an initial migration.</span></span>
* <span data-ttu-id="de33f-510">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-511">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="de33f-512">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="de33f-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="de33f-514">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="de33f-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="de33f-515">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="de33f-516">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie.</span><span class="sxs-lookup"><span data-stu-id="de33f-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="de33f-517">`Initial`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="de33f-518">Można użyć dowolnej nazwy, ale według Konwencji, nazwy opisującej migrację.</span><span class="sxs-lookup"><span data-stu-id="de33f-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="de33f-519">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="de33f-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="de33f-520">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-521">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="de33f-522">`ef migrations add InitialCreate`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="de33f-523">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie (w pliku *Data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="de33f-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="de33f-524">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="de33f-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="de33f-525">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="de33f-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="de33f-526">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="de33f-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="de33f-527">ASP.NET Core jest skompilowany przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="de33f-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="de33f-528">Usługi (takie jak kontekst EF Core DB) są rejestrowane przy użyciu funkcji "DI" podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de33f-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="de33f-529">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="de33f-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="de33f-530">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="de33f-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de33f-531">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de33f-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de33f-532">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu DI kontenera.</span><span class="sxs-lookup"><span data-stu-id="de33f-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="de33f-533">Przeanalizuj poniższą `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="de33f-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="de33f-534">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="de33f-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="de33f-535">`MvcMovieContext`Współrzędne EF Core funkcje (tworzenie, Odczyt, aktualizowanie, usuwanie itp.) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="de33f-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="de33f-536">Kontekst danych ( `MvcMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="de33f-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="de33f-537">Kontekst danych określa, które jednostki są uwzględnione w modelu danych:</span><span class="sxs-lookup"><span data-stu-id="de33f-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="de33f-538">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="de33f-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="de33f-539">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="de33f-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="de33f-540">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="de33f-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="de33f-541">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="de33f-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="de33f-542">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="de33f-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="de33f-543">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="de33f-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="de33f-544">Utworzono kontekst bazy danych i zarejestrowano go przy użyciu DI Container.</span><span class="sxs-lookup"><span data-stu-id="de33f-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="de33f-545">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="de33f-545">Test the app</span></span>

* <span data-ttu-id="de33f-546">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="de33f-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="de33f-547">Jeśli zostanie wyświetlony wyjątek bazy danych podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="de33f-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="de33f-548">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="de33f-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="de33f-549">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="de33f-549">Test the **Create** link.</span></span> <span data-ttu-id="de33f-550">Wprowadź i prześlij dane.</span><span class="sxs-lookup"><span data-stu-id="de33f-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="de33f-551">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="de33f-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="de33f-552">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="de33f-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="de33f-553">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="de33f-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="de33f-554">Przetestuj linki **Edytuj** , **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="de33f-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="de33f-555">Zapoznaj się z `Startup` klasą:</span><span class="sxs-lookup"><span data-stu-id="de33f-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="de33f-556">Poprzedni wyróżniony kod pokazuje kontekst bazy danych filmu dodawany do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="de33f-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="de33f-557">`services.AddDbContext<MvcMovieContext>(options =>` Określa bazę danych, która ma być używana, oraz parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="de33f-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="de33f-558">`=>` jest [operatorem lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="de33f-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="de33f-559">Otwórz plik *controllers/MoviesController. cs* i zapoznaj się z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="de33f-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="de33f-560">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="de33f-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="de33f-561">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="de33f-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="de33f-562">Modele silnie wpisane i @model słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="de33f-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="de33f-563">Wcześniej w tym samouczku pokazano, jak kontroler może przekazać dane lub obiekty do widoku przy użyciu `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="de33f-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="de33f-564">`ViewData`Słownik jest obiektem dynamicznym, który zapewnia wygodny, późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="de33f-565">MVC oferuje również możliwość przekazywania obiektów modelu silnie typu do widoku.</span><span class="sxs-lookup"><span data-stu-id="de33f-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="de33f-566">Takie silnie wpisane podejście umożliwia lepsze sprawdzanie czasu kompilowania kodu.</span><span class="sxs-lookup"><span data-stu-id="de33f-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="de33f-567">Mechanizm tworzenia szkieletów korzysta z tego podejścia (czyli przekazywania modelu silnie określonego typu) z `MoviesController` klasą i widokami, gdy utworzyły metody i widoki.</span><span class="sxs-lookup"><span data-stu-id="de33f-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="de33f-568">Przejrzyj wygenerowaną `Details` metodę w pliku *controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="de33f-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="de33f-569">`id`Parametr jest zazwyczaj przesyłany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="de33f-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="de33f-570">Na przykład `https://localhost:5001/movies/details/1` zestawy:</span><span class="sxs-lookup"><span data-stu-id="de33f-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="de33f-571">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="de33f-572">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="de33f-573">Identyfikator 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="de33f-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="de33f-574">Można również przekazać `id` za pomocą ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="de33f-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="de33f-575">`id`Parametr jest zdefiniowany jako [typ dopuszczający wartość null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) w przypadku, gdy nie podano wartości identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="de33f-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="de33f-576">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przesyłane do, `FirstOrDefaultAsync` Aby wybrać jednostki filmu, które pasują do wartości danych trasy lub ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="de33f-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="de33f-577">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest przesyłane do `Details` widoku:</span><span class="sxs-lookup"><span data-stu-id="de33f-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="de33f-578">Zapoznaj się z zawartością pliku *widoki/filmy/szczegóły. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="de33f-579">Dołączając `@model` instrukcję w górnej części pliku widoku, można określić typ obiektu, którego oczekuje widok.</span><span class="sxs-lookup"><span data-stu-id="de33f-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="de33f-580">Po utworzeniu kontrolera filmu Poniższa `@model` instrukcja została automatycznie uwzględniona w górnej części pliku *details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="de33f-581">Ta `@model` dyrektywa pozwala uzyskać dostęp do filmu, który kontroler przeszedł do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="de33f-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="de33f-582">Na przykład w widoku *details. cshtml* kod przekazuje każde pole filmu do `DisplayNameFor` `DisplayFor` pomocników HTML z obiektem o jednoznacznie określonym typie `Model` .</span><span class="sxs-lookup"><span data-stu-id="de33f-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="de33f-583">`Create`Metody i `Edit` i widoki również przekazują `Movie` obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="de33f-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="de33f-584">Sprawdź widok *index. cshtml* i `Index` metodę w kontrolerze filmów.</span><span class="sxs-lookup"><span data-stu-id="de33f-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="de33f-585">Zwróć uwagę, jak kod tworzy `List` obiekt, gdy wywołuje `View` metodę.</span><span class="sxs-lookup"><span data-stu-id="de33f-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="de33f-586">Kod przekazuje tę `Movies` listę z `Index` metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="de33f-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="de33f-587">Podczas tworzenia kontrolera filmów zostanie automatycznie uwzględniona następująca `@model` instrukcja w górnej części pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="de33f-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="de33f-588">`@model`Dyrektywa pozwala uzyskać dostęp do listy filmów przekazaną przez kontroler do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="de33f-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="de33f-589">Na przykład w widoku *index. cshtml* kod przechodzi przez filmy z `foreach` instrukcją względem obiektu silnie wpisanego `Model` :</span><span class="sxs-lookup"><span data-stu-id="de33f-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="de33f-590">Ponieważ `Model` obiekt jest silnie określony (jako `IEnumerable<Movie>` obiekt), każdy element w pętli jest wpisywany jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="de33f-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="de33f-591">Dzięki temu możesz uzyskać kontrolę czasu kompilowania kodu:</span><span class="sxs-lookup"><span data-stu-id="de33f-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de33f-592">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="de33f-592">Additional resources</span></span>

* [<span data-ttu-id="de33f-593">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="de33f-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="de33f-594">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="de33f-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="de33f-595">[Poprzednie dodanie widoku](adding-view.md) 
>  [Następna praca z bazą danych](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="de33f-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
