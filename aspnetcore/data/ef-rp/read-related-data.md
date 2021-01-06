---
title: Część 6 Razor strony z EF Core w ASP.NET Core — Odczytaj powiązane dane
author: rick-anderson
description: Część 6 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061070"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="1e0a7-103">Część 6 Razor strony z EF Core w ASP.NET Core — Odczytaj powiązane dane</span><span class="sxs-lookup"><span data-stu-id="1e0a7-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="1e0a7-104">Autorzy [Dykstra](https://github.com/tdykstra), [Jan P Kowalski](https://twitter.com/thereformedprog)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e0a7-105">W tym samouczku pokazano, jak odczytywać i wyświetlać powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="1e0a7-106">Powiązane dane to dane, które EF Core ładowane do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1e0a7-107">Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Strona indeksu kursów](read-related-data/_static/courses-index30.png)

![Strona indeksu instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="1e0a7-110">Eager, jawne i ładowanie z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="1e0a7-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="1e0a7-111">Istnieje kilka sposobów, EF Core mogą ładować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1e0a7-112">[Ładowanie eager](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1e0a7-113">Ładowanie eager jest, gdy zapytanie dla jednego typu jednostki również ładuje powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1e0a7-114">Po odczytaniu jednostki pobierane są powiązane z nią dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1e0a7-115">Zwykle powoduje to pojedyncze zapytanie sprzężenia, które pobiera wszystkie dane, które są zbędne.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1e0a7-116">EF Core będzie wystawiał wiele zapytań dla niektórych typów ładowania eager.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1e0a7-117">Wygenerowanie wielu zapytań może być bardziej wydajne niż bardzo duże pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="1e0a7-118">Ładowanie eager jest określone przy użyciu `Include` `ThenInclude` metod i.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Przykład ładowania eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1e0a7-120">Podczas ładowania eager są wysyłane wiele zapytań, gdy zostanie dołączona Nawigacja kolekcji:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1e0a7-121">Jedno zapytanie dotyczące głównej kwerendy</span><span class="sxs-lookup"><span data-stu-id="1e0a7-121">One query for the main query</span></span> 
  * <span data-ttu-id="1e0a7-122">Jedno zapytanie dla każdej kolekcji "Edge" w drzewie ładowania.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1e0a7-123">Oddziel zapytania z `Load` : dane można pobrać w oddzielnych zapytaniach, a EF Core "naprawia" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1e0a7-124">"Rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1e0a7-125">Osobne zapytania i `Load` są bardziej podobne do jawnego ładowania niż ładowanie eager.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1e0a7-127">**Uwaga:** EF Core automatycznie naprawia właściwości nawigacji do wszystkich innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1e0a7-128">Nawet jeśli dane dla właściwości nawigacji *nie* są jawnie uwzględniane, właściwość można nadal wypełnić, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1e0a7-129">[Jawne ładowanie](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1e0a7-130">Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1e0a7-131">Kod musi być zapisany, aby można było pobrać powiązane dane, gdy jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1e0a7-132">Jawne ładowanie z oddzielnymi zapytania powoduje wysłanie wielu zapytań do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="1e0a7-133">W przypadku jawnego ładowania kod określa właściwości nawigacji do załadowania.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1e0a7-134">Użyj `Load` metody, aby przeprowadzić jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1e0a7-135">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-135">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1e0a7-137">[Ładowanie z opóźnieniem](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1e0a7-138">Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1e0a7-139">Podczas pierwszego uzyskiwania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1e0a7-140">Zapytanie jest wysyłane do bazy danych przy każdym dostępie do właściwości nawigacji po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="1e0a7-141">Ładowanie z opóźnieniem może obniżyć wydajność, na przykład gdy deweloperzy używają wzorców N + 1, ładując element nadrzędny i wyliczający za pomocą elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="1e0a7-142">Tworzenie stron kursu</span><span class="sxs-lookup"><span data-stu-id="1e0a7-142">Create Course pages</span></span>

<span data-ttu-id="1e0a7-143">`Course`Jednostka zawiera właściwość nawigacji, która zawiera powiązaną `Department` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Kurs. Dział](read-related-data/_static/dep-crs.png)

<span data-ttu-id="1e0a7-145">Aby wyświetlić nazwę przypisanego działu dla kursu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="1e0a7-146">Załaduj powiązaną `Department` jednostkę do `Course.Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="1e0a7-147">Pobierz nazwę z `Department` `Name` właściwości jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="1e0a7-148">Strony kursu szkieletowego</span><span class="sxs-lookup"><span data-stu-id="1e0a7-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e0a7-149">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e0a7-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e0a7-150">Postępuj zgodnie z instrukcjami na [stronach uczniów tworzenia szkieletów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1e0a7-151">Utwórz folder *strony/kursy* .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="1e0a7-152">Użyj `Course` dla klasy model.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="1e0a7-153">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e0a7-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e0a7-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e0a7-155">Utwórz folder *strony/kursy* .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="1e0a7-156">Uruchom następujące polecenie, aby połączyć strony kursu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="1e0a7-157">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="1e0a7-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="1e0a7-158">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="1e0a7-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="1e0a7-159">Otwórz *stronę/kursy/index. cshtml. cs* i Przeanalizuj `OnGetAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1e0a7-160">Aparat tworzenia szkieletów określił eager ładowania dla `Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1e0a7-161">`Include`Metoda określa eager ładowania.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="1e0a7-162">Uruchom aplikację i wybierz łącze **kursy** .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1e0a7-163">W kolumnie dział zostanie wyświetlona wartość `DepartmentID` , która nie jest przydatna.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="1e0a7-164">Wyświetl nazwę działu</span><span class="sxs-lookup"><span data-stu-id="1e0a7-164">Display the department name</span></span>

<span data-ttu-id="1e0a7-165">Zaktualizuj strony/kursy/index. cshtml. cs przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="1e0a7-166">Poprzedni kod zmienia `Course` Właściwość na `Courses` i dodaje `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="1e0a7-167">`AsNoTracking` zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1e0a7-168">Nie trzeba śledzić jednostek, ponieważ nie są one aktualizowane w bieżącym kontekście.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1e0a7-169">Zaktualizuj *strony/kursy/index. cshtml* przy użyciu następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="1e0a7-170">W kodzie szkieletowym wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1e0a7-171">Zmieniono `Course` nazwę właściwości na `Courses` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="1e0a7-172">Dodano kolumnę **liczbową** , która wyświetla `CourseID` wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1e0a7-173">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle nie są oznaczane przez użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1e0a7-174">Jednak w tym przypadku klucz podstawowy ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1e0a7-175">Zmieniono kolumnę **działu** , aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1e0a7-176">Kod wyświetla `Name` Właściwość `Department` jednostki, która jest ładowana do `Department` właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1e0a7-177">Uruchom aplikację i wybierz kartę **kursy** , aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona indeksu kursów](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1e0a7-179">Ładowanie powiązanych danych przy użyciu opcji Select</span><span class="sxs-lookup"><span data-stu-id="1e0a7-179">Loading related data with Select</span></span>

<span data-ttu-id="1e0a7-180">`OnGetAsync`Metoda ładuje powiązane dane przy użyciu `Include` metody.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="1e0a7-181">`Select`Metoda jest alternatywą, która ładuje tylko powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="1e0a7-182">Dla pojedynczych elementów, podobnie jak w przypadku `Department.Name` użycia sprzężenia wewnętrznego SQL.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1e0a7-183">W przypadku kolekcji program używa innego dostępu do bazy danych, ale w związku z tym wykonuje `Include` operator dla kolekcji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1e0a7-184">Poniższy kod ładuje powiązane dane przy użyciu `Select` metody:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="1e0a7-185">Poprzedni kod nie zwraca żadnych typów jednostek, dlatego śledzenie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="1e0a7-186">Aby uzyskać więcej informacji na temat śledzenia EF, zobacz [śledzenie a No-Tracking zapytań](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="1e0a7-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1e0a7-188">Zobacz [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) , aby zapoznać się z kompletnym przykładem.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="1e0a7-189">Utwórz strony instruktora</span><span class="sxs-lookup"><span data-stu-id="1e0a7-189">Create Instructor pages</span></span>

<span data-ttu-id="1e0a7-190">Ta sekcja szkieletuje strony instruktorów i dodaje powiązane kursy i rejestracje do strony indeksu instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="1e0a7-191">![Strona indeksu instruktorów](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="1e0a7-192">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1e0a7-193">Lista instruktorów wyświetla powiązane dane z `OfficeAssignment` jednostki (Office na powyższym obrazie).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1e0a7-194">`Instructor`Jednostki i `OfficeAssignment` znajdują się w relacji jeden-do-zero-lub-jednego.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1e0a7-195">Eager ładowania jest używany dla `OfficeAssignment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1e0a7-196">Ładowanie eager jest zwykle wydajniejsze, gdy wymagane jest wyświetlenie powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1e0a7-197">W takim przypadku wyświetlane są przypisania pakietu Office dla instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1e0a7-198">Gdy użytkownik wybierze instruktora, `Course` wyświetlane są powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="1e0a7-199">`Instructor`Jednostki i `Course` znajdują się w relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1e0a7-200">Ładowanie eager jest używane dla `Course` jednostek i ich powiązanych `Department` jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1e0a7-201">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebują tylko kursów dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1e0a7-202">Ten przykład pokazuje, jak używać eager ładowania dla właściwości nawigacji w jednostkach, które są we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1e0a7-203">Gdy użytkownik wybierze kurs, zostaną wyświetlone powiązane dane z `Enrollments` jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1e0a7-204">Na powyższym obrazie wyświetlana jest nazwa ucznia i jej klasy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1e0a7-205">`Course`Jednostki i `Enrollment` znajdują się w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="1e0a7-206">Tworzenie modelu widoku</span><span class="sxs-lookup"><span data-stu-id="1e0a7-206">Create a view model</span></span>

<span data-ttu-id="1e0a7-207">Na stronie instruktorzy są wyświetlane dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1e0a7-208">Wymagany jest model widoku, który zawiera trzy właściwości reprezentujące trzy tabele.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="1e0a7-209">Utwórz *SchoolViewModels/InstructorIndexData. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="1e0a7-210">Strony instruktorów dla szkieletów</span><span class="sxs-lookup"><span data-stu-id="1e0a7-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e0a7-211">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e0a7-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e0a7-212">Postępuj zgodnie z instrukcjami w temacie Tworzenie [szkieletu stron uczniów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1e0a7-213">Utwórz folder *stron/instruktorów* .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="1e0a7-214">Użyj `Instructor` dla klasy model.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="1e0a7-215">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e0a7-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e0a7-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e0a7-217">Utwórz folder *stron/instruktorów* .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="1e0a7-218">Uruchom następujące polecenie, aby połączyć strony instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="1e0a7-219">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="1e0a7-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="1e0a7-220">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="1e0a7-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1e0a7-221">Aby sprawdzić, jak wygląda strona szkieletowa przed aktualizacją, uruchom aplikację i przejdź do strony instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="1e0a7-222">Aktualizowanie *stron/instruktorów/index. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="1e0a7-223">`OnGetAsync`Metoda akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1e0a7-224">Zbadaj zapytanie w pliku */instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="1e0a7-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="1e0a7-225">Kod określa eager ładowania dla następujących właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="1e0a7-226">Zwróć uwagę na powtarzanie `Include` i `ThenInclude` metody dla `CourseAssignments` i `Course` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="1e0a7-227">To powtórzenie jest niezbędne do określenia ładowania eager dla dwóch właściwości nawigacji `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="1e0a7-228">Poniższy kod jest wykonywany po wybraniu instruktora ( `id != null` ).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="1e0a7-229">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1e0a7-230">Właściwość widoku modelu `Courses` jest ładowana z `Course` jednostkami z tej `CourseAssignments` właściwości nawigacji instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="1e0a7-231">`Where`Metoda zwraca kolekcję.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="1e0a7-232">Ale w tym przypadku filtr wybierze pojedynczą jednostkę, więc `Single` Metoda jest wywoływana w celu przekonwertowania kolekcji na jedną `Instructor` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1e0a7-233">`Instructor`Jednostka zapewnia dostęp do `CourseAssignments` właściwości.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1e0a7-234">`CourseAssignments` zapewnia dostęp do powiązanych `Course` jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktora do kursu](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1e0a7-236">`Single`Metoda jest używana w kolekcji, gdy kolekcja zawiera tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1e0a7-237">`Single`Metoda zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1e0a7-238">Alternatywą jest `SingleOrDefault` , która zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="1e0a7-239">Poniższy kod wypełnia właściwość modelu widoku w `Enrollments` przypadku wybrania kursu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1e0a7-240">Aktualizowanie strony indeksu instruktorów</span><span class="sxs-lookup"><span data-stu-id="1e0a7-240">Update the instructors Index page</span></span>

<span data-ttu-id="1e0a7-241">Aktualizowanie *stron/instruktorów/index. cshtml* przy użyciu następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="1e0a7-242">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1e0a7-243">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1e0a7-244">`"{id:int?}"` jest szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1e0a7-245">Szablon trasy zmienia ciągi zapytań liczb całkowitych w adresie URL, aby przesyłać dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1e0a7-246">Na przykład kliknięcie linku **Wybierz** dla instruktora z tylko `@page` dyrektywą spowoduje utworzenie adresu URL w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="1e0a7-247">Gdy dyrektywa Page ma wartość `@page "{id:int?}"` , adres URL to:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="1e0a7-248">Dodaje kolumnę **pakietu Office** , która `item.OfficeAssignment.Location` jest wyświetlana tylko wtedy, gdy `item.OfficeAssignment` nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1e0a7-249">Ponieważ jest to relacja "jeden do zera" lub "jeden", nie może być powiązana jednostka OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1e0a7-250">Dodaje kolumnę **kursów** , która wyświetla nauczanie kursów przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1e0a7-251">Aby uzyskać więcej informacji na temat tej składni Razor, zobacz [jawne przejście liniowe](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="1e0a7-252">Dodaje kod, który dynamicznie dodaje `class="success"` do `tr` elementu wybranego instruktora i kursu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="1e0a7-253">Ustawia kolor tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1e0a7-254">Dodaje nowe hiperłącze z etykietą **SELECT**.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1e0a7-255">Ten link powoduje wysłanie wybranego identyfikatora instruktora do `Index` metody i ustawienie koloru tła.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="1e0a7-256">Dodaje tabelę kursów dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="1e0a7-257">Dodaje tabelę rejestracji uczniów dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="1e0a7-258">Uruchom aplikację i wybierz kartę **Instruktorzy** . Na stronie zostanie wyświetlona strona `Location` (Biuro) z jednostki powiązanej `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1e0a7-259">Jeśli `OfficeAssignment` ma wartość null, zostanie wyświetlona pusta komórka tabeli.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1e0a7-260">Kliknij link **Wybierz** dla instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="1e0a7-261">Zostaną wyświetlone zmiany w stylu wiersza i kursy przypisane do tego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="1e0a7-262">Wybierz kurs, aby zobaczyć listę zarejestrowanych studentów i ich klasy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Wybrany instruktor i kurs dla instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="1e0a7-264">Korzystanie z jednego</span><span class="sxs-lookup"><span data-stu-id="1e0a7-264">Using Single</span></span>

<span data-ttu-id="1e0a7-265">`Single`Metoda może przekazać `Where` warunek zamiast wywołania `Where` metody osobno:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1e0a7-266">Użycie `Single` z warunkiem WHERE jest kwestią preferencji osobistych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="1e0a7-267">Nie zapewnia żadnych korzyści z używania `Where` metody.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1e0a7-268">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="1e0a7-268">Explicit loading</span></span>

<span data-ttu-id="1e0a7-269">Bieżący kod określa eager ładowania dla `Enrollments` i `Students` :</span><span class="sxs-lookup"><span data-stu-id="1e0a7-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="1e0a7-270">Załóżmy, że użytkownicy rzadko chcą widzieć rejestracje w kursie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1e0a7-271">W takim przypadku Optymalizacja będzie ładować dane rejestracji tylko wtedy, gdy jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1e0a7-272">W tej sekcji `OnGetAsync` zostanie zaktualizowany w celu użycia jawnego ładowania `Enrollments` i `Students` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1e0a7-273">Aktualizowanie *stron/instruktorów/index. cshtml. cs* przy użyciu następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="1e0a7-274">Poprzedni kod odrzuca metody *ThenInclude* w celu uzyskania danych dotyczących rejestracji i uczniów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1e0a7-275">W przypadku wybrania kursu jawny kod ładowania pobiera:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="1e0a7-276">`Enrollment`Jednostki wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1e0a7-277">`Student`Jednostki dla każdej z `Enrollment` nich.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1e0a7-278">Zwróć uwagę, że poprzedzający kod komentarz ma wartość `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1e0a7-279">Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1e0a7-280">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-280">Test the app.</span></span> <span data-ttu-id="1e0a7-281">Z perspektywy użytkownika aplikacja zachowuje się identycznie z poprzednią wersją.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1e0a7-282">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="1e0a7-282">Next steps</span></span>

<span data-ttu-id="1e0a7-283">W następnym samouczku pokazano, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="1e0a7-284">[Poprzedni samouczek](xref:data/ef-rp/complex-data-model) 
> [Następny samouczek](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e0a7-285">W tym samouczku dane pokrewne są odczytywane i wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="1e0a7-286">Powiązane dane to dane, które EF Core ładowane do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1e0a7-287">Jeśli występują problemy, których nie można rozwiązać, [Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="1e0a7-288">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="1e0a7-289">Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="1e0a7-292">Eager, jawne i opóźnione ładowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="1e0a7-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="1e0a7-293">Istnieje kilka sposobów, EF Core mogą ładować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1e0a7-294">[Ładowanie eager](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1e0a7-295">Ładowanie eager jest, gdy zapytanie dla jednego typu jednostki również ładuje powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1e0a7-296">Po odczytaniu jednostki są pobierane powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1e0a7-297">Zwykle powoduje to pojedyncze zapytanie sprzężenia, które pobiera wszystkie dane, które są zbędne.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1e0a7-298">EF Core będzie wystawiał wiele zapytań dla niektórych typów ładowania eager.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1e0a7-299">Wygenerowanie wielu zapytań może być bardziej wydajne niż w przypadku niektórych zapytań w EF6, w których wystąpiło pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="1e0a7-300">Ładowanie eager jest określone przy użyciu `Include` `ThenInclude` metod i.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Przykład ładowania eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1e0a7-302">Podczas ładowania eager są wysyłane wiele zapytań, gdy zostanie dołączona Nawigacja kolekcji:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1e0a7-303">Jedno zapytanie dotyczące głównej kwerendy</span><span class="sxs-lookup"><span data-stu-id="1e0a7-303">One query for the main query</span></span> 
  * <span data-ttu-id="1e0a7-304">Jedno zapytanie dla każdej kolekcji "Edge" w drzewie ładowania.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1e0a7-305">Oddziel zapytania z `Load` : dane można pobrać w oddzielnych zapytaniach, a EF Core "naprawia" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1e0a7-306">"rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1e0a7-307">Osobne zapytania i `Load` są bardziej podobne do jawnego ładowania niż ładowanie eager.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1e0a7-309">Uwaga: EF Core automatycznie naprawia właściwości nawigacji do wszystkich innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1e0a7-310">Nawet jeśli dane dla właściwości nawigacji *nie* są jawnie uwzględniane, właściwość można nadal wypełnić, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1e0a7-311">[Jawne ładowanie](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1e0a7-312">Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1e0a7-313">Kod musi być zapisany, aby można było pobrać powiązane dane, gdy jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1e0a7-314">Jawne ładowanie z oddzielnymi zapytaniami powoduje wysłanie wielu zapytań do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="1e0a7-315">W przypadku jawnego ładowania kod określa właściwości nawigacji do załadowania.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1e0a7-316">Użyj `Load` metody, aby przeprowadzić jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1e0a7-317">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-317">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1e0a7-319">[Ładowanie z opóźnieniem](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1e0a7-320">[Ładowanie z opóźnieniem zostało dodane do EF Core w wersji 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1e0a7-321">Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1e0a7-322">Podczas pierwszego uzyskiwania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1e0a7-323">Zapytanie jest wysyłane do bazy danych przy każdym dostępie do właściwości nawigacji po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="1e0a7-324">`Select`Operator ładuje tylko powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="1e0a7-325">Tworzenie strony kursu, która wyświetla nazwę działu</span><span class="sxs-lookup"><span data-stu-id="1e0a7-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="1e0a7-326">Jednostka kursu zawiera właściwość nawigacji, która zawiera `Department` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="1e0a7-327">`Department`Jednostka zawiera dział, do którego jest przypisany kurs.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="1e0a7-328">Aby wyświetlić nazwę przypisanego działu na liście kursów:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="1e0a7-329">Pobierz `Name` Właściwość z `Department` jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="1e0a7-330">`Department`Jednostka pochodzi z `Course.Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Kurs. Dział](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="1e0a7-332">Tworzenie szkieletu modelu kursu</span><span class="sxs-lookup"><span data-stu-id="1e0a7-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e0a7-333">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e0a7-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1e0a7-334">Postępuj zgodnie z instrukcjami w obszarze [szkieletem model ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i Użyj `Course` klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e0a7-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e0a7-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="1e0a7-336">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="1e0a7-337">Poprzednie polecenie szkieletuje `Course` model.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="1e0a7-338">Otwórz projekt w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="1e0a7-339">Otwórz *stronę/kursy/index. cshtml. cs* i Przeanalizuj `OnGetAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1e0a7-340">Aparat tworzenia szkieletów określił eager ładowania dla `Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1e0a7-341">`Include`Metoda określa eager ładowania.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="1e0a7-342">Uruchom aplikację i wybierz łącze **kursy** .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1e0a7-343">W kolumnie dział zostanie wyświetlona wartość `DepartmentID` , która nie jest przydatna.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="1e0a7-344">Zaktualizuj `OnGetAsync` metodę przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="1e0a7-345">Poprzedni kod dodaje `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="1e0a7-346">`AsNoTracking` zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1e0a7-347">Jednostki nie są śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1e0a7-348">Aktualizuj *strony/kursy/index. cshtml* z następującymi wyróżnionymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="1e0a7-349">W kodzie szkieletowym wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1e0a7-350">Zmieniono nagłówek z indeksu na kursy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="1e0a7-351">Dodano kolumnę **liczbową** , która wyświetla `CourseID` wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1e0a7-352">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle nie są oznaczane przez użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1e0a7-353">Jednak w tym przypadku klucz podstawowy ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1e0a7-354">Zmieniono kolumnę **działu** , aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1e0a7-355">Kod wyświetla `Name` Właściwość `Department` jednostki, która jest ładowana do `Department` właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1e0a7-356">Uruchom aplikację i wybierz kartę **kursy** , aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1e0a7-358">Ładowanie powiązanych danych przy użyciu opcji Select</span><span class="sxs-lookup"><span data-stu-id="1e0a7-358">Loading related data with Select</span></span>

<span data-ttu-id="1e0a7-359">`OnGetAsync`Metoda ładuje powiązane dane przy użyciu `Include` metody:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1e0a7-360">`Select`Operator ładuje tylko powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="1e0a7-361">Dla pojedynczych elementów, podobnie jak w przypadku `Department.Name` użycia sprzężenia wewnętrznego SQL.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1e0a7-362">W przypadku kolekcji program używa innego dostępu do bazy danych, ale w związku z tym wykonuje `Include` operator dla kolekcji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1e0a7-363">Poniższy kod ładuje powiązane dane przy użyciu `Select` metody:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1e0a7-364">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1e0a7-365">Zobacz [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) , aby zapoznać się z kompletnym przykładem.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="1e0a7-366">Utwórz stronę instruktorów pokazującą kursy i rejestracje</span><span class="sxs-lookup"><span data-stu-id="1e0a7-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="1e0a7-367">W tej sekcji zostanie utworzona strona instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="1e0a7-368">![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="1e0a7-369">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1e0a7-370">Lista instruktorów wyświetla powiązane dane z `OfficeAssignment` jednostki (Office na powyższym obrazie).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1e0a7-371">`Instructor`Jednostki i `OfficeAssignment` znajdują się w relacji jeden-do-zero-lub-jednego.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1e0a7-372">Eager ładowania jest używany dla `OfficeAssignment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1e0a7-373">Ładowanie eager jest zwykle wydajniejsze, gdy wymagane jest wyświetlenie powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1e0a7-374">W takim przypadku wyświetlane są przypisania pakietu Office dla instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1e0a7-375">Gdy użytkownik wybierze instruktora (Harui na poprzednim obrazie), `Course` zostaną wyświetlone powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="1e0a7-376">`Instructor`Jednostki i `Course` znajdują się w relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1e0a7-377">Ładowanie eager jest używane dla `Course` jednostek i ich powiązanych `Department` jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1e0a7-378">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebują tylko kursów dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1e0a7-379">Ten przykład pokazuje, jak używać eager ładowania dla właściwości nawigacji w jednostkach, które są we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1e0a7-380">Gdy użytkownik wybierze kurs (chemia na powyższym obrazie), zostaną wyświetlone powiązane dane z `Enrollments` jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1e0a7-381">Na powyższym obrazie wyświetlana jest nazwa ucznia i jej klasy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1e0a7-382">`Course`Jednostki i `Enrollment` znajdują się w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="1e0a7-383">Utwórz model widoku dla widoku indeksu instruktora</span><span class="sxs-lookup"><span data-stu-id="1e0a7-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="1e0a7-384">Na stronie instruktorzy są wyświetlane dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1e0a7-385">Tworzony jest model widoku, który zawiera trzy jednostki reprezentujące trzy tabele.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="1e0a7-386">W folderze *SchoolViewModels* Utwórz *InstructorIndexData.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="1e0a7-387">Tworzenie szkieletu modelu instruktora</span><span class="sxs-lookup"><span data-stu-id="1e0a7-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e0a7-388">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e0a7-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1e0a7-389">Postępuj zgodnie z instrukcjami w obszarze [szkieletem model ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i Użyj `Instructor` klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e0a7-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e0a7-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="1e0a7-391">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1e0a7-392">Poprzednie polecenie szkieletuje `Instructor` model.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="1e0a7-393">Uruchom aplikację i przejdź do strony instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="1e0a7-394">Zamień *strony/instruktorów/index. cshtml. cs* na następujący kod:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="1e0a7-395">`OnGetAsync`Metoda akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1e0a7-396">Zbadaj zapytanie w pliku */instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="1e0a7-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1e0a7-397">Zapytanie zawiera dwa:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-397">The query has two includes:</span></span>

* <span data-ttu-id="1e0a7-398">`OfficeAssignment`: Wyświetlane w [widoku instruktorów](#IP).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="1e0a7-399">`CourseAssignments`: Co to jest w nauczaniu kursów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1e0a7-400">Aktualizowanie strony indeksu instruktorów</span><span class="sxs-lookup"><span data-stu-id="1e0a7-400">Update the instructors Index page</span></span>

<span data-ttu-id="1e0a7-401">Aktualizowanie *stron/instruktorów/index. cshtml* przy użyciu następującego znacznika:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="1e0a7-402">Poprzedzające znaczniki wprowadzają następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="1e0a7-403">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1e0a7-404">`"{id:int?}"` jest szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1e0a7-405">Szablon trasy zmienia ciągi zapytań liczb całkowitych w adresie URL, aby przesyłać dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1e0a7-406">Na przykład kliknięcie linku **Wybierz** dla instruktora z tylko `@page` dyrektywą spowoduje utworzenie adresu URL w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="1e0a7-407">Gdy dyrektywa Page ma wartość `@page "{id:int?}"` , poprzedni adres URL to:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="1e0a7-408">Tytuł strony to **Instruktorzy**.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="1e0a7-409">Dodano kolumnę **pakietu Office** , która `item.OfficeAssignment.Location` jest wyświetlana tylko wtedy, gdy `item.OfficeAssignment` nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1e0a7-410">Ponieważ jest to relacja "jeden do zera" lub "jeden", nie może być powiązana jednostka OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1e0a7-411">Dodano kolumnę **kursów** , która wyświetla nauczanie kursów przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1e0a7-412">Aby uzyskać więcej informacji na temat tej składni Razor, zobacz [jawne przejście liniowe](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="1e0a7-413">Dodano kod, który dynamicznie dodaje `class="success"` do `tr` elementu wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="1e0a7-414">Ustawia kolor tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1e0a7-415">Dodano nowe hiperłącze z etykietą **SELECT**.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1e0a7-416">Ten link powoduje wysłanie wybranego identyfikatora instruktora do `Index` metody i ustawienie koloru tła.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="1e0a7-417">Uruchom aplikację i wybierz kartę **Instruktorzy** . Na stronie zostanie wyświetlona strona `Location` (Biuro) z jednostki powiązanej `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1e0a7-418">Jeśli OfficeAssignment ' ma wartość null, zostanie wyświetlona pusta komórka tabeli.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1e0a7-419">Kliknij link **Wybierz** .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-419">Click on the **Select** link.</span></span> <span data-ttu-id="1e0a7-420">Styl wiersza zmienia się.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="1e0a7-421">Dodaj nauczanie kursów według wybranych instruktorów</span><span class="sxs-lookup"><span data-stu-id="1e0a7-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="1e0a7-422">Zaktualizuj `OnGetAsync` metodę na *stronach/instruktorów/index. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="1e0a7-423">Dodana `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="1e0a7-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="1e0a7-424">Zbadaj zaktualizowane zapytanie:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1e0a7-425">Poprzednie zapytanie dodaje `Department` jednostki.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="1e0a7-426">Poniższy kod jest wykonywany po wybraniu instruktora ( `id != null` ).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="1e0a7-427">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1e0a7-428">Właściwość widoku modelu `Courses` jest ładowana z `Course` jednostkami z tej `CourseAssignments` właściwości nawigacji instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="1e0a7-429">`Where`Metoda zwraca kolekcję.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="1e0a7-430">W poprzedniej `Where` metodzie zwracana jest tylko pojedyncza `Instructor` jednostka.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="1e0a7-431">`Single`Metoda konwertuje kolekcję na jedną `Instructor` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1e0a7-432">`Instructor`Jednostka zapewnia dostęp do `CourseAssignments` właściwości.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1e0a7-433">`CourseAssignments` zapewnia dostęp do powiązanych `Course` jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktora do kursu](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1e0a7-435">`Single`Metoda jest używana w kolekcji, gdy kolekcja zawiera tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1e0a7-436">`Single`Metoda zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1e0a7-437">Alternatywą jest `SingleOrDefault` , która zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="1e0a7-438">Używanie `SingleOrDefault` w pustej kolekcji:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="1e0a7-439">Wynikiem jest wyjątek (od próby znalezienia `Courses` właściwości w odwołaniu o wartości null).</span><span class="sxs-lookup"><span data-stu-id="1e0a7-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="1e0a7-440">Komunikat o wyjątku będzie mniej jasno wskazywał przyczynę problemu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="1e0a7-441">Poniższy kod wypełnia właściwość modelu widoku w `Enrollments` przypadku wybrania kursu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="1e0a7-442">Dodaj następujący znacznik na końcu strony */instruktorów/index. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="1e0a7-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="1e0a7-443">Powyższy znacznik wyświetla listę kursów związanych z instruktorem w przypadku wybrania instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="1e0a7-444">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-444">Test the app.</span></span> <span data-ttu-id="1e0a7-445">Kliknij link **Wybierz** na stronie instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="1e0a7-446">Pokaż dane ucznia</span><span class="sxs-lookup"><span data-stu-id="1e0a7-446">Show student data</span></span>

<span data-ttu-id="1e0a7-447">W tej sekcji aplikacja zostanie zaktualizowana, aby wyświetlić dane uczniów dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="1e0a7-448">Zaktualizuj zapytanie w `OnGetAsync` metodzie na *stronach/instruktorów/index. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1e0a7-449">Aktualizowanie *stron/instruktorów/index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="1e0a7-450">Dodaj następujący znacznik na końcu pliku:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="1e0a7-451">Powyższy znacznik wyświetla listę studentów, którzy są zarejestrowani w wybranym kursie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="1e0a7-452">Odśwież stronę i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="1e0a7-453">Wybierz kurs, aby zobaczyć listę zarejestrowanych studentów i ich klasy.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Wybrany instruktor i kurs dla instruktorów](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="1e0a7-455">Korzystanie z jednego</span><span class="sxs-lookup"><span data-stu-id="1e0a7-455">Using Single</span></span>

<span data-ttu-id="1e0a7-456">`Single`Metoda może przekazać `Where` warunek zamiast wywołania `Where` metody osobno:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1e0a7-457">Poprzednie `Single` podejście nie zapewnia żadnych korzyści w porównaniu z korzystaniem z programu `Where` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="1e0a7-458">Niektórzy deweloperzy preferują `Single` styl podejścia.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1e0a7-459">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="1e0a7-459">Explicit loading</span></span>

<span data-ttu-id="1e0a7-460">Bieżący kod określa eager ładowania dla `Enrollments` i `Students` :</span><span class="sxs-lookup"><span data-stu-id="1e0a7-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1e0a7-461">Załóżmy, że użytkownicy rzadko chcą widzieć rejestracje w kursie.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1e0a7-462">W takim przypadku Optymalizacja będzie ładować dane rejestracji tylko wtedy, gdy jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1e0a7-463">W tej sekcji `OnGetAsync` zostanie zaktualizowany w celu użycia jawnego ładowania `Enrollments` i `Students` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1e0a7-464">Zaktualizuj `OnGetAsync` program przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="1e0a7-465">Poprzedni kod odrzuca metody *ThenInclude* w celu uzyskania danych dotyczących rejestracji i uczniów.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1e0a7-466">W przypadku wybrania kursu wyróżniony kod pobiera:</span><span class="sxs-lookup"><span data-stu-id="1e0a7-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="1e0a7-467">`Enrollment`Jednostki wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1e0a7-468">`Student`Jednostki dla każdej z `Enrollment` nich.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1e0a7-469">Zwróć uwagę na powyższe komentarze w kodzie `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="1e0a7-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1e0a7-470">Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1e0a7-471">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-471">Test the app.</span></span> <span data-ttu-id="1e0a7-472">Z perspektywy użytkowników aplikacja zachowuje się identycznie z poprzednią wersją.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="1e0a7-473">W następnym samouczku pokazano, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1e0a7-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e0a7-474">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1e0a7-474">Additional resources</span></span>

* [<span data-ttu-id="1e0a7-475">Wersja tego samouczka usługi YouTube (part1)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="1e0a7-476">Wersja tego samouczka usługi YouTube (part2)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="1e0a7-477">[Poprzedni](xref:data/ef-rp/complex-data-model) 
> [Dalej](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="1e0a7-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
