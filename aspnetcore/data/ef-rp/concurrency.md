---
title: Część 8 Razor strony z EF Core w ASP.NET Core — współbieżność
author: rick-anderson
description: Część 8 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053998"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="bad6d-103">Część 8 Razor strony z EF Core w ASP.NET Core — współbieżność</span><span class="sxs-lookup"><span data-stu-id="bad6d-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="bad6d-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra)i [Jan P Kowalski](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="bad6d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bad6d-105">W tym samouczku pokazano, jak obsłużyć konflikty, gdy wielu użytkowników aktualizuje jednostkę współbieżnie (w tym samym czasie).</span><span class="sxs-lookup"><span data-stu-id="bad6d-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="bad6d-106">Konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="bad6d-106">Concurrency conflicts</span></span>

<span data-ttu-id="bad6d-107">Konflikt współbieżności występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="bad6d-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="bad6d-108">Użytkownik przechodzi do strony Edycja dla jednostki.</span><span class="sxs-lookup"><span data-stu-id="bad6d-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="bad6d-109">Inny użytkownik aktualizuje tę samą jednostkę przed zapisaniem zmiany pierwszego użytkownika w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="bad6d-110">Jeśli wykrywanie współbieżności nie jest włączone, osoba, która aktualizuje bazę danych, ostatnio zastępuje zmiany wprowadzone przez innych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bad6d-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="bad6d-111">Jeśli to ryzyko jest akceptowalne, koszt programowania dla współbieżności może być korzystny.</span><span class="sxs-lookup"><span data-stu-id="bad6d-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="bad6d-112">Współbieżność pesymistyczna (blokowanie)</span><span class="sxs-lookup"><span data-stu-id="bad6d-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="bad6d-113">Jednym ze sposobów zapobiegania konfliktom współbieżności jest użycie blokad bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="bad6d-114">Jest to nazywane pesymistyczną współbieżnością.</span><span class="sxs-lookup"><span data-stu-id="bad6d-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="bad6d-115">Zanim aplikacja odczyta wiersz bazy danych, który zamierza zaktualizować, żąda blokady.</span><span class="sxs-lookup"><span data-stu-id="bad6d-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="bad6d-116">Gdy wiersz jest zablokowany na potrzeby dostępu do aktualizacji, żaden inny użytkownik nie może zablokować wiersza do momentu zwolnienia pierwszej blokady.</span><span class="sxs-lookup"><span data-stu-id="bad6d-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="bad6d-117">Zarządzanie blokadami ma wady.</span><span class="sxs-lookup"><span data-stu-id="bad6d-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="bad6d-118">Może być skomplikowany dla programu i może spowodować problemy z wydajnością w miarę zwiększania się liczby użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bad6d-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="bad6d-119">Entity Framework Core nie zapewnia wbudowanej pomocy technicznej i ten samouczek nie pokazuje, jak wdrożyć go.</span><span class="sxs-lookup"><span data-stu-id="bad6d-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="bad6d-120">Współbieżność optymistyczna</span><span class="sxs-lookup"><span data-stu-id="bad6d-120">Optimistic concurrency</span></span>

