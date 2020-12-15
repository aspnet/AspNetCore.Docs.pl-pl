Aplikacje serwera Blazor na żywo w pamięci serwera. Oznacza to, że istnieje wiele aplikacji hostowanych w ramach tego samego procesu. W przypadku każdej sesji aplikacji Blazor rozpoczyna obwód z własnym zakresem DI kontenera. Oznacza to, że usługi o określonym zakresie są unikatowe dla sesji Blazor.

> [!WARNING]
> Nie zalecamy stosowania aplikacji w tym samym stanie udostępniania serwera przy użyciu pojedynczych usług, chyba że jest to wyjątkowo ważne, ponieważ może to spowodować powstanie luk w zabezpieczeniach, takich jak przeciek stanu użytkownika w obwodach.

W aplikacjach Blazor można używać stanowych pojedynczych usług, jeśli są one specjalnie przeznaczone do tego celu. Na przykład można użyć pamięci podręcznej pamięci jako pojedynczej, ponieważ wymaga ona klucza w celu uzyskania dostępu do danego wpisu, przy założeniu, że użytkownicy nie mają kontroli nad używanymi kluczami pamięci podręcznej.

**Ponadto ze względów bezpieczeństwa nie można używać <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> w aplikacjach Blazor.** Aplikacje Blazor działają poza kontekstem potoku ASP.NET Core. <xref:Microsoft.AspNetCore.Http.HttpContext>Nie ma gwarancji, że jest ona dostępna w ramach programu <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , ani nie gwarantuje posiadania kontekstu, w którym uruchomiono aplikację Blazor.

Zalecany sposób przekazywania stanu żądania do aplikacji Blazor odbywa się za pomocą parametrów do składnika głównego podczas początkowego renderowania aplikacji:

* Zdefiniuj klasę zawierającą wszystkie dane, które chcesz przekazać do aplikacji Blazor.
* Wypełnij te dane ze strony Razor przy użyciu <xref:Microsoft.AspNetCore.Http.HttpContext> dostępnych w tym czasie.
* Przekaż dane do aplikacji Blazor jako parametr do składnika głównego (aplikacji).
* Zdefiniuj parametr w składniku głównym, aby przechowywać dane przesyłane do aplikacji.
* Korzystanie z danych specyficznych dla użytkownika w aplikacji; lub alternatywnie Skopiuj te dane do usługi w zakresie w programie, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Aby można było jej używać w całej aplikacji.

Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .
