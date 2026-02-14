Zrób to jeszcze raz: Pętle (Loops)
----------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czym jest „pętla”?

Wszystkie skrypty, które widziałeś do tej pory, miały jednokierunkowy przepływ od góry do dołu. Podana była dokładna kolejność, a polecenia wykonywały się jedno po drugim, ewentualnie pomijając kilka, jeśli użyłeś polecenia `if`.
Co jednak, gdybyś chciał wykonać te same polecenia jeszcze raz? Masz kilka opcji. Najprostszym podejściem byłoby po prostu napisanie tych samych poleceń ponownie. Choć może to zadziałać, jeśli chcesz powtórzyć coś tylko dwa razy lub jeśli jest to tylko jedna linijka, bardzo szybko może to wymknąć się spod kontroli.

Na szczęście nie musisz tego robić. Denizen posiada 3 różne polecenia pętli, które powtarzają blok instrukcji:
- `repeat` – powtarza blok określoną liczbę razy
- `foreach` – powtarza blok dla każdego elementu z podanej listy
- `while` – powtarza blok, dopóki warunek jest spełniony (jak polecenie `if`, które wykonuje się w kółko, dopóki warunek nie przestanie być prawdziwy)

### Polecenie Repeat

Najbardziej podstawowym zastosowaniem pętli jest po prostu ponowne uruchomienie bloku poleceń, do czego najlepiej nadaje się polecenie `repeat`.

Polecenie `repeat`, jak sugeruje nazwa, powtarza blok poleceń wielokrotnie.

Podstawowy format polecenia repeat to:

```dscript_blue
- repeat (liczba powtórzeń):
    - (polecenia tutaj)
```

Struktura ta jest podobna do polecenia `if` – kończysz linię `repeat` dwukropkiem `:`. Polecenia, które mają być powtarzane, muszą być wcięte, aby Denizen wiedział, że należą do pętli.

Oto jak może to wyglądać w prawdziwym skrypcie:

```dscript_green
my_lightning_task:
    type: task
    script:
    - repeat 5:
        - strike <player.location>
        - narrate "zostałeś trafiony piorunem!"
        - wait 1s
    - narrate "koniec piorunów"
```
Wypróbuj to w grze, wpisując `/ex run my_lightning_task`.

Pętla wykona się 5 razy, podczas których gracz zostanie trafiony piorunem i otrzyma wiadomość „zostałeś trafiony piorunem!”.
Na końcu bloku znajduje się również polecenie `wait` (czekaj). Wstrzymuje ono działanie skryptu na określony czas (w tym przykładzie na 1 sekundę). Jest to ważne w wielu przypadkach, aby polecenia nie uruchamiały się błyskawicznie jedno po drugim. W tym przykładzie, bez czekania, odniosłoby się wrażenie, że wszystkie pioruny uderzyły jednocześnie.

Gdy pętla się zakończy, skrypt toczy się dalej, kończąc się wiadomością „koniec piorunów”.

A co, jeśli chciałbyś poinformować gracza, ile razy już został trafiony? Mógłbyś dodać definicję śledzącą liczbę powtórzeń, ale `repeat` robi to już za Ciebie. Wystarczy dodać argument `as:` do polecenia repeat w ten sposób:

```dscript_green
my_lightning_task:
    type: task
    script:
    - repeat 5 as:count:
        - strike <player.location>
        - narrate "zostałeś trafiony piorunem już <[count]> razy!"
        - wait 1s
    - narrate "koniec piorunów"
```

Narracja będzie teraz mówić: „zostałeś trafiony piorunem już 1 razy!”, „zostałeś trafiony piorunem już 2 razy!” itd., aż do 5.

### Polecenie Foreach

Choć polecenie repeat jest poręczne, często będziesz chciał przejść przez zawartość listy i zrobić coś z każdym jej elementem.

Załóżmy, że chcemy powiedzieć graczowi, gdzie znajdują się wszystkie krowy wokół niego.
Używając polecenia repeat, mógłbyś zrobić coś takiego:

```dscript_red
my_cow_task:
    type: task
    script:
    - define cows <player.location.find_entities[cow].within[30]>
    - repeat <[cows].size> as:index:
        - define cow <[cows].get[<[index]>]>
        - narrate "Krowa jest zaledwie o <[cow].location.distance[<player.location>].round> bloków stąd!"
        - playeffect effect:fireworks_spark at:<[cow].location> visibility:50 quantity:100 data:0 offset:3
```

Istnieje jednak znacznie lepszy sposób z użyciem polecenia `foreach`.
Nazwa „foreach” oznacza dosłownie „dla każdego [elementu listy]...” i działa dokładnie tak, jak to brzmi – uruchamia blok poleceń raz dla każdego wpisu na liście.

`foreach` ma taką samą strukturę jak repeat:

```dscript_blue
- foreach (jakaś lista) as:(definicja przechowująca element):
    - (polecenia tutaj)
```

Oto jak możemy uprościć ten skrypt za pomocą pętli `foreach`:

```dscript_green
my_cow_task:
    type: task
    script:
    - foreach <player.location.find_entities[cow].within[30]> as:cow:
        - narrate "Krowa jest o <[cow].location.distance[<player.location>].round> bloków stąd!"
        - playeffect effect:fireworks_spark at:<[cow].location> visibility:50 quantity:100 data:0 offset:3
```

Wypróbuj to w grze przez `/ex run my_cow_task`, stojąc w pobliżu krów.

Znacznie lepiej. Co najważniejsze, nie musimy ręcznie pobierać elementu `cow` z listy: jest on już zdefiniowany.

Dodatkowo, foreach posiada wbudowaną definicję `<[loop_index]>`, która śledzi, po raz który pętla się wykonuje. Przykład użycia:

```dscript_green
my_cow_task:
    type: task
    script:
    - foreach <player.location.find_entities[cow].within[30]> as:cow:
        - narrate "<[loop_index]> - Krowa jest o <[cow].location.distance[<player.location>].round> bloków stąd!"
        - playeffect effect:fireworks_spark at:<[cow].location> visibility:50 quantity:100 data:0 offset:3
```

Wiadomości będą teraz poprzedzone „1- ”, „2- ” itd. w miarę trwania pętli.

### Polecenie While

Możesz napotkać sytuację, w której nie wiesz, ile razy pętla ma się powtórzyć, ale wiesz, kiedy powinna przestać. Możesz to osiągnąć za pomocą pętli `while`. Jest ona jak polecenie `if`, które uruchamia blok poniżej tak długo, jak warunek pozostaje prawdziwy.

```dscript_blue
- while (warunek):
    - (polecenia tutaj)
```
Format warunku jest taki sam jak w poleceniu `if`; [sprawdź stronę tego przewodnika](/guides/basics/if-command), aby dowiedzieć się więcej o jego strukturze.

Zobaczmy teraz przykład pętli `while`...

```dscript_green
my_move_task:
    type: task
    script:
    - define location <player.location>
    - while <player.is_online> && <[location].distance[<player.location>]> < 3:
        - narrate "Jesteś za blisko, odsuń się!"
        - wait 2s
```

Możesz to wypróbować w grze przez `/ex run my_move_task`. To zadanie po uruchomieniu będzie wielokrotnie kazało Ci się odsunąć, dopóki nie znajdziesz się 3 bloki od miejsca, w którym byłeś na początku.

Podobnie jak w poleceniu `repeat`, na końcu pętli mamy polecenie `wait`. Choć w pętlach `repeat` bywa ono przydatne tylko w niektórych przypadkach, w pętlach `while` powinieneś używać go niemal zawsze. Bez niego skrypt będzie próbował sprawdzać warunek ciągle i wielokrotnie, co może spowodować „zamrożenie” serwera do czasu zakończenia pętli, a nawet jego awarię, jeśli pętla nigdy się nie skończy.

Słowo ostrzeżenia: pętli `while` należy w miarę możliwości unikać. Bardzo łatwo jest stworzyć tzw. „nieskończoną pętlę”, czyli taką, która nigdy nie ma szansy się zatrzymać. Gdy taka pętla już ruszy, trudno ją zatrzymać inaczej niż przez wyłączenie serwera.

Dodatkowo, niektórzy użytkownicy piszą polecenia typu `while true`, aby uzyskać skrypty działające celowo w nieskończoność – nie jest to idealna metoda. Zazwyczaj do tego celu powinieneś używać [zdarzenia `delta time`](https://meta.denizenscript.com/Docs/Events/delta%20time).

### Zatrzymywanie pętli

Czasami w pętlach chcesz kontynuować powtarzanie tylko do osiągnięcia pewnego punktu, ale nie chcesz przerywać całego skryptu. Podczas gdy `stop` służy do przerywania skryptów, `repeat/foreach/while stop` służy do przerywania samej pętli.

Na przykład w pierwszym przykładzie, gdybyśmy chcieli przestać ciskać piorunami, jeśli gracz ma mniej niż 5 punktów życia, zrobilibyśmy to tak:

```dscript_green
my_lightning_task:
    type: task
    script:
    - repeat 5 as:count:
        - strike <player.location>
        - narrate "zostałeś trafiony piorunem już <[count]> razy!"
        - if <player.health> < 5:
            - repeat stop
        - wait 1s
    - narrate "koniec piorunów"
```
Ponieważ używamy `repeat stop`, kończy się tylko pętla; końcowa linia `narrate` wciąż zostanie wykonana.

Można to również stosować w pętlach `foreach`. Na przykład, gdybyśmy w przykładzie powyżej chcieli przestać szukać krów po znalezieniu małej krowy:

```dscript_green
my_cow_task:
    type: task
    script:
    - foreach <player.location.find_entities[cow].within[30]> as:cow:
        - narrate "Krowa jest o <[cow].location.distance[<player.location>].round> bloków stąd!"
        - playeffect effect:fireworks_spark at:<[cow].location> visibility:50 quantity:100 data:0 offset:3
        - if <[cow].is_baby>:
            - narrate "o mój boże, to cielaczek!!"
            - foreach stop
```

### Następny proszę

Zdarzają się sytuacje, w których chcesz pominąć resztę aktualnego powtórzenia i przejść od razu do następnego kroku pętli. Do tego służy `repeat/foreach/while next`.

Weźmy przykład z `foreach`. Załóżmy, że chcemy pominąć wszystkie małe krowy. Choć możesz umieścić cały blok wewnątrz polecenia `if` w ten sposób...

```dscript_green
my_cow_task:
    type: task
    script:
    - foreach <player.location.find_entities[cow].within[30]> as:cow:
        - if !<[cow].is_baby>:
            - narrate "<[loop_index]> - Krowa jest o <[cow].location.distance[<player.location>].round> bloków stąd!"
            - playeffect effect:fireworks_spark at:<[cow].location> visibility:50 quantity:100 data:0 offset:3
```

Możesz zamiast tego użyć `next`:

```dscript_green
my_cow_task:
    type: task
    script:
    - foreach <player.location.find_entities[cow].within[30]> as:cow:
        - if <[cow].is_baby>:
            - foreach next
        - narrate "Krowa jest o <[cow].location.distance[<player.location>].round> bloków stąd!"
        - playeffect effect:fireworks_spark at:<[cow].location> visibility:50 quantity:100 data:0 offset:3
```

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o pętlach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja polecenia Foreach](https://meta.denizenscript.com/Docs/Commands/foreach)
- [Dokumentacja polecenia Repeat](https://meta.denizenscript.com/Docs/Commands/repeat)
- [Dokumentacja polecenia While](https://meta.denizenscript.com/Docs/Commands/while)
