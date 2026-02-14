Polecenie Ex
------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Więc chcesz używać Denizen

Jeśli czytasz tę stronę, prawdopodobnie jesteś nowym użytkownikiem Denizen i jesteś gotowy nauczyć się absolutnych podstaw. Jak przeczytałeś na poprzednich stronach, Denizen to *silnik skryptowy*. Oznacza to, że będziesz pisać pliki skryptów pełne kodu, prawda? Cóż, poniekąd. Technicznie rzecz biorąc, *nie musisz* pisać skryptów w oddzielnym pliku. Aby łagodnie wprowadzić Cię w absolutne podstawy Denizen, zaprezentujemy jedną z największych zalet Denizen: możesz po prostu wpisywać polecenia Denizen bezpośrednio w grze i będą one działać, tak po prostu!

### Polecenia Denizen a polecenia serwera

Zanim to zrobimy, należy zrozumieć ważną różnicę: „polecenie Denizen” a „polecenie serwera”.

- **Polecenie serwera** to polecenie, które możesz wpisać na czacie w grze z ukośnikiem `/` na początku. Na przykład `/gamemode creative` to polecenie serwera.
- **Polecenie Denizen** to polecenie, które trafia do skryptu i jest przetwarzane przez silnik skryptowy Denizen. Na przykład `- narrate "cześć <player.name>"` to polecenie Denizen. Zauważ, że jest ono oznaczone znakiem `-` na początku (co jest standardem dla poleceń Denizen) i może wykorzystywać tagi (takie jak `<player.name>`), których polecenia serwera oczywiście nie obsługują.

Ważne jest, aby pamiętać, że nie są one zamienne. Nie możesz wpisać `/narrate "cześć <player.name>"` – serwer wyświetli komunikat „unknown command” (nieznane polecenie). Nie możesz też wpisać `- gamemode creative`, co spowoduje wyświetlenie komunikatu o błędzie skryptu.

### Ale powiedziałeś, że możemy wpisywać polecenia Denizen w grze

Tak, powiedziałem! Co więcej, możesz wpisywać polecenia serwera w skrypcie. Jak? Otóż istnieje polecenie Denizen, które wykonuje polecenie serwera, oraz polecenie serwera, które wykonuje polecenie Denizen.

Jeśli piszesz polecenia w grze i chcesz uruchomić polecenie Denizen, po prostu użyj `/ex`. Na przykład: `/ex narrate "cześć <player.name>"`. Działa to, ponieważ `/ex` to polecenie serwera, które po prostu mówi Denizen, aby przetworzył resztę Twojego wpisu. Będzie to wyglądać mniej więcej tak:
![](images/excommandnarrate.png)

Podobnie, jeśli piszesz polecenia w skrypcie i chcesz uruchomić polecenie serwera, możesz użyć polecenia `- execute`, na przykład `- execute as_op "gamemode creative"` <span class="parens">(możesz również użyć as_player, as_server, a nawet as_npc)</span>. Pamiętaj, że w większości przypadków należy unikać używania polecenia `- execute`, ponieważ prawie wszystko, co możesz zrobić za pomocą polecenia serwera, możesz zrobić lepiej za pomocą polecenia Denizen <span class="parens">(dla przykładu z trybem gry: `- adjust <player> gamemode:creative`)</span>. Jedynym wyjątkiem są zazwyczaj polecenia z innych wtyczek, które chcesz zintegrować ze swoim skryptem.

Specjalna uwaga: pamiętaj, że `/ex` i `- execute` po prostu przełączają z jednego trybu na drugi. Prawie nigdy nie ma uzasadnionego powodu, aby używać obu jednocześnie, ponieważ jest to po prostu przechodzenie tam i z powrotem bez celu. Kiedy się nad tym zastanowić, sam pomysł brzmi zupełnie szalenie... ale zdarzali nam się użytkownicy próbujący rzeczy typu `- execute as_server "ex narrate 'cześć'"`. Oczywiście jest to tylko skomplikowany i nielogiczny sposób na zapisanie `- narrate 'cześć'`.

### Chwila, narracja? Myślałem, że kodujemy, a nie piszemy sztukę teatralną!

Nie martw się, Denizen to rzeczywiście „kodowy” typ skryptu, a nie „teatralny”. `narrate` to pierwsze podstawowe polecenie Denizen, którego warto się nauczyć: wyświetla ono tekst na czacie gracza. <span class="parens">(Kiedy używasz go przez `/ex`, widzisz również informacje debugowania Denizen, ale w rzeczywistym użyciu w skrypcie graczom ukaże się tylko zamierzona linijka tekstu)</span>. To bardzo powszechne podstawowe polecenie i oczywiście główne polecenie do prostych testów. Będzie ono używane w rzeczach takich jak skrypty NPC <span class="parens">(wszystko, co gracz musi przeczytać, a czego NPC mu nie mówi – zostanie użyte narrate!)</span>, własne polecenia serwera <span class="parens">(odpowiedź, gdy gracz uruchomi Twoje własne `/polecenie` serwera, zostanie podana przez narrate)</span> i wiele innych!

