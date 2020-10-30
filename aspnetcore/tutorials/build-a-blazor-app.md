---
title: Tworzenie aplikacji z listą zadań do wykonania Blazor
author: guardrex
description: Kompiluj Blazor aplikację krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 68a38b82f5a89365e4f345a60f1f34b697c027ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060095"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Tworzenie aplikacji z listą zadań do wykonania Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor
> * Modyfikuj Razor składniki
> * Używanie obsługi zdarzeń i powiązania danych w składnikach
> * Używanie routingu w Blazor aplikacji

Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a>Tworzenie aplikacji z listą zadań do zrobienia Blazor

1. Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Poprzednie polecenie tworzy folder o nazwie `TodoList` do przechowywania aplikacji. `TodoList`Folder jest *folderem głównym* projektu. Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:

   ```dotnetcli
   cd TodoList
   ```

1. Dodaj nowy `Todo` Razor składnik do aplikacji w `Pages` folderze przy użyciu następującego polecenia:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor nazwy plików składników wymagają wielkich liter. Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` . Nazwa pliku powinna być `Todo.razor` .

1. W polu `Pages/Todo.razor` Podaj początkowe znaczniki dla składnika:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Dodaj `Todo` składnik do paska nawigacyjnego.

   `NavMenu`Składnik ( `Shared/NavMenu.razor` ) jest używany w układzie aplikacji. Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.

   Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w `Shared/NavMenu.razor` pliku:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Skompiluj i uruchom aplikację, wykonując `dotnet run` polecenie w powłoce poleceń z `TodoList` folderu. Odwiedź stronę nowych zadań do wykonania, aby upewnić się, że łącze do `Todo` składnika działa.

1. Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania. Użyj następującego kodu w języku C# dla `TodoItem` klasy:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Wróć do `Todo` składnika ( `Pages/Todo.razor` ):

   * Dodaj pole do zadań do wykonania w `@code` bloku. `Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.
   * Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania. Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Zatrzymaj uruchomioną aplikację w powłoce poleceń. Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację. Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia. Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest połączony z przyciskiem.

1. Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu. `AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Aby uzyskać tytuł nowego elementu do wykonania, należy dodać `newTodo` pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy. Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Zatrzymaj uruchomioną aplikację w powłoce poleceń. Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia. Dodaj do listy zadań do zrobienia elementy do wykonania, aby przetestować nowy kod.

1. Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone. Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością. Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Ukończony `Todo` składnik ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Zatrzymaj uruchomioną aplikację w powłoce poleceń. Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia. Dodaj elementy do wykonania, aby przetestować nowy kod.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor
> * Modyfikuj Razor składniki
> * Używanie obsługi zdarzeń i powiązania danych w składnikach
> * Używanie routingu w Blazor aplikacji

Informacje o narzędziach dla ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
