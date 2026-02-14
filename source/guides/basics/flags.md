Pamięć długotrwała: Flagi (Flags)
------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

Na [poprzedniej stronie](/guides/basics/definitions) dowiedziałeś się o definicjach i ich zastosowaniu jako pamięci krótkotrwałej. Na tej stronie poznasz **flagi** (flags).

### Czym są flagi?

Flagi, podobnie jak definicje, są formą pamięci. Są to punkty danych, które możesz dowolnie kontrolować i zmieniać w skrypcie, nie wpływając na nic poza własnymi skryptami.

Flagi różnią się od definicji dwoma kluczowymi aspektami:
- Po pierwsze, flagi to pamięć *długotrwała*, podczas gdy definicje są *krótkotrwałe*. Jak dowiedziałeś się na poprzedniej stronie, definicja trwa tylko tak długo, jak kolejka skryptu. Flagi natomiast trwają znacznie dłużej – mogą nawet pozostać na zawsze, jeśli to konieczne.
- Po drugie, możesz wybrać, do jakiego obiektu flaga jest przypisana. Jak zrozumiesz po zapoznaniu się z systemem flag, definicje są zasadniczo specjalnym przypadkiem flag powiązanym wyłącznie z kolejkami.

Flagi również wykorzystują system akcji na danych (Data Actions), podobnie jak definicje.

W przeciwieństwie do polecenia `define`, polecenie `flag` *zawsze* używa składni akcji na danych. Oznacza to, że najbardziej podstawowe ustawienie flagi używa dwukropka `:` zamiast spacji, np.: `- flag <obiekt> my_flag:moje_wartosc`.

### Podstawowe użycie flag

Zanim zajmiemy się teorią stojącą za flagami, poćwiczmy ich używanie, korzystając z [polecenia /ex](/guides/first-steps/ex-command)!

Otwórz okno Minecrafta i wpisz to polecenie: `/ex flag server greetable:Świecie`

Tym poleceniem ustawiłeś flagę o nazwie „`greetable`” na wartość „`Świecie`” na obiekcie `server`.

Teraz skorzystajmy z tej flagi... wpisz: `/ex narrate "Witaj, <server.flag[greetable]>!"`

Jeśli wszystko poszło dobrze, powinieneś zobaczyć wynik podobny do tego:

![](images/hello_greetable.png)

### Flagi są przypisane do obiektów

Flagi są zawsze *przypisane do obiektu*. W powyższym przykładzie użyliśmy obiektu `server`. Obiekt `server` to specjalny obiekt pomocniczy, który istnieje po to, aby flagi (i inne rzeczy) mogły mieć globalny, statyczny cel. Flagi przypisane do serwera zostają na zawsze i można do nich uzyskać dostęp z dowolnego miejsca w dowolnym czasie.

Najczęstszym obiektem, do którego przypisuje się flagi, jest gracz. Kiedy flaga jest przypisana do gracza, oznacza to, że tylko ten jeden konkretny gracz posiada tę flagę – inni gracze będą ją mieć tylko wtedy, gdy polecenie flag zostanie użyte ponownie dla każdego z nich.

Ustawienie flagi na graczu jest proste – działa to tak samo jak ustawienie flagi globalnej na serwerze, musisz tylko podać poleceniu konkretnego gracza zamiast `server`.

Spróbuj wpisać to polecenie: `/ex flag <player> status:uczeń`

A następnie wpisz: `/ex narrate "<player.name> to <player.flag[status]> Denizen!"`

Powinieneś zobaczyć mniej więcej coś takiego:

![](images/learning_denizen_flag.png)

Jeśli jakikolwiek inny gracz spróbuje użyć tego polecenia `/ex narrate` bez wcześniejszego wykonania `/ex flag`, zobaczy komunikat o błędzie – ponieważ tylko *Ty* masz flagę `status`, nie inni gracze.

### Do czego mi się to przyda?

Powyższe przykłady są dość abstrakcyjne, więc wykorzystajmy części systemu flag, których już się nauczyliśmy, aby stworzyć prosty skrypt przypominający coś, czego faktycznie mógłbyś użyć.

```dscript_green
ore_counter:
    type: world
    events:
        after player breaks iron_ore:
        - flag player ores_broken:++
        - narrate "Wykopałeś już <player.flag[ores_broken]> rud żelaza! Tak trzymaj!"
```

Ten skrypt będzie prowadził bieżące odliczanie, ile bloków rudy żelaza wykopał gracz. Każdy gracz będzie miał swój własny licznik <span class="parens">(wykorzystuje to akcję „increment” (inkrementacja), która została wyjaśniona na [stronie o definicjach](/guides/basics/definitions))</span>.

![](images/ore_counter.gif)

Możesz użyć akcji „clear” (wyczyść), oznaczonej symbolem `!`, aby usunąć flagę w dowolnym momencie. Po wypróbowaniu powyższego skryptu spróbuj wpisać `/ex flag player ores_broken:!`, aby usunąć swój własny licznik rudy żelaza, a następnie zniszcz kolejny blok rudy. Jeśli wszystko poszło dobrze, zobaczysz, że tag flagi wypełni się wartością `1` <span class="parens">(wskazując, że z punktu widzenia skryptu jest to pierwsza ruda żelaza, jaką kiedykolwiek wykopałeś)</span>.

### Czy posiadasz tę flagę?

Jeśli chcesz wiedzieć, czy dany obiekt posiada już flagę, czy nie, rozwiązanie jest proste: `has_flag`! Wygląda to tak samo jak tag `flag`, ale zamiast zwracać wartość flagi, zwróci prostą wartość logiczną: `true` lub `false`.

Możesz tego użyć wewnątrz polecenia if, np. `- if <player.has_flag[ores_broken]>:`, aby uruchomić polecenia bloku `if` tylko wtedy, gdy gracz wykopał już przynajmniej jedną rudę żelaza w powyższym skrypcie.

Jest to również podstawowy sposób na implementację wartości, które albo są, albo ich nie ma <span class="parens">(w przeciwieństwie do posiadania dodatkowych danych, jak w przypadku `ores_broken`, z którym powiązana jest liczba)</span>.

```dscript_blue
toggler_task:
    type: task
    script:
    - if <player.has_flag[my_first_toggle]>:
        - flag player my_first_toggle:!
        - narrate "Wyłączono przełącznik."
    - else:
        - flag player my_first_toggle
        - narrate "Włączono przełącznik."
```

Powyższy przykładowy skrypt, po uruchomieniu przez `/ex run toggler_task`, przełączy flagę `my_first_toggle` (włączy lub wyłączy), usuwając ją lub ustawiając <span class="parens">(jeśli nie masz konkretnej wartości do podania, możesz po prostu pominąć wartość w poleceniu flag)</span>, używając tagu `has_flag` do określenia, czy należy ją dodać, czy usunąć.

W miarę poznawania kolejnych funkcji, takich jak [polecenia gracza (player commands)](/guides/basics/player-commands), będziesz mógł używać tego do tworzenia prostych, lecz potężnych opcji, jak np. polecenie, które po użyciu przełącza flagę, co z kolei wpływa na zdarzenie świata sprawdzające tę flagę w celu nałożenia specjalnego efektu.

### Flagi to podstawa Denizen

Flagi są kluczową funkcją Denizen... oznacza to, że są one zintegrowane w wielu miejscach, aby ułatwić ich używanie.

Na przykład, jeśli chcesz, aby zdarzenie uruchamiało się tylko wtedy, gdy gracz posiada konkretną flagę, możesz użyć przełącznika zdarzenia `flagged:`, w ten sposób:

```dscript_green
optional_ore_counter:
    type: world
    events:
        after player breaks iron_ore flagged:ore_quest:
        - flag player ores_broken:++
        - narrate "Wykopałeś już <player.flag[ores_broken]> rud żelaza! Tak trzymaj!"
```