<span data-ttu-id="bad6d-121">Optymistyczna współbieżność umożliwia konflikty współbieżności, a następnie ponownie wykonuje odpowiednie działania.</span><span class="sxs-lookup"><span data-stu-id="bad6d-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="bad6d-122">Na przykład Joanna odwiedzi stronę Edycja działu i zmieni budżet działu angielskiego z $350 000,00 na $0,00.</span><span class="sxs-lookup"><span data-stu-id="bad6d-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Zmiana wartości budżetu na 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="bad6d-124">Przed Janem kliknie przycisk **Zapisz**, Jan odwiedzi tę samą stronę i zmieni pole Data rozpoczęcia z 9/1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="bad6d-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Zmiana daty rozpoczęcia na 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="bad6d-126">Janina klika pozycję **Zapisz** pierwszy i widzimy, że zmiany zaczęły obowiązywać, ponieważ przeglądarka wyświetla stronę indeksu z zerem jako kwotą budżetu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="bad6d-127">Jan klika pozycję **Zapisz** na stronie edytowania, która nadal zawiera budżet $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="bad6d-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="bad6d-128">Co się stanie dalej, zależy od sposobu obsługi konfliktów współbieżności:</span><span class="sxs-lookup"><span data-stu-id="bad6d-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="bad6d-129">Można śledzić, która właściwość została zmodyfikowana przez użytkownika i zaktualizować tylko odpowiednie kolumny w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="bad6d-130">W tym scenariuszu żadne dane nie zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="bad6d-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="bad6d-131">Różne właściwości zostały zaktualizowane przez dwóch użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bad6d-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="bad6d-132">Następnym razem, gdy ktoś przegląda dział w języku angielskim, zobaczy zmiany w kategorii Janina i Jan.</span><span class="sxs-lookup"><span data-stu-id="bad6d-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="bad6d-133">Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogłyby spowodować utratę danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="bad6d-134">Takie podejście ma pewne wady:</span><span class="sxs-lookup"><span data-stu-id="bad6d-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="bad6d-135">Nie można uniknąć utraty danych, jeśli wprowadzono konkurencyjne zmiany w tej samej właściwości.</span><span class="sxs-lookup"><span data-stu-id="bad6d-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="bad6d-136">Zwykle nie jest to praktyczne w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="bad6d-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="bad6d-137">Wymaga utrzymywania znaczących stanów w celu śledzenia wszystkich pobranych wartości i nowych wartości.</span><span class="sxs-lookup"><span data-stu-id="bad6d-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="bad6d-138">Utrzymywanie dużej ilości stanu może wpłynąć na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="bad6d-139">Może zwiększyć złożoność aplikacji w porównaniu do wykrywania współbieżności w jednostce.</span><span class="sxs-lookup"><span data-stu-id="bad6d-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="bad6d-140">Możesz pozwolić, aby zmiana została zastąpiona przez Jan.</span><span class="sxs-lookup"><span data-stu-id="bad6d-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="bad6d-141">Następnym razem, gdy ktoś przegląda dział w języku angielskim, zobaczy 9/1/2013 i pobraną wartość $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="bad6d-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="bad6d-142">Ta metoda jest nazywana *klientem WINS* lub *ostatnim scenariuszem usługi WINS* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="bad6d-143">(Wszystkie wartości z klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jeśli nie jest wykonywane żadne kodowanie dla obsługi współbieżności, klient WINS działa automatycznie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="bad6d-144">Można zapobiec aktualizacji firmy Jan ze zmian w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="bad6d-145">Zazwyczaj aplikacja będzie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-145">Typically, the app would:</span></span>

  * <span data-ttu-id="bad6d-146">Wyświetla komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-146">Display an error message.</span></span>
  * <span data-ttu-id="bad6d-147">Pokaż bieżący stan danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="bad6d-148">Zezwalaj użytkownikowi na ponowne stosowanie zmian.</span><span class="sxs-lookup"><span data-stu-id="bad6d-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="bad6d-149">Jest to tzw. scenariusz *magazynu usługi WINS* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="bad6d-150">(Wartości ze sklepu danych mają pierwszeństwo przed wartościami przesyłanymi przez klienta). W tym samouczku zaimplementowano scenariusz magazynu usługi WINS.</span><span class="sxs-lookup"><span data-stu-id="bad6d-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="bad6d-151">Ta metoda zapewnia, że żadne zmiany nie są zastępowane bez alertu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bad6d-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="bad6d-152">Wykrywanie konfliktów w EF Core</span><span class="sxs-lookup"><span data-stu-id="bad6d-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="bad6d-153">EF Core zgłasza `DbConcurrencyException` wyjątki w przypadku wykrycia konfliktów.</span><span class="sxs-lookup"><span data-stu-id="bad6d-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="bad6d-154">Model danych musi być skonfigurowany, aby umożliwić wykrywanie konfliktów.</span><span class="sxs-lookup"><span data-stu-id="bad6d-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="bad6d-155">Opcje włączania wykrywania konfliktów obejmują następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="bad6d-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="bad6d-156">Skonfiguruj EF Core, aby uwzględnić oryginalne wartości kolumn skonfigurowanych jako [tokeny współbieżności](/ef/core/modeling/concurrency) w klauzuli WHERE poleceń Update i DELETE.</span><span class="sxs-lookup"><span data-stu-id="bad6d-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="bad6d-157">Gdy `SaveChanges` jest wywoływana, klauzula WHERE szuka oryginalnych wartości wszelkich właściwości adnotacji z <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> atrybutem.</span><span class="sxs-lookup"><span data-stu-id="bad6d-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="bad6d-158">Instrukcja Update nie znajdzie wiersza do zaktualizowania, jeśli którykolwiek z właściwości tokenu współbieżności został zmieniony od momentu pierwszego odczytu wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="bad6d-159">EF Core interpretuje ten sposób jako konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="bad6d-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="bad6d-160">W przypadku tabel bazy danych z wieloma kolumnami takie podejście może skutkować bardzo dużymi klauzulami WHERE i może wymagać dużej ilości danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="bad6d-161">W związku z tym takie podejście zwykle nie jest zalecane i nie jest to metoda używana w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="bad6d-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="bad6d-162">W tabeli bazy danych Dołącz kolumnę śledzenia, której można użyć do określenia, kiedy wiersz został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="bad6d-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="bad6d-163">W SQL Server bazie danych jest typem danych kolumny śledzenie `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="bad6d-164">`rowversion`Wartość jest kolejnym numerem, który jest zwiększany za każdym razem, gdy wiersz zostanie zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bad6d-165">W przypadku polecenia Update lub DELETE klauzula WHERE zawiera oryginalną wartość kolumny śledzenia (numer wersji pierwszego wiersza).</span><span class="sxs-lookup"><span data-stu-id="bad6d-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="bad6d-166">Jeśli aktualizowany wiersz został zmieniony przez innego użytkownika, wartość w `rowversion` kolumnie jest różna od oryginalnej wartości.</span><span class="sxs-lookup"><span data-stu-id="bad6d-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="bad6d-167">W takim przypadku instrukcja UPDATE lub DELETE nie może znaleźć wiersza do zaktualizowania z powodu klauzuli WHERE.</span><span class="sxs-lookup"><span data-stu-id="bad6d-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="bad6d-168">EF Core zgłasza wyjątek współbieżności, gdy polecenie Update lub DELETE nie ma na nich żadnych wierszy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="bad6d-169">Dodaj właściwość śledzenia</span><span class="sxs-lookup"><span data-stu-id="bad6d-169">Add a tracking property</span></span>

<span data-ttu-id="bad6d-170">W obszarze *modele/dział. cs* Dodaj właściwość śledzenia o nazwie rowversion:</span><span class="sxs-lookup"><span data-stu-id="bad6d-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="bad6d-171">Ten <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> atrybut identyfikuje kolumnę jako kolumnę śledzenia współbieżności.</span><span class="sxs-lookup"><span data-stu-id="bad6d-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="bad6d-172">Interfejs API Fluent jest alternatywnym sposobem określenia właściwości śledzenia:</span><span class="sxs-lookup"><span data-stu-id="bad6d-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="bad6d-173">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bad6d-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bad6d-174">W przypadku bazy danych SQL Server `[Timestamp]` atrybut klasy Entity został zdefiniowany jako tablica bajtowa:</span><span class="sxs-lookup"><span data-stu-id="bad6d-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="bad6d-175">Powoduje, że kolumna powinna zostać uwzględniona w klauzulach DELETE i UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="bad6d-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="bad6d-176">Ustawia typ kolumny w bazie danych na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="bad6d-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="bad6d-177">Baza danych generuje sekwencyjny numer wersji wiersza, który jest zwiększany za każdym razem, gdy wiersz zostanie zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bad6d-178">W `Update` `Delete` poleceniu lub, `Where` klauzula zawiera wartość pobranego wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="bad6d-179">Jeśli aktualizowany wiersz został zmieniony od czasu pobrania:</span><span class="sxs-lookup"><span data-stu-id="bad6d-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="bad6d-180">Wartość bieżącej wersji wiersza nie jest zgodna z pobraną wartością.</span><span class="sxs-lookup"><span data-stu-id="bad6d-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="bad6d-181">`Update`Polecenia lub `Delete` nie szukają wiersza, ponieważ `Where` klauzula szuka wartości wersji wiersza pobrania.</span><span class="sxs-lookup"><span data-stu-id="bad6d-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="bad6d-182">`DbUpdateConcurrencyException`Jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="bad6d-183">Poniższy kod przedstawia część języka T-SQL wygenerowanego przez EF Core w przypadku zaktualizowania nazwy działu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="bad6d-184">Poprzedni wyróżniony kod pokazuje `WHERE` klauzulę zawierającą `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="bad6d-185">Jeśli baza danych `RowVersion` nie jest równa `RowVersion` parametrowi ( `@p2` ), żadne wiersze nie są aktualizowane.</span><span class="sxs-lookup"><span data-stu-id="bad6d-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="bad6d-186">Następujący wyróżniony kod pokazuje T-SQL, który sprawdza dokładnie jeden wiersz został zaktualizowany:</span><span class="sxs-lookup"><span data-stu-id="bad6d-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="bad6d-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Zwraca liczbę wierszy objętych ostatnią instrukcją.</span><span class="sxs-lookup"><span data-stu-id="bad6d-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="bad6d-188">Jeśli żadne wiersze nie są aktualizowane, EF Core zgłasza `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bad6d-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bad6d-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bad6d-190">W przypadku bazy danych programu SQLite `[Timestamp]` atrybut właściwości Entity został zdefiniowany jako tablica bajtowa:</span><span class="sxs-lookup"><span data-stu-id="bad6d-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="bad6d-191">Powoduje, że kolumna powinna zostać uwzględniona w klauzulach DELETE i UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="bad6d-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="bad6d-192">Mapuje do typu kolumny obiektu BLOB.</span><span class="sxs-lookup"><span data-stu-id="bad6d-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="bad6d-193">Wyzwalacze bazy danych aktualizują kolumnę RowVersion za pomocą nowej losowej tablicy bajtów za każdym razem, gdy wiersz zostanie zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="bad6d-194">W `Update` `Delete` poleceniu lub, `Where` klauzula zawiera pobraną wartość kolumny rowversion.</span><span class="sxs-lookup"><span data-stu-id="bad6d-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="bad6d-195">Jeśli aktualizowany wiersz został zmieniony od czasu pobrania:</span><span class="sxs-lookup"><span data-stu-id="bad6d-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="bad6d-196">Wartość bieżącej wersji wiersza nie jest zgodna z pobraną wartością.</span><span class="sxs-lookup"><span data-stu-id="bad6d-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="bad6d-197">`Update`Polecenie lub `Delete` nie znajduje wiersza, ponieważ `Where` klauzula szuka pierwotnej wartości wersji wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="bad6d-198">`DbUpdateConcurrencyException`Jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="bad6d-199">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="bad6d-199">Update the database</span></span>

<span data-ttu-id="bad6d-200">Dodanie `RowVersion` Właściwości zmienia model danych, który wymaga migracji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="bad6d-201">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="bad6d-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="bad6d-202">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bad6d-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bad6d-203">Uruchom następujące polecenie w obszarze PMC:</span><span class="sxs-lookup"><span data-stu-id="bad6d-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bad6d-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bad6d-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bad6d-205">Uruchom następujące polecenie w terminalu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="bad6d-206">To polecenie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-206">This command:</span></span>

* <span data-ttu-id="bad6d-207">Tworzy plik migracji *_RowVersion. cs migracji/sygnatur czasowych}* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="bad6d-208">Aktualizuje plik *migrations/SchoolContextModelSnapshot. cs* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="bad6d-209">Aktualizacja dodaje następujący wyróżniony kod do `BuildModel` metody:</span><span class="sxs-lookup"><span data-stu-id="bad6d-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="bad6d-210">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bad6d-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bad6d-211">Uruchom następujące polecenie w obszarze PMC:</span><span class="sxs-lookup"><span data-stu-id="bad6d-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bad6d-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bad6d-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bad6d-213">Otwórz `Migrations/<timestamp>_RowVersion.cs` plik i Dodaj wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="bad6d-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="bad6d-214">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-214">The preceding code:</span></span>

  * <span data-ttu-id="bad6d-215">Aktualizuje istniejące wiersze z losowymi wartościami obiektów BLOB.</span><span class="sxs-lookup"><span data-stu-id="bad6d-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="bad6d-216">Dodaje wyzwalacze bazy danych, które ustawiają kolumnę RowVersion na losową wartość obiektu BLOB za każdym razem, gdy wiersz zostanie zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="bad6d-217">Uruchom następujące polecenie w terminalu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="bad6d-218">Strony działu szkieletu</span><span class="sxs-lookup"><span data-stu-id="bad6d-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bad6d-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bad6d-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bad6d-220">Postępuj zgodnie z instrukcjami na [stronach uczniów tworzenia szkieletów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="bad6d-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="bad6d-221">Utwórz folder *strony/działy* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="bad6d-222">Użyj `Department` dla klasy model.</span><span class="sxs-lookup"><span data-stu-id="bad6d-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="bad6d-223">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="bad6d-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bad6d-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bad6d-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bad6d-225">Utwórz folder *strony/działy* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="bad6d-226">Uruchom następujące polecenie, aby uzyskać szkielet na stronach działu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="bad6d-227">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="bad6d-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="bad6d-228">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="bad6d-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="bad6d-229">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="bad6d-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="bad6d-230">Aktualizowanie strony indeksu</span><span class="sxs-lookup"><span data-stu-id="bad6d-230">Update the Index page</span></span>

