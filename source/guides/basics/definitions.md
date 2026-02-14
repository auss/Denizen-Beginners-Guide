Pamięć krótkotrwała: Definicje (Definitions)
------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czym są definicje?

Wiesz już, jak pisać podstawowe skrypty. Poznałeś podstawowe koncepcje typów skryptów, poleceń i tagów.

Jeśli śledziłeś wszystko uważnie i analizowałeś przykłady, prawdopodobnie zorientowałeś się już, że możesz spowodować zmianę wartości tagu poprzez modyfikację rzeczywistej wartości w grze, na której dany tag bazuje.

Na przykład możesz zmodyfikować wartość `<player.health>` za pomocą poleceń `hurt` lub `heal`. Jeśli `<player.health>` zwraca 20, a chcesz, aby wynosiło 10, możesz po prostu użyć `- hurt 10`. Całkiem proste.

Ale co, jeśli potrzebujesz wartości, którą możesz odczytać z tagu lub zmienić w dowolnym momencie, a która nie jest przypisana do żadnej rzeczywistej wartości w grze?

Tutaj wchodzą **definicje** (definitions).

Definicje <span class="parens">(często nazywane po prostu „defs”)</span> to prosta forma pamięci krótkotrwałej w skrypcie Denizen. W matematyce czy programowaniu nazwalibyśmy je *zmiennymi*.

### Jak używać definicji?

Definicje są tworzone lub zmieniane za pomocą polecenia `define`, a odczytywane za pomocą tagu definicji.

Każda definicja ma nazwę i wartość. Nazwa jest zazwyczaj statyczna <span class="parens">(ale może być dynamiczna)</span> i jest wpisywana bezpośrednio do skryptu. Wartość jest dynamiczna i przechowywana w całości w pamięci.

#### Tag definicji

Tag definicji jest tagiem specjalnym. Chociaż technicznie jest oznaczony jako `<definition[<nazwa>]>` <span class="parens">(gdzie `nazwa` to nazwa definicji)</span>, w skryptach zapisuje się go jako `<[<nazwa>]>`.

Na przykład definicję o nazwie `target` można odczytać za pomocą `<[target]>`.

Jeśli definicja o nazwie `target` przechowuje wartość typu `EntityTag`, to tagu `<EntityTag.health>` można użyć jako `<[target].health>`.

Jeśli pamiętasz [podstawowy format tagu](/guides/first-steps/tags), tag zaczyna się od tagu bazowego i czasem może przyjmować parametr wejściowy. Na przykład `<element[cześć]>` to tag bazowy o nazwie `element` z wejściem `cześć`. Może Cię to zdziwić, ale tag definicji dokładnie pasuje do tego formatu. Prawdopodobnie pytasz „jaki jest tutaj tag bazowy?”, a odpowiedź brzmi: żaden. Pusty tag bazowy to tag bazowy definicji. Oznacza to, że tag bazowy o zerowej długości nazwy jest interpretowany jako skrót dla `definition`.

#### Polecenie Define

Polecenie `define` służy do tworzenia lub zmiany definicji.

Standardowy format wejściowy dla `define` to: `- define <nazwa> <wartość>`.

Na przykład:
- `- define target <player.target>` ustawia definicję o nazwie `target` na wartość encji, na którą gracz aktualnie patrzy.
- `- define count 10` ustawia definicję o nazwie `count` na wartość `10`.
- `- define goal <player.health.mul[2]>` ustawia definicję o nazwie `goal` na aktualną wartość zdrowia gracza pomnożoną przez dwa.

### Przykład podstawowego użycia

Oto prosty skrypt zadania, który pokazuje użycie polecenia `define` i tagów definicji.

```dscript_green
def_sample:
    type: task
    script:
    - define current <player.health>
    - define goal <util.random.int[2].to[10]>
    - narrate "Twoje zdrowie to <[current]>. Uleczmy cię o <[goal]>, do poziomu <[current].add[<[goal]>]>!"
    - heal <[goal]>
```

Jeśli uruchomisz w grze `/ex run def_sample`, zostanie Ci wyświetlony Twój poziom zdrowia oraz losowo wybrana wyższa wartość, a następnie zostaniesz uleczony, aby osiągnąć tę wyższą wartość <span class="parens">(lub maksymalne zdrowie)</span>.

### Po co właściwie są definicje?

Jeśli spojrzysz na powyższy skrypt `def_sample`, zobaczysz bardzo powszechny przykład przydatności definicji: wartości losowe! Gdybyś próbował użyć tagu `util.random` zarówno w linii `narrate`, jak i `heal`, na czacie wyświetliłaby się jedna wartość, a uleczony zostałbyś o inną. To nie byłoby dobre! Użycie definicji oznacza, że możesz wylosować wartość raz, a następnie użyć tej wybranej wartości wielokrotnie.

