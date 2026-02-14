Aktualizacje od czasu filmów instruktażowych
---------------------------------------------

Denizen przeszedł wiele aktualizacji od czasu opublikowania oryginalnych filmów instruktażowych <span class="parens">(5-6 lat temu)</span>! Ta strona dokumentuje zmiany, o których powinieneś wiedzieć, aby stosować najlepsze praktyki.

```eval_rst
.. contents:: Spis treści
    :local:
```

### Wybór edytora

W filmach instruktażowych jako edytor skryptów prezentowany był Notepad++. Był to zasadniczo tylko nieco lepszy edytor tekstowy. Od tego czasu zyskaliśmy prawdziwy, dedykowany edytor skryptów, oparty na rozszerzeniu do VS Code.

Więcej informacji znajdziesz na stronie [Konfiguracja edytora skryptów](/guides/first-steps/script-editor).

Pamiętaj, że w związku z tym nie używa się już rozszerzenia `.yml` dla skryptów. Zamiast tego używaj `.dsc`.

### Jesteśmy teraz na Discordzie

Filmy instruktażowe pokazują interakcje z botem na IRC – jeśli próbowałeś się tam połączyć, widziałeś już wiadomości o przenosinach na Discorda. Prawdopodobnie wiesz o tym również, jeśli trafiłeś do tego przewodnika.

Ale na wszelki wypadek, gdybyś jeszcze o tym nie wiedział – jesteśmy na Discordzie, więc [dołącz do nas!](https://discord.gg/Q6pZGSR). Zapewniamy tam wsparcie ludzi oraz boty pomagające w przeszukiwaniu dokumentacji, sprawdzaniu skryptów itp.

### Składnia z dwukropkiem (Colon Syntax)

Składnia z klamrami (braced syntax) została zastąpiona składnią z dwukropkiem. Jak udokumentowano na stronie [Polecenie If](/guides/basics/if-command), składnia z dwukropkiem pozwala na użycie wielu poleceń wewnątrz poleceń takich jak `if` czy `foreach`. W przeciwieństwie do składni z klamrami, nie jest potrzebny żaden znak zamykający – gdy wrócisz do poprzedniego poziomu wcięcia, skrypt będzie toczył się normalnie dalej.

Jeśli napisałeś skrypty używając klamer, możesz je szybko przekonwertować na składnię z dwukropkiem, usuwając wszystkie klamry zamykające (`}`), zastępując klamry otwierające (`{`) dwukropkiem i usuwając ewentualne zbędne spacje między dwukropkiem a ostatnim znakiem w linii.

Oto przykład skryptu przekonwertowanego ze składni klamrowej na dwukropkową:

```dscript_red
old_brace_syntax:
    type: task
    script:
    - if <player.has_flag[test]> {
        - narrate "To jest przykład składni klamrowej!"
        }
    - narrate "Ta narracja zawsze się uruchomi!"
```

```dscript_green
new_colon_syntax:
    type: task
    script:
    - if <player.has_flag[test]>:
        - narrate "To jest przykład składni z dwukropkiem!"
    - narrate "Ta narracja zawsze się uruchomi!"
```

Składnia z dwukropkiem jest łatwiejsza do pisania, wygląda czystziej, a jako dodatkowy bonus, Denizen przetwarza ją wydajniej!

### Składnia definicji

Polecenie `define` przeszło dwie istotne zmiany – po pierwsze, zmieniła się składnia samego tagu definicji, a po drugie, polecenie define wspiera teraz [akcje na danych (data actions)](https://meta.denizenscript.com/Docs/Languages/data%20actions).

Filmy uczyły dwóch różnych form składni definicji: po pierwsze, „antycznego stylu” z procentami (np. `%this%`), a po drugie, „starego stylu” z tagami (np. `<def[this]>`). Tag definicji nie wygląda już w ten sposób, lecz ma postać `<[this]>` <span class="parens">(dla definicji o nazwie `this`)</span>. Dodatkowe szczegóły na temat używania i modyfikowania definicji znajdziesz na stronie [Definicje](/guides/basics/definitions).

Zauważ, że zapis `%nazwa%` jest uważany za antyczny i całkowicie niewspierany – nigdy go nie używaj. Zapis `<def[nazwa]>` jest natomiast traktowany jako starsza/alternatywna składnia dla nowoczesnego `<[nazwa]>` <span class="parens">(jest to ta sama składnia bazowa tagu, tylko tag bez nazwy jest teraz dostępny w miejsce tagu o nazwie `def`)</span>.

Oto przykład starej składni zaktualizowanej do nowej:

```dscript_red
old_definition_syntax:
    type: task
    script:
    - define name <player.name>
    # Antyczne
    - narrate "Witaj, %name%!"
    # Stare
    - narrate "Witaj, <def[name]>!"
```

```dscript_green
new_definition_syntax:
    type: task
    script:
    - define name <player.name>
    - narrate "Witaj, <[name]>!"
```

### Zmiany w poleceniu While

W przeszłości polecenie `while` przyjmowało tylko jeden argument, więc konieczne było używanie tagów takich jak `<player.health.is[>=].to[10]>]>` do ewaluacji wyrażeń.

To ograniczenie już nie istnieje – polecenie `while` wspiera teraz operatory, tak samo jak polecenie `if`.

Poniżej znajduje się przykład skryptu zadania używającego polecenia `while`, który będzie czekać, aż zdrowie gracza spadnie poniżej 10, a następnie wyświetli mu ostrzeżenie – w wersji starej i nowoczesnej:

```dscript_red
old_while_syntax:
    type: task
    script:
    - narrate "Wyzwanie: nie trać zbyt dużo zdrowia!"
    - while <player.health.is[>=].to[10]>:
        - wait 1s
    - narrate "Twoje zdrowie jest zbyt niskie! Przegrywasz!"
```

```dscript_green
new_while_syntax:
    type: task
    script:
    - narrate "Wyzwanie: nie trać zbyt dużo zdrowia!"
    - while <player.health> >= 10:
        - wait 1s
    - narrate "Twoje zdrowie jest zbyt niskie! Przegrywasz!"
```

Zauważ, że ten konkretny styl logiki typu „czekaj-aż-coś-się-stanie” został zastąpiony przez wyspecjalizowane polecenie `waituntil`, którego można użyć w ten sposób:

```dscript_green
waituntil_example:
    type: task
    script:
    - narrate "Wyzwanie: nie trać zbyt dużo zdrowia!"
    - waituntil rate:1s <player.health> < 10
    - narrate "Twoje zdrowie jest zbyt niskie! Przegrywasz!"
```

### Aktualizacje skryptów przypisania (Assignment Scripts)

Historycznie na NPC używano wielu skryptów interakcji (interact scripts) wraz z warunkami określającymi, który skrypt ma się uruchomić. Denizen posiada teraz kroki (steps) w skryptach interakcji oraz polecenie `zap`, więc potrzebny (i wspierany) jest tylko jeden skrypt interakcji. Liczby obok wpisu interact scripts w skrypcie przypisania nie są już potrzebne i powinny zostać usunięte.

Oto przykłady starej i zaktualizowanej składni:

```dscript_red
old_assignment_script:
    type: assignment
    interact scripts:
    - 10 moja_fajna_interakcja_npc
```

```dscript_green
new_assignment_script:
    type: assignment
    interact scripts:
    - moja_fajna_interakcja_npc
```

### Stop to nowe Queue Clear

Polecenie `queue clear` było niegdyś używane do zatrzymania działającej kolejki. Polecenie to zostało zaktualizowane do `stop`.

Zamiana jest prosta:

```dscript_red
old_queue_clear:
    type: task
    script:
    - if <player.has_flag[buff]>:
        - queue clear
    - narrate "Nie posiadasz wymaganego wzmocnienia!"
```

```dscript_green
new_stop:
    type: task
    script:
    - if <player.has_flag[buff]>:
        - stop
    - narrate "Nie posiadasz wymaganego wzmocnienia!"
```

### Używaj .has_flag[] do sprawdzania istnienia flagi

W przeszłości, również w filmie o [Zadaniu typu Kill Quest](/guides/put-it-together/kill-quest), tag `.flag[]` był używany zarówno do sprawdzania wartości flagi, jak i tego, czy flaga w ogóle istnieje.

Obecnie poprawnym sposobem sprawdzenia istnienia flagi jest użycie `.has_flag[]`. Tag `.flag[]` służy teraz wyłącznie do odczytu wartości flagi i powinien być używany tylko tam, gdzie mamy pewność, że flaga istnieje <span class="parens">(lub z użyciem fallbacka)</span>. Oto przykład:

```dscript_red
old_flag:
    type: task
    script:
    - if <player.flag[VIP]>:
        - narrate "Twój poziom VIP to <player.flag[VIP]>!"
```

```dscript_green
new_has_flag:
    type: task
    script:
    - if <player.has_flag[VIP]>:
        - narrate "Twój poziom VIP to <player.flag[VIP]>!"
```

### Przepisanie systemu flag

Oprócz tagu `has_flag`, w późniejszym czasie nastąpiło całkowite wewnętrzne przepisanie systemu flag (grudzień 2020).

Najistotniejszą zmianą względem filmów w tym zakresie jest to, że tag `<player.flag[NAZWA].expiration>` widoczny w filmie o kill queście powinien zostać zmieniony na `<player.flag_expiration[NAZWA].from_now>`.

Ze względu na znaczące ogólne zmiany w systemie flag, należy korzystać z nowszej dokumentacji niż stare filmy – sprawdź stronę [Flagi](/guides/basics/flags).

### Anulowanie zdarzeń (Event Cancellation) jest teraz bardziej zaawansowane

W filmie instruktażowym dotyczącym [GUI w ekwipunku](/guides/put-it-together/inventory-guis) uczy się anulowania ogólnego zdarzenia kliknięcia i uruchamiania akcji w odpowiedzi na bardziej szczegółowe zdarzenie.

Choć jest to wciąż poprawne, brakuje w tym podejściu elementu niezbędnego do dobrego działania w nowoczesnym Denizen.

W przeszłości zdarzenia po prostu wszystkie się odpalały, a anulowanie zdarzenia oznaczało jedynie, że bazowa akcja nie zostanie wykonana. W nowoczesnym Denizen system inteligentniej rozpoznaje, aby nie odpalać kolejnych zdarzeń skryptowych po tym, jak zdarzenie zostało anulowane. Choć [można to po prostu wyłączyć dla odpowiednich zdarzeń](https://meta.denizenscript.com/Docs/Languages/Script%20Event%20Cancellation), lepszym rozwiązaniem jest zagwarantowanie, że ogólne zdarzenie, które je anuluje, uruchomi się jako *ostatnie*. Jest to tak proste, jak dodanie wysokiej wartości `priority` (priorytet) do linii anulującego zdarzenia.

Zatem tam, gdzie wcześniej miałeś `on player clicks in moj_ekwipunek:`, teraz masz zamiast tego `on player clicks in moj_ekwipunek priority:100:`, i podobną zmianę w linii zdarzenia `drags`. Priorytety zdarzeń działają w kolejności numerycznej, gdzie domyślny priorytet to `0`. Wszystkie specyficzne zdarzenia, z ich domyślnym priorytetem, uruchomią się najpierw, a dopiero po ich zakończeniu, ogólne zdarzenia anulujące <span class="parens">(teraz z priorytetem `100`)</span> odpalą się jako ostatnie.

### Tagi z daszkami (Carets) nie są już używane

Filmy demonstrowały tag `<^npc>` jako sposób na pobranie NPC z kolejki przed użyciem argumentu `npc:`, oraz wspominały o `<^player>` jako o analogicznej koncepcji. Są one obecnie uważane za przestarzałe/nieistotne, ponieważ zamiast nich możesz i powinieneś po prostu używać `define`.

```dscript_blue
better_npc_handler:
    type: task
    script:
    - narrate "Przekażmy NPC wstecz"
    - define npc <npc>
    - run my_other_task def:<[npc]> npc:<[some_other_npc]>
```

W powyższym przykładzie polecenie `define` oraz tag `<[npc]>` zastępują to, co w przeciwnym razie byłoby tagiem `<^npc>`.

(Zostało to usunięte z powodu połączenia faktu bycia niezwykle rzadko używanym oraz posiadania bardzo złożonych mechanizmów wewnętrznych obciążających silnik skryptowy nawet wtedy, gdy nie są używane).