<span data-ttu-id="bad6d-231">Narzędzie tworzenia szkieletów utworzyło `RowVersion` kolumnę dla strony indeks, ale to pole nie będzie wyświetlane w aplikacji produkcyjnej.</span><span class="sxs-lookup"><span data-stu-id="bad6d-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="bad6d-232">W tym samouczku zostanie wyświetlony ostatni bajt, `RowVersion` Aby zobaczyć, jak działa obsługa współbieżności.</span><span class="sxs-lookup"><span data-stu-id="bad6d-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="bad6d-233">Ostatni bajt nie gwarantuje, że jest unikatowy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="bad6d-234">Strona aktualizacji *Pages\Departments\Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bad6d-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="bad6d-235">Zamień indeks na działy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="bad6d-236">Zmień kod zawierający, `RowVersion` Aby wyświetlić tylko ostatni bajt tablicy bajtów.</span><span class="sxs-lookup"><span data-stu-id="bad6d-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="bad6d-237">Zamień FirstMidName na FullName.</span><span class="sxs-lookup"><span data-stu-id="bad6d-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="bad6d-238">Poniższy kod przedstawia zaktualizowaną stronę:</span><span class="sxs-lookup"><span data-stu-id="bad6d-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="bad6d-239">Aktualizowanie modelu strony edycji</span><span class="sxs-lookup"><span data-stu-id="bad6d-239">Update the Edit page model</span></span>

<span data-ttu-id="bad6d-240">Zaktualizuj *Pages\Departments\Edit.cshtml.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="bad6d-241"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue>Zostanie zaktualizowany o `rowVersion` wartość z jednostki, gdy została ona pobrana w `OnGet` metodzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="bad6d-242">EF Core generuje polecenie SQL UPDATE z klauzulą WHERE zawierającą oryginalną `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="bad6d-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="bad6d-243">Jeśli nie ma żadnych wierszy, które ma wpływ na polecenie UPDATE (żadne wiersze nie mają oryginalnej `RowVersion` wartości), `DbUpdateConcurrencyException` zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="bad6d-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="bad6d-244">W poprzednim wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="bad6d-245">Wartość w `Department.RowVersion` to co było w jednostce, gdy została pierwotnie pobrana w żądaniu pobrania dla strony edycji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="bad6d-246">Wartość jest dostarczana do `OnPost` metody przez ukryte pole na Razor stronie, które wyświetla jednostkę do edycji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="bad6d-247">Wartość pola ukrytego jest kopiowana do `Department.RowVersion` przez spinacz modelu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="bad6d-248">`OriginalValue` Czy EF Core będą używane w klauzuli WHERE.</span><span class="sxs-lookup"><span data-stu-id="bad6d-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="bad6d-249">Przed wykonaniem wyróżnionego wiersza kodu program `OriginalValue` ma wartość znajdującą się w bazie danych `FirstOrDefaultAsync` , gdy została wywołana w tej metodzie, która może się różnić od tego, co zostało wyświetlone na stronie Edycja.</span><span class="sxs-lookup"><span data-stu-id="bad6d-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="bad6d-250">Wyróżniony kod sprawdza, czy EF Core używa oryginalnej `RowVersion` wartości z wyświetlonej `Department` jednostki w klauzuli WHERE instrukcji SQL Update.</span><span class="sxs-lookup"><span data-stu-id="bad6d-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="bad6d-251">Gdy wystąpi błąd współbieżności, poniższy wyróżniony kod pobiera wartości klienta (wartości ogłoszone w tej metodzie) oraz wartości bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="bad6d-252">Poniższy kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych różne od tego, co zostało ogłoszone `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="bad6d-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="bad6d-253">Poniższy wyróżniony kod ustawia `RowVersion` wartość nowej wartości pobranej z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="bad6d-254">Następnym razem, gdy użytkownik kliknie przycisk **Zapisz**, zostanie przechwycony tylko błąd współbieżności występujący od momentu ostatniego wyświetlenia strony edycji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="bad6d-255">`ModelState.Remove`Instrukcja jest wymagana, ponieważ `ModelState` ma starą `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="bad6d-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="bad6d-256">Na Razor stronie `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy są obecne oba typy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="bad6d-257">Aktualizowanie strony edytowania</span><span class="sxs-lookup"><span data-stu-id="bad6d-257">Update the Edit page</span></span>

<span data-ttu-id="bad6d-258">Zaktualizuj *strony/działy/Edit. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="bad6d-259">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-259">The preceding code:</span></span>

* <span data-ttu-id="bad6d-260">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bad6d-261">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-261">Adds a hidden row version.</span></span> <span data-ttu-id="bad6d-262">`RowVersion` należy dodać, aby ogłaszać zwrotnie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="bad6d-263">Wyświetla ostatni bajt `RowVersion` do celów debugowania.</span><span class="sxs-lookup"><span data-stu-id="bad6d-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="bad6d-264">Zastępuje `ViewData` silnie wpisaną wartość `InstructorNameSL` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="bad6d-265">Konflikty współbieżności testów ze stroną edycji</span><span class="sxs-lookup"><span data-stu-id="bad6d-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="bad6d-266">Otwórz dwa wystąpienia przeglądarki edycji dla działu angielskiego:</span><span class="sxs-lookup"><span data-stu-id="bad6d-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="bad6d-267">Uruchom aplikację i wybierz pozycję działy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="bad6d-268">Kliknij prawym przyciskiem myszy hiperłącze **Edytuj** dla działu angielskiego i wybierz polecenie **Otwórz na nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bad6d-269">Na pierwszej karcie kliknij hiperłącze **Edytuj** dla działu w języku angielskim.</span><span class="sxs-lookup"><span data-stu-id="bad6d-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="bad6d-270">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="bad6d-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bad6d-271">Zmień nazwę na pierwszej karcie przeglądarki, a następnie kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-271">Change the name in the first browser tab and click **Save**.</span></span>

![Edycja działu Strona 1 po zmianie](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="bad6d-273">W przeglądarce zostanie wyświetlona strona indeks z wartością zmieniona i zaktualizowanym wskaźnikiem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="bad6d-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bad6d-274">Zanotuj zaktualizowany wskaźnik rowVersion, który jest wyświetlany na drugim serwerze ogłaszania zwrotnego na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bad6d-275">Zmień inne pole w drugiej karcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bad6d-275">Change a different field in the second browser tab.</span></span>

![Edycja działu Strona 2 po zmianie](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="bad6d-277">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-277">Click **Save**.</span></span> <span data-ttu-id="bad6d-278">Komunikaty o błędach są wyświetlane dla wszystkich pól, które nie pasują do wartości bazy danych:</span><span class="sxs-lookup"><span data-stu-id="bad6d-278">You see error messages for all fields that don't match the database values:</span></span>

![Komunikat o błędzie strony edytowania działu](concurrency/_static/edit-error30.png)

<span data-ttu-id="bad6d-280">To okno przeglądarki nie zamiało zmiany pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="bad6d-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="bad6d-281">Skopiuj i wklej bieżącą wartość (języki) do pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="bad6d-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="bad6d-282">Na zewnątrz. Walidacja po stronie klienta usuwa komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="bad6d-283">Kliknij przycisk **Zapisz** ponownie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-283">Click **Save** again.</span></span> <span data-ttu-id="bad6d-284">Wartość wprowadzona na drugiej karcie przeglądarki jest zapisywana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="bad6d-285">Zapisane wartości są widoczne na stronie indeks.</span><span class="sxs-lookup"><span data-stu-id="bad6d-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="bad6d-286">Aktualizowanie modelu strony usuwania</span><span class="sxs-lookup"><span data-stu-id="bad6d-286">Update the Delete page model</span></span>

<span data-ttu-id="bad6d-287">Zaktualizuj *strony/działy/Delete. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="bad6d-288">Strona usuwania wykrywa konflikty współbieżności, gdy jednostka została zmieniona po pobraniu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="bad6d-289">`Department.RowVersion` jest to wersja wiersza, gdy jednostka została pobrana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="bad6d-290">Gdy EF Core tworzy polecenie SQL DELETE, zawiera klauzulę WHERE z `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="bad6d-291">Jeśli polecenie SQL DELETE skutkuje niezerowymi wierszami:</span><span class="sxs-lookup"><span data-stu-id="bad6d-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="bad6d-292">`RowVersion`Polecenie w instrukcji SQL Delete nie jest zgodne z `RowVersion` bazą danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="bad6d-293">Zgłaszany jest wyjątek DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="bad6d-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="bad6d-294">`OnGetAsync` jest wywoływana z `concurrencyError` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="bad6d-295">Aktualizowanie strony usuwania</span><span class="sxs-lookup"><span data-stu-id="bad6d-295">Update the Delete page</span></span>

<span data-ttu-id="bad6d-296">Zaktualizuj *strony/działy/Delete. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="bad6d-297">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="bad6d-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="bad6d-298">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bad6d-299">Dodaje komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-299">Adds an error message.</span></span>
* <span data-ttu-id="bad6d-300">Zastępuje FirstMidName z FullName w polu **administrator** .</span><span class="sxs-lookup"><span data-stu-id="bad6d-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="bad6d-301">Zmiany `RowVersion` w celu wyświetlenia ostatniego bajtu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="bad6d-302">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-302">Adds a hidden row version.</span></span> <span data-ttu-id="bad6d-303">`RowVersion` należy dodać, aby ogłaszać zwrotnie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="bad6d-304">Testuj konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="bad6d-304">Test concurrency conflicts</span></span>