To jest skrypt `ore_counter` sprzed chwili, ale teraz uruchamia się tylko dla graczy, którzy mają włączoną flagę o nazwie `ore_quest`. Teraz, zamiast być tylko ogólnym licznikiem wykopanych rud, skrypt ten stał się częścią zadania (questu), które wymaga od gracza wyjścia i wykopania określonej liczby bloków rudy żelaza, aby je ukończyć.

W Denizen istnieje wiele takich funkcji, w tym przełącznik zdarzenia gracza `flagged:`, podobne przełączniki `server_flagged:`, `location_flagged:` itp., obiekty typu `(x)_flagged` do dopasowywania (matchables), różne argumenty poleceń jak `to_flagged` w poleceniu `announce`, szereg wygodnych tagów jak `<server.online_players_flagged[nazwa_flagi]>` i wiele innych. Napotkasz wiele z nich, w miarę jak będziesz uczyć się Denizen i zaczniesz przeglądać meta-dokumentację.

### Czas życia (Lifetime)

Flagi są *trwałe* (persistent). Oznacza to, że rzeczy, które normalnie powodują utratę danych, jak restart serwera, nie usuną danych flag.

Istnieją tylko cztery przypadki, w których flaga zniknie:
- Jawne, zamierzone usunięcie w skrypcie przy użyciu akcji „clear”.
- Wygaśnięcie czasowe <span class="parens">(wyjaśnione w następnej sekcji)</span>.
- Obiekt, do którego flaga była przypisana, przestał istnieć <span class="parens">(na przykład flaga jest na mobie, a ten mob ginie)</span>.
- I oczywiście: poważna awaria serwera lub usunięcie plików serwerowych.

### Wygasanie flag

Jednym z częstych zastosowań flag są długoterminowe liczniki czasu odnowienia (cooldown), np. zadanie lub efekt specjalny działający tylko raz dziennie. Oto przykład, jak może to wyglądać:

```dscript_blue
once_a_day:
    type: task
    script:
    - if <player.has_flag[my_script_cooldown]>:
        - narrate "Możesz odebrać tę nagrodę tylko raz dziennie. Musisz poczekać jeszcze <player.flag_expiration[my_script_cooldown].from_now.formatted>."
        - stop
    - flag player my_script_cooldown expire:1d
    - narrate "Oto Twoja codzienna nagroda!"
    - give diamond
```

Ten przykład skryptu da graczowi diament, ale nie częściej niż raz dziennie, używając flagi jako metody odliczania czasu odnowienia.

Chociaż w skrypcie typu `task` może się to wydawać mało sensowne, po zapoznaniu się z dalszą częścią przewodnika będziesz mógł [podpiąć to pod obsługę kliknięcia NPC](/guides/npcs/assignment-scripts), aby stworzyć NPC, którego można klikać raz dziennie po nagrodę – stanie się to kompletną funkcją Twojego serwera!

![](images/once_a_day.gif)

Zauważysz tutaj dwie nowe części systemu flag:

Po pierwsze, w poleceniu `flag` pojawił się argument `expire:1d`. Robi on dokładnie to, co myślisz: sprawia, że flaga pozostaje tylko przez jeden dzień... po tym czasie flaga jest traktowana tak, jakby nie istniała. Wejściem jest obiekt `DurationTag`, który można zapisać jako `1d` (jeden dzień), `5h` (pięć godzin) itp. Wartość czasu trwania jest następnie używana do obliczenia daty i godziny wygaśnięcia flagi <span class="parens">(przez dodanie czasu trwania do obecnej chwili)</span> i zapisywana wraz z flagą.

Po drugie, w poleceniu `narrate` użyliśmy tagu `flag_expiration`. Tag ten, jak sama nazwa wskazuje, zwraca czas, w którym flaga ma wygasnąć. Robi to w formie obiektu `TimeTag`. Następnie używamy tagu `TimeTag.from_now`, który pobiera `DurationTag` reprezentujący odstęp czasu między „teraz” a wcześniej zapisanym `TimeTag` <span class="parens">(zatem w momencie ustawienia flagi będzie to `1d`... jeśli poczekasz godzinę, będzie to `23h` itd.)</span>, a na końcu tagu `DurationTag.formatted`, który konwertuje surowy obiekt czasu na postać czytelną dla człowieka.

Pamiętaj, że wygaśnięcie flagi to coś, co możesz sprawdzić, ale nie możesz na to czekać – nie ma zdarzenia typu „flag expires” ani podobnego. Jeśli chcesz wiedzieć dlaczego, sprawdź [dokumentację techniczną Flag System](https://meta.denizenscript.com/Docs/Languages/flag%20system).

Pamiętaj również, że dla bardzo krótkich/prostych czasów odnowienia (zazwyczaj krótszych niż kilka godzin) często lepiej użyć polecenia `ratelimit` zamiast flag, np. `- ratelimit <player> 1h`. Ratelimity są bardzo proste i łatwe w użyciu, ale co ważne – nie przetrwają restartu serwera i nie dają możliwości wyświetlenia własnej wiadomości o czasie oczekiwania, jak skrypt powyżej.

### Do czego jeszcze mogę przypisać flagi?

Możesz flagować naprawdę wiele obiektów! Oto najważniejsze z nich:

- Sam serwer jako flagę globalną, używając słowa kluczowego `server`.
- Dowolnego gracza, używając obiektu `PlayerTag`.
- Dowolnego NPC z Citizens, używając obiektu `NPCTag`.
- Dowolną encję w Minecraft, używając obiektu `EntityTag`.
- Dowolny blok w świecie, używając obiektu `LocationTag`.
- Dowolny chunk (fragment świata), używając obiektu `ChunkTag`.
- Dowolny przedmiot – jednak przedmioty działają nieco inaczej, co zostanie omówione na [stronie o własnych przedmiotach](/guides/basics/custom-items).
- Dowolny świat, używając obiektu `WorldTag`.
- Dowolny *zanotowany* (noted) obszar <span class="parens">(notatki zostaną omówione w [sekcji zaawansowanej](/guides/advanced/notables))</span>, używając obiektów `CuboidTag`, `EllipsoidTag` lub `PolygonTag`.
- Dowolny *zanotowany* ekwipunek <span class="parens">(notatki zostaną omówione w [sekcji zaawansowanej](/guides/advanced/notables))</span>, używając obiektu `InventoryTag`.
- Większość innych typów obiektów również. Każdy typ, który jest w jakiś sposób unikalnie zidentyfikowany, zazwyczaj może posiadać flagi.

Pamiętaj, że flagi są zazwyczaj ładowane wtedy, gdy ładowany jest obiekt – flagi serwera przy starcie, gracza przy dołączeniu itd.

W przypadku graczy, jeśli gracz jest offline, gdy używasz poleceń lub tagów flag, dane gracza zostaną automatycznie załadowane w tym momencie.

W przypadku encji (bytów) jednak, jeśli encja jest odładowana (despawned) <span class="parens">(co oznacza, że znajduje się w chunku, który nie jest załadowany)</span>, nie ma sposobu na niezawodne załadowanie tej encji i flaga nie zadziała.

W przypadku bloków, jeśli chunk zawierający blok nie jest załadowany, będziesz musiał go załadować przed pracą z jego flagami. Dla polecenia `flag` stanie się to automatycznie, jednak dla *tagów* flag nie ma automatycznego ładowania chunka – możesz zamiast tego użyć wcześniej polecenia `chunkload`, aby upewnić się, że fragment świata jest dostępny.

Więcej informacji o tym, jak różne typy obiektów obsługują flagi, znajdziesz w [dokumentacji technicznej Flag System](https://meta.denizenscript.com/Docs/Languages/flag%20system).

### Nazwy flag

Wiele podstawowych symboli ASCII <span class="parens">(jak `.` lub `!` lub `%` itp., właściwie wszystko, co nie jest literą ani cyfrą)</span> w nazwach czegokolwiek <span class="parens">(flag, definicji, kontenerów skryptów itd.)</span> w Denizen może mieć specjalne znaczenie i poza sytuacjami, gdy celowo z nich korzystasz, należy ich unikać. Trzymaj się prostych nazw tekstowych, używając podkreślników `_` do oddzielania słów zamiast spacji.

#### Uwaga zaawansowana: Mapy i sub-mapy

Zaawansowani użytkownicy mogą zauważyć, że flagi są formą map danych – czyli systemu danych składającego się z kluczy i przypisanych do nich wartości. Takich użytkowników mogą zainteresować obiekty `MapTag` lub używanie flag do tworzenia dynamicznych struktur sub-mapowania (mapy w mapach). System flag Denizen wspiera sub-mapowanie automatycznie przez użycie symbolu `.` jako separatora, np. `- flag server korzen.podmapa.klucz:wartosc`. Przypisze to flagę o nazwie `korzen.podmapa` do wartości MapTag `[klucz=wartosc]`, a tym samym flagę o nazwie `korzen` do zagnieżdżonej wartości MapTag `[podmapa=[klucz=wartosc]]`. Tag `has_flag` zwróci prawdę dla `has_flag[korzen]`, `has_flag[korzen.podmapa]` oraz `has_flag[korzen.podmapa.klucz]`.

Jeśli wydaje Ci się to skomplikowane lub tajemnicze, nie martw się, nie musisz tego teraz używać ani rozumieć. Dalsze wyjaśnienie tego systemu, sposobu jego użycia i zastosowań znajdzie się w późniejszej części [sekcji zaawansowanej](/guides/advanced/submapping).

#### Uwaga specjalna: Zastrzeżone nazwy flag

Należy pamiętać, że nazwy flag zaczynające się od dwóch podkreślników, np. `__nazwa`, są uważane za zastrzeżone. Wynika to z faktu, że niektóre wewnętrzne funkcje Denizen generują własne flagi i używają tej konwencji nazewnictwa, aby uniknąć konfliktów ze skryptami. Obejmuje to na przykład polecenie `zap` używające `__interact_step` oraz polecenie `cooldown` używające `__interact_cooldown`.

### Jeszcze więcej pamięci

Zaawansowani użytkownicy mogą uznać, że potrzebują jeszcze więcej opcji własnej pamięci. Chociaż flagi i definicje powinny pokrywać zdecydowaną większość przypadków, Denizen udostępnia dodatkowe opcje <span class="parens">(przeznaczone głównie do kompatybilności z istniejącymi systemami danych)</span>, takie jak [polecenie YAML](https://meta.denizenscript.com/Docs/Commands/yaml) oraz [polecenie SQL](https://meta.denizenscript.com/Docs/Commands/sql).

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o flagach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka Flag System](https://meta.denizenscript.com/Docs/Languages/flag%20system)
- [Dokumentacja polecenia flag](https://meta.denizenscript.com/Docs/Commands/flag)
- [Tag FlaggableObject.flag](https://meta.denizenscript.com/Docs/Tags/flaggableobject.flag)
- [Tag FlaggableObject.has_flag](https://meta.denizenscript.com/Docs/Tags/flaggableobject.has_flag)
- [Tag FlaggableObject.list_flags](https://meta.denizenscript.com/Docs/Tags/flaggableobject.list_flags)
- [Tag FlaggableObject.flag_expiration](https://meta.denizenscript.com/Docs/Tags/flaggableobject.flag_expiration)
- [Tag FlaggableObject.flag_map](https://meta.denizenscript.com/Docs/Tags/flaggableobject.flag_map)
- [Dokumentacja polecenia ChunkLoad](https://meta.denizenscript.com/Docs/Commands/chunkload)
- [Dokumentacja polecenia Ratelimit](https://meta.denizenscript.com/Docs/Commands/ratelimit)
- [Dokumentacja typu obiektu DurationTag](https://meta.denizenscript.com/Docs/ObjectTypes/DurationTag)