Oprócz wartości losowych i złożonych, innym głównym przypadkiem użycia definicji są wartości, które się zmieniają. Rozważmy na przykład poniższy skrypt:

```dscript_red
this_needs_defs:
    type: world
    events:
        after player breaks *_ore:
        - ratelimit <player> 30s
        - if <player.health> < 5:
            - narrate "Wykopałeś <context.material.name>! Całkiem nieźle."
            - wait 2s
            - narrate "Ale czy nie powinieneś się leczyć zamiast kopać świecidełka, <player.name>?"
            - wait 2s
            - narrate "Zostało ci tylko <player.health> życia."
```

Na pierwszy rzut oka może to wyglądać w porządku i prawdopodobnie zadziała zgodnie z oczekiwaniami podczas testów. Gdy gracz jest poważnie ranny i rozprasza się kopaniem rudy, skrypt wyśle irytujące wiadomości na czacie mówiące mu, żeby się uleczył, nie częściej niż co 30 sekund.

Wiele skryptów wykorzystuje polecenia `wait` (czekaj), zwłaszcza te, które mają działać jak dialog i wymagają przerw między wiadomościami. Niestety to opóźnienie wprowadza potencjalny problem: co jeśli gracz uleczy się <span class="parens">(lub, co gorsza, zginie)</span> w ciągu tych 4 sekund między zniszczeniem bloku a wyświetleniem ostatniej wiadomości? Zobaczysz wiadomość w stylu `Zostało ci tylko 20 życia!`, co brzmi trochę głupio.

Definicje pozwalają nam uratować ten pomysł. Poprzez wcześniejsze zdefiniowanie wartości zdrowia upewniamy się, że ostatnia wiadomość pokaże tę niską wartość, której się spodziewaliśmy.

```dscript_green
better_with_defs:
    type: world
    events:
        after player breaks *_ore:
        - ratelimit <player> 30s
        - define health <player.health>
        - if <[health]> < 5:
            - narrate "Wykopałeś <context.material.name>! Całkiem nieźle."
            - wait 2s
            - narrate "Ale czy nie powinieneś się leczyć zamiast kopać świecidełka, <player.name>?"
            - wait 2s
            - narrate "Zostało ci tylko <[health]> życia."
```

Teraz ostatnia wiadomość ma gwarancję wyświetlenia wartości zdrowia poniżej 5 z momentu, gdy gracz po raz pierwszy zniszczył blok, zgodnie z oczekiwaniami.

Ostatnim częstym powodem używania definicji jest czytelność. Czasami linia staje się bardzo długa z powodu skomplikowanych tagów, więc wstawienie skomplikowanego tagu do definicji pozwala zmieścić go w oryginalnej linii za pomocą krótkiego tagu definicji, czyniąc kod czystszym.

### Czas życia (Lifetime)

Definicje, jak sugeruje tytuł strony, są pamięcią *krótkotrwałą*. Co oznacza to „krótkotrwała”?

Formalnie rzecz biorąc, definicja jest powiązana z kolejką (queue), w której została utworzona, i istnieje tylko tak długo, jak ta kolejka <span class="parens">(dowiesz się więcej o [kolejkach w dalszej części przewodnika](/guides/basics/queues))</span>.

W normalnym użytkowaniu oznacza to, że za każdym razem, gdy skrypt się uruchamia, ma on swój własny zestaw definicji. Jeśli ustawisz definicję w jednym zdarzeniu (event) lub jednym wywołaniu zdarzenia, nie będzie ona dostępna przy następnym wywołaniu.

Oto kilka przykładów, jak definicje **NIE** działają:

```dscript_red
cant_go_between_scripts:
    type: world
    events:
        after player breaks iron_ore:
        - define health <player.health>
        after player breaks stone:
        - narrate "Miałeś <[health]> życia, kiedy kopałeś żelazo."
```

Powyższy przykład nie zadziała, ponieważ definicja została ustawiona w innej ścieżce skryptu niż ta, w której próbowano ją odczytać.

```dscript_red
no_queue_persistence:
    type: world
    events:
        after player breaks iron_ore:
        - narrate "Miałeś <[next_health]> życia, kiedy kopałeś poprzednią bryłkę żelaza."
        - define next_health <player.health>
```

Ten przykład oczywiście nie zadziała najpierw dlatego, że przy pierwszym wykopaniu `iron_ore` polecenie define w ogóle się jeszcze nie uruchomiło. Jednak nawet po tym pierwszym uruchomieniu definicja wciąż nie będzie działać, ponieważ definicja z *poprzedniego razu*, gdy zdarzenie wystąpiło, nie jest już dostępna przy *następnym* wystąpieniu tego zdarzenia.

Dla jasności: dotyczy to nie tylko zdarzeń. Za każdym razem, gdy używasz `run` dla skryptu `task`, ma on inny zestaw definicji <span class="parens">(dowiesz się, jak to zmienić w [dalszej części przewodnika](/guides/basics/run-options))</span>.

Na [następnej stronie](/guides/basics/flags) dowiesz się o opcji pamięci długotrwałej.

### Zaawansowane zmiany definicji

Jeśli chcesz zmienić definicję, którą już ustawiłeś, możesz oczywiście po prostu użyć kolejnego polecenia define. Na przykład poniższy kod jest poprawny:

```dscript_green
def_sample:
    type: task
    script:
    - define current <player.health>
    - define goal 1
    - if <player.inventory.contains_item[healing_tool]>:
        - define goal 20
    - narrate "Twoje zdrowie to <[current]>. Uleczmy cię o <[goal]>, do poziomu <[current].add[<[goal]>]>!"
    - heal <[goal]>
```

Ten skrypt uleczy gracza o 1 HP, chyba że ma on w ekwipunku własny przedmiot o nazwie „healing_tool” – w takim przypadku uleczy go do pełna. Takie użycie define, które nadpisuje poprzednie o tej samej nazwie, jest całkowicie poprawne i zadziała zgodnie z oczekiwaniami.

Ale co, jeśli chcesz zmodyfikować wartość w pewien sposób – na przykład dodać 1 do istniejącej wartości w definicji? Możesz oczywiście po prostu użyć tagów, np. `- define mydef <[mydef].add[1]>`.

Istnieje jednak czystszy sposób na wykonywanie tych powszechnych zmian wartości definicji. Ten sposób nazywa się **akcjami na danych** (Data Actions).

#### Jak używać akcji na danych?

Akcje na danych są dość proste do napisania. Oto poprzedni przykład, ale z użyciem akcji zamiast tagu: `- define mydef:++`. O wiele czyściej!

Akcje na danych, których można tak używać, to:
- `++` zwiększa wartość o jeden (inkrementacja). Przykład: `- define mydef:++`
- `--` zmniejsza wartość o jeden (dekrementacja). Przykład: `- define mydef:--`
- `!` czyści wartość (usuwa definicję). Przykład: `- define mydef:!`

Istnieją również akcje, które przyjmują dodatkową wartość wejściową. Na przykład, jeśli chcesz dodać 3 do definicji, możesz napisać `- define mydef:+:3`.

Oto akcje tego typu:
- `+` dodaje wartość do definicji. Przykład: `- define mydef:+:3`
- `-` odejmuje wartość od definicji. Przykład: `- define mydef:-:3`
- `*` mnoży definicję przez wartość. Przykład: `- define mydef:*:3`
- `/` dzieli definicję przez wartość. Przykład: `- define mydef:/:3`

Definicje mogą oczywiście przechowywać wartości typu ListTag (listy). Gdy definicja zawiera listę, dostępne są akcje ułatwiające interakcję z tą listą.

Akcje dla ListTag to:
- `->` wstawia nową wartość do listy. Przykład: `- define mydef:->:nowa_wartosc`
- `<-` usuwa istniejącą wartość z listy. Przykład: `- define mydef:<-:stara_wartosc`
- `|` dzieli listę wejściową i dołącza ją do oryginalnej listy. Przykład: `- define mydef:|:a|b|c`

Aby upewnić się, że rozumiesz, co robi każda z nich, napisz skrypt zadania, który wykonuje przykładowe zmiany na istniejącej definicji i obserwuj wynik.

```dscript_blue
data_actions_test:
    type: task
    script:
    - define mydef 3
    # Zmieniaj poniższą linię na każdą z przykładowych akcji i uruchamiaj skrypt
    - define mydef:++
    - narrate "mydef wynosi teraz <[mydef]>"
```

Kiedy uruchomisz ten skrypt, najpierw wyświetli `mydef wynosi teraz 4`, pokazując, że wartość 3 została zwiększona i stała się 4. Zmieniając środkową linię na kolejne przykłady, zobaczysz, jak wpływają one na wartość.

Dla akcji na listach zmień pierwszą linię na `- define mydef <list[stara_wartosc|inna_wartosc]>` dla celów testowych.

#### Co jeszcze?

Możesz również użyć polecenia `define` (oraz jego specjalnego wariantu `definemap`) do dynamicznego budowania map danych, co zostanie wyjaśnione bardziej szczegółowo w [sekcji zaawansowanej](/guides/advanced/submapping).

### Inne źródła definicji

Dodatkowo pamiętaj, że istnieją rzeczy, które tworzą definicje poza poleceniem `define`, w tym [pętle (loops)](/guides/basics/loops), niektóre polecenia <span class="parens">(jak shoot)</span>, o czym informuje dokumentacja danego polecenia, a czasem zewnętrzne wtyczki integrujące się z Denizen.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o definicjach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja polecenia Define](https://meta.denizenscript.com/Docs/Commands/define)
- [Tag definition](https://meta.denizenscript.com/Docs/Tags/definition)
- [Dokumentacja języka Data Actions](https://meta.denizenscript.com/Docs/Languages/data%20actions)