<span data-ttu-id="bad6d-305">Tworzenie działu testowego.</span><span class="sxs-lookup"><span data-stu-id="bad6d-305">Create a test department.</span></span>

<span data-ttu-id="bad6d-306">Otwórz dwa wystąpienia przeglądarki usuwania dla działu testowego:</span><span class="sxs-lookup"><span data-stu-id="bad6d-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="bad6d-307">Uruchom aplikację i wybierz pozycję działy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="bad6d-308">Kliknij prawym przyciskiem myszy hiperłącze **Usuń** dla działu testowego i wybierz polecenie **Otwórz na nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bad6d-309">Kliknij hiperłącze **Edytuj** dla działu testowego.</span><span class="sxs-lookup"><span data-stu-id="bad6d-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="bad6d-310">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="bad6d-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bad6d-311">Zmień budżet na pierwszej karcie przeglądarki, a następnie kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="bad6d-312">W przeglądarce zostanie wyświetlona strona indeks z wartością zmieniona i zaktualizowanym wskaźnikiem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="bad6d-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bad6d-313">Zanotuj zaktualizowany wskaźnik rowVersion, który jest wyświetlany na drugim serwerze ogłaszania zwrotnego na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bad6d-314">Usuń dział testów z drugiej karty. Błąd współbieżności jest wyświetlany z bieżącymi wartościami z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="bad6d-315">Kliknięcie przycisku **Usuń** powoduje usunięcie jednostki, o ile `RowVersion` nie została zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bad6d-316">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bad6d-316">Additional resources</span></span>