### Więc co mam zrobić?

Jeśli jeszcze się nie domyśliłeś: otwórz czat w grze i wpisz `/ex narrate "cześć <player.name>"`. Będzie to powracający motyw w całym tym przewodniku: znajdziesz przykłady rzeczy, które możesz zrobić, oraz opisy tego, co robią i jak działają... nie zawsze będziesz o tym wyraźnie informowany, ale zawsze powinieneś spróbować je wykonać (na swoim lokalnym serwerze testowym!). Gdy przejdziesz do pisania rzeczywistych plików skryptów, pojawią się przykłady specjalnie oznaczone kolorami, wskazujące, czy możesz je po prostu skopiować. Poświęć chwilę, aby otworzyć stronę [Ten przewodnik: Przykładowe skrypty](/guides/this-guide/sample-scripts) w nowej karcie i zapoznać się z systemem oznaczania kolorami, zanim przejdziesz dalej.

Pamiętaj, że zamiast czystego kopiowania i wklejania, powinieneś w miarę możliwości przepisywać skrypty, które testujesz. Pomaga to zagwarantować, że faktycznie przeczytałeś każdy fragment skryptu i daje czas na zastanowienie się i zrozumienie dokładnie tego, czym jest każda część przed jej uruchomieniem – oraz, co ważne, daje Ci lekką wprawę w pisaniu skryptów. Oczywiście będziesz znacznie bardziej gotowy do przetestowania czegoś za pomocą `/ex`, jeśli wcześniej sam wpiszesz kilka poleceń testowych `/ex` (w porównaniu do sytuacji, gdy tylko przeczytałeś o tym i skopiowałeś przykład).

#### Wszystkie te dodatkowe informacje

Powyższy przykład ma na celu jedynie wyświetlenie wiadomości `cześć (twoje imię)`, ale jak widać zarówno na czacie w grze, jak i w konsoli serwera, pojawia się szereg dodatkowych linii. Nazywa się to *wyjściem debugowania* (debug output), które – jak sama nazwa wskazuje – ma pomóc Ci ustalić, co poszło nie tak, jeśli tak się stanie. Będziesz widzieć tego dużo podczas pracy z Denizen, ale nie martw się: kiedy już będziesz mieć gotowe skrypty dla użytkowników, będziesz mógł ukryć te wszystkie informacje przed graczami i w swojej konsoli. Zostanie to wyjaśnione dokładniej kilka stron dalej.

### Powrót do tematu: Co potrafi `/ex`?

Zasadniczo wszystko! Możesz uruchomić dowolne polecenie Denizen za pomocą `/ex`, a polecenia Denizen istnieją praktycznie do wszystkiego. W rzeczywistości jest to tak potężne narzędzie, że muszę zaznaczyć: na serwerach publicznych nie dawaj dostępu do `/ex` nikomu, komu nie dałbyś pełnych uprawnień operatora (opa). Jeśli masz jakiekolwiek wątpliwości co do poziomu tego ograniczenia: pamiętaj, że każdy gracz z dostępem do `/ex` mógłby łatwo wpisać `/ex adjust <player> is_op:true`.

### Co to ma wspólnego ze skryptowaniem?

Polecenia Denizen leżą u podstaw każdego skryptu. Skrypty można opisać jako proste pliki konfiguracyjne zawierające listę poleceń Denizen <span class="parens">(a części konfiguracyjne służą jedynie do wskazania, kiedy dokładnie te polecenia powinny zostać uruchomione)</span>.

`/ex` to naprawdę przydatny sposób na szybkie przetestowanie poszczególnych poleceń w grze. Będziemy z niego często korzystać w kolejnych sekcjach tego przewodnika. To jedno z kilku potężnych narzędzi Denizen, które ułatwiają życie <span class="parens">(i sprawiają, że programiści Java nienawidzą swojego wyboru nauki Javy po zobaczeniu, o ile lepiej jest po stronie Denizen!)</span>.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o poleceniu /ex, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka polecenia Ex](https://meta.denizenscript.com/Docs/Languages/ex%20command)
- [Dokumentacja polecenia Narrate](https://meta.denizenscript.com/Docs/Commands/narrate)
