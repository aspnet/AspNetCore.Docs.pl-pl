> [!NOTE]
> 
> <span data-ttu-id="ec20b-101">**Ograniczenia oprogramowania SQLite**</span><span class="sxs-lookup"><span data-stu-id="ec20b-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="ec20b-102">Ten samouczek używa funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="ec20b-102">This tutorial uses the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="ec20b-103">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="ec20b-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="ec20b-104">Jednak tylko te zmiany są obsługiwane przez aparat bazy danych, a możliwości zmiany schematu oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="ec20b-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="ec20b-105">Na przykład, Dodawanie kolumny jest obsługiwane, ale usuwanie kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ec20b-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="ec20b-106">Jeśli migracja zostanie utworzona w celu usunięcia kolumny, `ef migrations add` polecenie zakończy się pomyślnie, `ef database update` ale polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="ec20b-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="ec20b-107">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="ec20b-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="ec20b-108">Kod będzie `Up` zawierać metody i `Down` dla migracji i będzie obejmował:</span><span class="sxs-lookup"><span data-stu-id="ec20b-108">The code would go in the `Up` and `Down` methods for a migration and would involve:</span></span>
>
> * <span data-ttu-id="ec20b-109">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="ec20b-109">Creating a new table.</span></span>
> * <span data-ttu-id="ec20b-110">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="ec20b-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="ec20b-111">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="ec20b-111">Dropping the old table.</span></span>
> * <span data-ttu-id="ec20b-112">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="ec20b-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="ec20b-113">Pisanie kodu specyficznego dla bazy danych tego typu jest poza zakresem tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="ec20b-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="ec20b-114">Zamiast tego ten samouczek opuszcza i ponownie tworzy bazę danych za każdym razem, gdy próba zastosowania migracji zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="ec20b-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="ec20b-115">Aby uzyskać więcej informacji, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="ec20b-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="ec20b-116">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="ec20b-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="ec20b-117">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="ec20b-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="ec20b-118">Rozmieszczanie danych</span><span class="sxs-lookup"><span data-stu-id="ec20b-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="ec20b-119">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="ec20b-119">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)