* [<span data-ttu-id="bad6d-317">Tokeny współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="bad6d-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="bad6d-318">Obsługa współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="bad6d-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="bad6d-319">Debugowanie ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="bad6d-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="bad6d-320">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="bad6d-320">Next steps</span></span>

<span data-ttu-id="bad6d-321">Jest to ostatni samouczek z serii.</span><span class="sxs-lookup"><span data-stu-id="bad6d-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="bad6d-322">Dodatkowe tematy zostały omówione w [wersji MVC tej serii samouczków](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="bad6d-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bad6d-323">Poprzedni samouczek</span><span class="sxs-lookup"><span data-stu-id="bad6d-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bad6d-324">W tym samouczku pokazano, jak obsłużyć konflikty, gdy wielu użytkowników aktualizuje jednostkę współbieżnie (w tym samym czasie).</span><span class="sxs-lookup"><span data-stu-id="bad6d-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="bad6d-325">Jeśli występują problemy, których nie można rozwiązać, [Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="bad6d-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="bad6d-326">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="bad6d-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="bad6d-327">Konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="bad6d-327">Concurrency conflicts</span></span>

<span data-ttu-id="bad6d-328">Konflikt współbieżności występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="bad6d-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="bad6d-329">Użytkownik przechodzi do strony Edycja dla jednostki.</span><span class="sxs-lookup"><span data-stu-id="bad6d-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="bad6d-330">Inny użytkownik aktualizuje tę samą jednostkę przed zapisaniem zmiany pierwszego użytkownika w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="bad6d-331">Jeśli wykrywanie współbieżności nie jest włączone, gdy wystąpią współbieżne aktualizacje:</span><span class="sxs-lookup"><span data-stu-id="bad6d-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="bad6d-332">Ostatnia aktualizacja usługi WINS.</span><span class="sxs-lookup"><span data-stu-id="bad6d-332">The last update wins.</span></span> <span data-ttu-id="bad6d-333">Oznacza to, że ostatnie wartości aktualizacji są zapisywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="bad6d-334">Pierwszy z bieżących aktualizacji zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="bad6d-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="bad6d-335">Współbieżność optymistyczna</span><span class="sxs-lookup"><span data-stu-id="bad6d-335">Optimistic concurrency</span></span>

<span data-ttu-id="bad6d-336">Optymistyczna współbieżność umożliwia konflikty współbieżności, a następnie ponownie wykonuje odpowiednie działania.</span><span class="sxs-lookup"><span data-stu-id="bad6d-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="bad6d-337">Na przykład Joanna odwiedzi stronę Edycja działu i zmieni budżet działu angielskiego z $350 000,00 na $0,00.</span><span class="sxs-lookup"><span data-stu-id="bad6d-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Zmiana wartości budżetu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="bad6d-339">Przed Janem kliknie przycisk **Zapisz**, Jan odwiedzi tę samą stronę i zmieni pole Data rozpoczęcia z 9/1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="bad6d-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Zmiana daty rozpoczęcia na 2013](concurrency/_static/change-date.png)

<span data-ttu-id="bad6d-341">Jan klika pozycję **Zapisz** jako pierwszy i widzi zmiany, gdy przeglądarka wyświetli stronę indeksu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="bad6d-343">Jan klika pozycję **Zapisz** na stronie edytowania, która nadal zawiera budżet $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="bad6d-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="bad6d-344">Co się stanie dalej, zależy od sposobu obsługi konfliktów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="bad6d-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="bad6d-345">Współbieżność optymistyczna obejmuje następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="bad6d-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="bad6d-346">Można śledzić, która właściwość została zmodyfikowana przez użytkownika i zaktualizować tylko odpowiednie kolumny w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="bad6d-347">W tym scenariuszu żadne dane nie zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="bad6d-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="bad6d-348">Różne właściwości zostały zaktualizowane przez dwóch użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bad6d-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="bad6d-349">Następnym razem, gdy ktoś przegląda dział w języku angielskim, zobaczy zmiany w kategorii Janina i Jan.</span><span class="sxs-lookup"><span data-stu-id="bad6d-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="bad6d-350">Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogłyby spowodować utratę danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="bad6d-351">Takie podejście:</span><span class="sxs-lookup"><span data-stu-id="bad6d-351">This approach:</span></span>
 
  * <span data-ttu-id="bad6d-352">Nie można uniknąć utraty danych, jeśli wprowadzono konkurencyjne zmiany w tej samej właściwości.</span><span class="sxs-lookup"><span data-stu-id="bad6d-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="bad6d-353">Zwykle nie jest to praktyczne w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="bad6d-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="bad6d-354">Wymaga utrzymywania znaczących stanów w celu śledzenia wszystkich pobranych wartości i nowych wartości.</span><span class="sxs-lookup"><span data-stu-id="bad6d-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="bad6d-355">Utrzymywanie dużej ilości stanu może wpłynąć na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="bad6d-356">Może zwiększyć złożoność aplikacji w porównaniu do wykrywania współbieżności w jednostce.</span><span class="sxs-lookup"><span data-stu-id="bad6d-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="bad6d-357">Możesz pozwolić, aby zmiana została zastąpiona przez Jan.</span><span class="sxs-lookup"><span data-stu-id="bad6d-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="bad6d-358">Następnym razem, gdy ktoś przegląda dział w języku angielskim, zobaczy 9/1/2013 i pobraną wartość $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="bad6d-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="bad6d-359">Ta metoda jest nazywana *klientem WINS* lub *ostatnim scenariuszem usługi WINS* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="bad6d-360">(Wszystkie wartości z klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jeśli nie jest wykonywane żadne kodowanie dla obsługi współbieżności, klient WINS działa automatycznie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="bad6d-361">Można zapobiec aktualizacji firmy Jan ze zmian w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="bad6d-362">Zazwyczaj aplikacja będzie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-362">Typically, the app would:</span></span>

  * <span data-ttu-id="bad6d-363">Wyświetla komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-363">Display an error message.</span></span>
  * <span data-ttu-id="bad6d-364">Pokaż bieżący stan danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="bad6d-365">Zezwalaj użytkownikowi na ponowne stosowanie zmian.</span><span class="sxs-lookup"><span data-stu-id="bad6d-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="bad6d-366">Jest to tzw. scenariusz *magazynu usługi WINS* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="bad6d-367">(Wartości ze sklepu danych mają pierwszeństwo przed wartościami przesyłanymi przez klienta). W tym samouczku zaimplementowano scenariusz magazynu usługi WINS.</span><span class="sxs-lookup"><span data-stu-id="bad6d-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="bad6d-368">Ta metoda zapewnia, że żadne zmiany nie są zastępowane bez alertu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bad6d-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="bad6d-369">Obsługa współbieżności</span><span class="sxs-lookup"><span data-stu-id="bad6d-369">Handling concurrency</span></span> 

<span data-ttu-id="bad6d-370">Gdy właściwość jest skonfigurowana jako [Token współbieżności](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="bad6d-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="bad6d-371">EF Core sprawdza, czy właściwość nie została zmodyfikowana po pobraniu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="bad6d-372">Sprawdzanie występuje, gdy wywoływana jest [metody SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) lub [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) .</span><span class="sxs-lookup"><span data-stu-id="bad6d-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="bad6d-373">Jeśli właściwość została zmieniona po pobraniu, zgłaszany jest [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) .</span><span class="sxs-lookup"><span data-stu-id="bad6d-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="bad6d-374">Baza danych i model danych muszą być skonfigurowane do obsługi zgłaszania `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="bad6d-375">Wykrywanie konfliktów współbieżności dla właściwości</span><span class="sxs-lookup"><span data-stu-id="bad6d-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="bad6d-376">Konflikty współbieżności mogą być wykrywane na poziomie właściwości przy użyciu atrybutu [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) .</span><span class="sxs-lookup"><span data-stu-id="bad6d-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="bad6d-377">Ten atrybut może być stosowany do wielu właściwości w modelu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="bad6d-378">Aby uzyskać więcej informacji, zobacz [Adnotacje danych — ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="bad6d-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="bad6d-379">`[ConcurrencyCheck]`Ten atrybut nie jest używany w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="bad6d-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="bad6d-380">Wykrywanie konfliktów współbieżności w wierszu</span><span class="sxs-lookup"><span data-stu-id="bad6d-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="bad6d-381">Aby wykrywać konflikty współbieżności, do modelu dodawana jest kolumna śledzenia [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) .</span><span class="sxs-lookup"><span data-stu-id="bad6d-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="bad6d-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="bad6d-382">`rowversion` :</span></span>

* <span data-ttu-id="bad6d-383">SQL Server specyficzny.</span><span class="sxs-lookup"><span data-stu-id="bad6d-383">Is SQL Server specific.</span></span> <span data-ttu-id="bad6d-384">Inne bazy danych mogą nie zapewniać podobnej funkcji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="bad6d-385">Służy do określenia, że jednostka nie została zmieniona od czasu pobrania z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="bad6d-386">Baza danych generuje numer sekwencyjny `rowversion` , który jest zwiększany za każdym razem, gdy wiersz zostanie zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bad6d-387">W `Update` `Delete` poleceniu lub, `Where` klauzula zawiera pobrana wartość `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="bad6d-388">Jeśli aktualizowany wiersz został zmieniony:</span><span class="sxs-lookup"><span data-stu-id="bad6d-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="bad6d-389">`rowversion` nie jest zgodna z pobraną wartością.</span><span class="sxs-lookup"><span data-stu-id="bad6d-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="bad6d-390">`Update`Polecenia lub `Delete` nie znalazły wiersza, ponieważ `Where` klauzula zawiera pobrane `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="bad6d-391">`DbUpdateConcurrencyException`Jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="bad6d-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="bad6d-392">W EF Core, gdy żadne wiersze nie zostały zaktualizowane przez `Update` polecenie lub `Delete` , zgłaszany jest wyjątek współbieżności.</span><span class="sxs-lookup"><span data-stu-id="bad6d-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="bad6d-393">Dodawanie właściwości śledzenia do jednostki działu</span><span class="sxs-lookup"><span data-stu-id="bad6d-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="bad6d-394">W obszarze *modele/dział. cs* Dodaj właściwość śledzenia o nazwie rowversion:</span><span class="sxs-lookup"><span data-stu-id="bad6d-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="bad6d-395">Atrybut [timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) określa, że ta kolumna jest uwzględniona w `Where` klauzuli `Update` i `Delete` poleceń.</span><span class="sxs-lookup"><span data-stu-id="bad6d-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="bad6d-396">Ten atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje SQL Server używały `timestamp` typu danych SQL przed `rowversion` zastąpieniem typu SQL.</span><span class="sxs-lookup"><span data-stu-id="bad6d-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="bad6d-397">Interfejs API Fluent może również określać Właściwość śledzenia:</span><span class="sxs-lookup"><span data-stu-id="bad6d-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="bad6d-398">Poniższy kod przedstawia część języka T-SQL wygenerowanego przez EF Core w przypadku zaktualizowania nazwy działu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="bad6d-399">Poprzedni wyróżniony kod pokazuje `WHERE` klauzulę zawierającą `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="bad6d-400">Jeśli baza danych `RowVersion` nie jest równa `RowVersion` parametrowi ( `@p2` ), żadne wiersze nie są aktualizowane.</span><span class="sxs-lookup"><span data-stu-id="bad6d-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="bad6d-401">Następujący wyróżniony kod pokazuje T-SQL, który sprawdza dokładnie jeden wiersz został zaktualizowany:</span><span class="sxs-lookup"><span data-stu-id="bad6d-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="bad6d-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Zwraca liczbę wierszy objętych ostatnią instrukcją.</span><span class="sxs-lookup"><span data-stu-id="bad6d-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="bad6d-403">Nie zaktualizowano żadnych wierszy, EF Core zgłasza `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="bad6d-404">W oknie danych wyjściowych programu Visual Studio można wyświetlić EF Core T-SQL.</span><span class="sxs-lookup"><span data-stu-id="bad6d-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="bad6d-405">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="bad6d-405">Update the DB</span></span>

<span data-ttu-id="bad6d-406">Dodanie `RowVersion` właściwości powoduje zmianę modelu bazy danych, który wymaga migracji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="bad6d-407">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="bad6d-407">Build the project.</span></span> <span data-ttu-id="bad6d-408">Wprowadź następujące polecenie w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="bad6d-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="bad6d-409">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="bad6d-409">The preceding commands:</span></span>

* <span data-ttu-id="bad6d-410">Dodaje *migrację/{Time sygnaturę} _RowVersion. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="bad6d-411">Aktualizuje plik *migrations/SchoolContextModelSnapshot. cs* .</span><span class="sxs-lookup"><span data-stu-id="bad6d-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="bad6d-412">Aktualizacja dodaje następujący wyróżniony kod do `BuildModel` metody:</span><span class="sxs-lookup"><span data-stu-id="bad6d-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="bad6d-413">Uruchamia migracje, aby zaktualizować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="bad6d-414">Tworzenie szkieletu modelu działów</span><span class="sxs-lookup"><span data-stu-id="bad6d-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bad6d-415">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bad6d-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="bad6d-416">Postępuj zgodnie z instrukcjami w obszarze [szkieletem model ucznia](xref:data/ef-rp/intro#scaffold-student-pages) i Użyj `Department` klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bad6d-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bad6d-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="bad6d-418">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="bad6d-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="bad6d-419">Poprzednie polecenie szkieletuje `Department` model.</span><span class="sxs-lookup"><span data-stu-id="bad6d-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="bad6d-420">Otwórz projekt w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bad6d-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="bad6d-421">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="bad6d-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="bad6d-422">Aktualizowanie strony indeksu działów</span><span class="sxs-lookup"><span data-stu-id="bad6d-422">Update the Departments Index page</span></span>

<span data-ttu-id="bad6d-423">Aparat tworzenia szkieletów utworzył `RowVersion` kolumnę dla strony indeks, ale to pole nie powinno być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="bad6d-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="bad6d-424">W tym samouczku zostanie wyświetlony ostatni bajt elementu, `RowVersion` który pomoże zrozumieć współbieżność.</span><span class="sxs-lookup"><span data-stu-id="bad6d-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="bad6d-425">Ostatni bajt nie gwarantuje unikalności.</span><span class="sxs-lookup"><span data-stu-id="bad6d-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="bad6d-426">Rzeczywista aplikacja nie zostanie wyświetlona `RowVersion` lub ostatni bajt `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="bad6d-427">Aktualizowanie strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-427">Update the Index page:</span></span>

* <span data-ttu-id="bad6d-428">Zamień indeks na działy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="bad6d-429">Zastąp znacznik zawierający `RowVersion` ostatni bajt `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="bad6d-430">Zamień FirstMidName na FullName.</span><span class="sxs-lookup"><span data-stu-id="bad6d-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="bad6d-431">Następujące znaczniki pokazują zaktualizowaną stronę:</span><span class="sxs-lookup"><span data-stu-id="bad6d-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="bad6d-432">Aktualizowanie modelu strony edycji</span><span class="sxs-lookup"><span data-stu-id="bad6d-432">Update the Edit page model</span></span>

<span data-ttu-id="bad6d-433">Zaktualizuj *Pages\Departments\Edit.cshtml.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="bad6d-434">Aby wykryć problem współbieżności, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) zostaje zaktualizowany przy użyciu `rowVersion` wartości z jednostki, która została pobrana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="bad6d-435">EF Core generuje polecenie SQL UPDATE z klauzulą WHERE zawierającą oryginalną `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="bad6d-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="bad6d-436">Jeśli nie ma żadnych wierszy, które ma wpływ na polecenie UPDATE (żadne wiersze nie mają oryginalnej `RowVersion` wartości), `DbUpdateConcurrencyException` zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="bad6d-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="bad6d-437">W poprzednim kodzie `Department.RowVersion` jest wartością, gdy jednostka została pobrana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="bad6d-438">`OriginalValue` jest wartością w bazie danych, gdy `FirstOrDefaultAsync` została wywołana w tej metodzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="bad6d-439">Poniższy kod pobiera wartości klienta (wartości ogłoszone w tej metodzie) i wartości bazy danych:</span><span class="sxs-lookup"><span data-stu-id="bad6d-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="bad6d-440">Poniższy kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych różne od tego, co zostało opublikowane `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="bad6d-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="bad6d-441">Poniższy wyróżniony kod ustawia `RowVersion` wartość nowej wartości pobranej z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="bad6d-442">Następnym razem, gdy użytkownik kliknie przycisk **Zapisz**, zostanie przechwycony tylko błąd współbieżności występujący od momentu ostatniego wyświetlenia strony edycji.</span><span class="sxs-lookup"><span data-stu-id="bad6d-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="bad6d-443">`ModelState.Remove`Instrukcja jest wymagana, ponieważ `ModelState` ma starą `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="bad6d-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="bad6d-444">Na Razor stronie `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy są obecne oba typy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="bad6d-445">Aktualizowanie strony edytowania</span><span class="sxs-lookup"><span data-stu-id="bad6d-445">Update the Edit page</span></span>

<span data-ttu-id="bad6d-446">Aktualizuj *strony/działy/Edit. cshtml* przy użyciu następującego znacznika:</span><span class="sxs-lookup"><span data-stu-id="bad6d-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="bad6d-447">Poprzedzające znaczniki:</span><span class="sxs-lookup"><span data-stu-id="bad6d-447">The preceding markup:</span></span>

* <span data-ttu-id="bad6d-448">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bad6d-449">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-449">Adds a hidden row version.</span></span> <span data-ttu-id="bad6d-450">`RowVersion` należy dodać ten wpis, aby ponownie utworzyć powiązanie z powrotem.</span><span class="sxs-lookup"><span data-stu-id="bad6d-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="bad6d-451">Wyświetla ostatni bajt `RowVersion` do celów debugowania.</span><span class="sxs-lookup"><span data-stu-id="bad6d-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="bad6d-452">Zastępuje `ViewData` silnie wpisaną wartość `InstructorNameSL` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="bad6d-453">Konflikty współbieżności testów ze stroną edycji</span><span class="sxs-lookup"><span data-stu-id="bad6d-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="bad6d-454">Otwórz dwa wystąpienia przeglądarki edycji dla działu angielskiego:</span><span class="sxs-lookup"><span data-stu-id="bad6d-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="bad6d-455">Uruchom aplikację i wybierz pozycję działy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="bad6d-456">Kliknij prawym przyciskiem myszy hiperłącze **Edytuj** dla działu angielskiego i wybierz polecenie **Otwórz na nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bad6d-457">Na pierwszej karcie kliknij hiperłącze **Edytuj** dla działu w języku angielskim.</span><span class="sxs-lookup"><span data-stu-id="bad6d-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="bad6d-458">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="bad6d-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bad6d-459">Zmień nazwę na pierwszej karcie przeglądarki, a następnie kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-459">Change the name in the first browser tab and click **Save**.</span></span>

![Edycja działu Strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="bad6d-461">W przeglądarce zostanie wyświetlona strona indeks z wartością zmieniona i zaktualizowanym wskaźnikiem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="bad6d-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bad6d-462">Zanotuj zaktualizowany wskaźnik rowVersion, który jest wyświetlany na drugim serwerze ogłaszania zwrotnego na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bad6d-463">Zmień inne pole w drugiej karcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bad6d-463">Change a different field in the second browser tab.</span></span>

![Edycja działu Strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="bad6d-465">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-465">Click **Save**.</span></span> <span data-ttu-id="bad6d-466">Komunikaty o błędach są wyświetlane dla wszystkich pól, które nie pasują do wartości bazy danych:</span><span class="sxs-lookup"><span data-stu-id="bad6d-466">You see error messages for all fields that don't match the DB values:</span></span>

![Komunikat o błędzie strony edytowania działu](concurrency/_static/edit-error.png)

<span data-ttu-id="bad6d-468">To okno przeglądarki nie zamiało zmiany pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="bad6d-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="bad6d-469">Skopiuj i wklej bieżącą wartość (języki) do pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="bad6d-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="bad6d-470">Na zewnątrz. Walidacja po stronie klienta usuwa komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-470">Tab out. Client-side validation removes the error message.</span></span>

![Komunikat o błędzie strony edytowania działu](concurrency/_static/cv.png)

<span data-ttu-id="bad6d-472">Kliknij przycisk **Zapisz** ponownie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-472">Click **Save** again.</span></span> <span data-ttu-id="bad6d-473">Wartość wprowadzona na drugiej karcie przeglądarki jest zapisywana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="bad6d-474">Zapisane wartości są widoczne na stronie indeks.</span><span class="sxs-lookup"><span data-stu-id="bad6d-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="bad6d-475">Aktualizowanie strony usuwania</span><span class="sxs-lookup"><span data-stu-id="bad6d-475">Update the Delete page</span></span>

<span data-ttu-id="bad6d-476">Zaktualizuj model usuwania stron przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="bad6d-477">Strona usuwania wykrywa konflikty współbieżności, gdy jednostka została zmieniona po pobraniu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="bad6d-478">`Department.RowVersion` jest to wersja wiersza, gdy jednostka została pobrana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="bad6d-479">Gdy EF Core tworzy polecenie SQL DELETE, zawiera klauzulę WHERE z `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="bad6d-480">Jeśli polecenie SQL DELETE skutkuje niezerowymi wierszami:</span><span class="sxs-lookup"><span data-stu-id="bad6d-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="bad6d-481">`RowVersion`Polecenie w instrukcji SQL Delete nie jest zgodne z `RowVersion` bazą danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="bad6d-482">Zgłaszany jest wyjątek DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="bad6d-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="bad6d-483">`OnGetAsync` jest wywoływana z `concurrencyError` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="bad6d-484">Aktualizowanie strony usuwania</span><span class="sxs-lookup"><span data-stu-id="bad6d-484">Update the Delete page</span></span>

<span data-ttu-id="bad6d-485">Zaktualizuj *strony/działy/Delete. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="bad6d-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="bad6d-486">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="bad6d-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="bad6d-487">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="bad6d-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bad6d-488">Dodaje komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-488">Adds an error message.</span></span>
* <span data-ttu-id="bad6d-489">Zastępuje FirstMidName z FullName w polu **administrator** .</span><span class="sxs-lookup"><span data-stu-id="bad6d-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="bad6d-490">Zmiany `RowVersion` w celu wyświetlenia ostatniego bajtu.</span><span class="sxs-lookup"><span data-stu-id="bad6d-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="bad6d-491">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="bad6d-491">Adds a hidden row version.</span></span> <span data-ttu-id="bad6d-492">`RowVersion` należy dodać ten wpis, aby ponownie utworzyć powiązanie z powrotem.</span><span class="sxs-lookup"><span data-stu-id="bad6d-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="bad6d-493">Konflikty współbieżności testów ze stroną usuwania</span><span class="sxs-lookup"><span data-stu-id="bad6d-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="bad6d-494">Tworzenie działu testowego.</span><span class="sxs-lookup"><span data-stu-id="bad6d-494">Create a test department.</span></span>

<span data-ttu-id="bad6d-495">Otwórz dwa wystąpienia przeglądarki usuwania dla działu testowego:</span><span class="sxs-lookup"><span data-stu-id="bad6d-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="bad6d-496">Uruchom aplikację i wybierz pozycję działy.</span><span class="sxs-lookup"><span data-stu-id="bad6d-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="bad6d-497">Kliknij prawym przyciskiem myszy hiperłącze **Usuń** dla działu testowego i wybierz polecenie **Otwórz na nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bad6d-498">Kliknij hiperłącze **Edytuj** dla działu testowego.</span><span class="sxs-lookup"><span data-stu-id="bad6d-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="bad6d-499">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="bad6d-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bad6d-500">Zmień budżet na pierwszej karcie przeglądarki, a następnie kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bad6d-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="bad6d-501">W przeglądarce zostanie wyświetlona strona indeks z wartością zmieniona i zaktualizowanym wskaźnikiem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="bad6d-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bad6d-502">Zanotuj zaktualizowany wskaźnik rowVersion, który jest wyświetlany na drugim serwerze ogłaszania zwrotnego na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="bad6d-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bad6d-503">Usuń dział testów z drugiej karty. Błąd współbieżności jest wyświetlany z bieżącymi wartościami z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="bad6d-504">Kliknięcie przycisku **Usuń** powoduje usunięcie jednostki, o ile `RowVersion` nie została zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="bad6d-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="bad6d-505">Zobacz [dziedziczenie](xref:data/ef-mvc/inheritance) sposobu dziedziczenia modelu danych.</span><span class="sxs-lookup"><span data-stu-id="bad6d-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="bad6d-506">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bad6d-506">Additional resources</span></span>

* [<span data-ttu-id="bad6d-507">Tokeny współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="bad6d-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="bad6d-508">Obsługa współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="bad6d-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="bad6d-509">Wersja tego samouczka usługi YouTube (obsługa konfliktów współbieżności)</span><span class="sxs-lookup"><span data-stu-id="bad6d-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="bad6d-510">Wersja usługi YouTube w tym samouczku (część 2)</span><span class="sxs-lookup"><span data-stu-id="bad6d-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="bad6d-511">Wersja usługi YouTube w tym samouczku (część 3)</span><span class="sxs-lookup"><span data-stu-id="bad6d-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="bad6d-512">Poprzednie</span><span class="sxs-lookup"><span data-stu-id="bad6d-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

