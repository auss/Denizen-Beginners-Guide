Częste błędy
-------------

Istnieje kilka powszechnych błędów oraz niezbyt oczywistych oczekiwań dotyczących obsługi różnych rzeczy, które zaobserwowaliśmy, pomagając nowym użytkownikom Denizen. Aby pomóc Ci szybciej opanować Denizen, wymieniliśmy poniżej kilka z tych kwestii wraz z informacją, co z nimi zrobić.

```eval_rst
.. contents:: Spis treści
    :local:
```

### Tekst wyświetlany a Dane

Ważnym rozróżnieniem, którego należy się nauczyć, jest różnica między *tekstem wyświetlanym* (display text) a *rzeczywistymi danymi* (real data).

- **Rzeczywiste Dane** to faktyczne dane wewnętrzne, sformatowane tak, aby skrypty mogły je przetwarzać jak najłatwiej.
- **Tekst Wyświetlany** to wiadomość wyświetlana użytkownikom, często generowana na podstawie rzeczywistych danych.

Zasada ogólna brzmi: rzeczywiste dane mogą służyć do generowania tekstu wyświetlanego, ale tekst wyświetlany nigdy nie powinien służyć do generowania rzeczywistych danych.

Oto kilka przykładów sytuacji, w których użytkownicy mylą *rzeczywiste dane* z *tekstem wyświetlanym*.

#### Lore przedmiotu to nie dane

Częstym błędem jest traktowanie *opisu przedmiotu* (lore) jako źródła danych.

Naturalne wydaje się myślenie: „Skoro mój przedmiot ma w opisie linię `Dodatkowe Obrażenia: +15`, to muszę wyciągnąć to `+15` z opisu podczas zdarzenia zadawania obrażeń, aby je faktycznie zastosować!”. Jest to jednak działanie od tyłu: byłaby to konwersja tekstu wyświetlanego na dane, podczas gdy należy robić tylko odwrotnie.

Własne rzeczywiste dane dla przedmiotu mogą być przechowywane w kilku miejscach:
- Flagi przedmiotu <span class="parens">(dostępne przez `<[item].flag[nazwa_flagi]>`)</span>
- Klucze skryptu przedmiotu <span class="parens">(dostępne przez `<[item].script.data_key[nazwa_klucza]>`)</span>
- Standardowe funkcje Minecrafta, takie jak system atrybutów (Attributes)

Po zapisaniu danych we właściwym miejscu możesz je wyświetlić w opisie (lore). Od tego momentu zawsze pisz tagi itp. w oparciu o rzeczywiste dane, które zapisałeś, a nie o opis – ten jest dla graczy, nie dla Twoich skryptów!

#### Gracze to nie ich imiona

Historycznie w Minecraft gracze byli unikalni na podstawie swojego imienia. Oznaczało to, że „Steve” teoretycznie zawsze miał być „Steve'em”. Był jeden prawdziwy „Steve”, nikt inny nie mógł nim być, a on sam nie mógł zmienić imienia. Zmieniło się to w okolicach wersji 1.7, kiedy to identyfikatory UUID stały się unikalnym znacznikiem gracza, a graczom pozwolono na zmianę imion.

Nigdy nie śledź imienia gracza wewnętrznie w skryptach. Tag `<player.name>` powinien być używany wyłącznie do wyświetlania czytelnego imienia w poleceniu `narrate` lub podobnym wyjściu przeznaczonym dla graczy. Tylko do tego służy imię: do czytania przez ludzi. Nie służy ono do żadnego wewnętrznego śledzenia. Nie jest unikalne ani wiarygodne.

\* Uwaga: istnieje jeden wyjątek: polecenie `execute` uruchamia polecenia zewnętrznych wtyczek, które prawdopodobnie oczekują imienia lub UUID jako wejścia, a nie obiektu Denizen.

#### Więc gracz to jego UUID?

Gracz **nie jest** po prostu swoim UUID. Gracz nie jest imieniem, UUID, lokalizacją ani niczym innym. Gracz to gracz.

Podobnie NPC **nie jest** po prostu swoim ID. NPC to NPC.

W rzeczywistości nic nie jest *tylko* tym małym skrawkiem informacji, który go unikalnie identyfikuje.

#### System obiektów (Object System)

Ważną częścią sposobu działania Denizen jest **system obiektów**.

„Obiekt” w świecie oprogramowania to reprezentacja czegoś konkretnego, co można śledzić za pomocą identyfikatora **wyszukiwania** (lookup identifier), ale co istnieje jako coś więcej niż tylko te dane wyszukiwania. Brzmi to skomplikowanie, więc co oznacza w praktyce? Oznacza to, że „obiekt encji” to rzeczywista, pełna encja (byt), ze swoją sztuczną inteligencją, zdrowiem, imieniem, konkretnym miejscem w świecie i wszystkim innym, co sprawia, że jest tym, czym jest. Encję można szybko wyszukać za pomocą UUID, ale prawdziwa encja sama w sobie to znacznie więcej niż krótki ciąg cyfr i liter. <span class="parens">(Uwaga dla ciekawskich: w większości języków programowania unikalnym identyfikatorem obiektu jest jego adres w pamięci)</span>.

W Denizen, ilekroć patrzysz na obiekt w debugu lub za pomocą polecenia `narrate` <span class="parens">(lub gdziekolwiek indziej w tekście)</span>, widoczny jest unikalny identyfikator z prefiksem określającym typ obiektu (nazywa się to **notacją obiektu** – Object Notation). **Nie ma** ona być samym obiektem, lecz identyfikatorem wyszukiwania, abyś Ty lub system mogli go odczytać i dowiedzieć się, o jaki obiekt chodzi.

Pisząc skrypty, ważne jest, aby upewnić się, że pracujesz na *rzeczywistym obiekcie*, a nie na tekście zawierającym identyfikator wyszukiwania.

Kilka przykładów, gdzie może to mieć znaczenie:
- Obiekt gracza zostaje umieszczony w linii tekstu. Powiedzmy, że gdzieś zapisano `"Player:<player>"`. Kiedy odczytasz ten tekst, możesz założyć, że `<[TEN_TEKST].after[:]>` zwróci obiekt gracza – ale tak się nie stanie. Zwróci on czysty tekst unikalnego identyfikatora gracza. Musiałbyś go ponownie skonwertować na obiekt gracza, używając albo `<player[<[TEN_TEKST].after[:]>]>` albo `<[TEN_TEKST].after[:].as[player]>` <span class="parens">(choć w niektórych kontekstach Denizen może to automatycznie naprawić za Ciebie)</span>.
- W niektórych przypadkach odczyt bezpośrednio z magazynu danych <span class="parens">(YAML, Flagi, SQL itp.)</span> może zwrócić tekstowy identyfikator obiektu, który został tam wstawiony. W takiej sytuacji ponownie musisz skonwertować go z powrotem na rzeczywisty obiekt za pomocą odpowiednich tagów konwersji.
- Zazwyczaj gdy podawane są dane od użytkownika (np. w skrypcie polecenia). Może zostać użyty unikalny identyfikator lub nawet nieunikalny, a Ty będziesz musiał wykonać bardziej złożone operacje wyszukiwania rzeczywistego obiektu. Jako konkretny przykład: gdy skrypt polecenia ma opcję wpisania gracza, zazwyczaj można założyć, że użytkownicy nie wpiszą idealnego identyfikatora obiektu. Tag `server.match_player` jest przydatny do konwersji wpisanego przez człowieka imienia gracza na rzeczywisty obiekt gracza.

### Nie ufaj graczom

![](images/cheatingplayer.png)

Kiedy piszesz skrypty, możesz zazwyczaj założyć, że system przetworzy to, co napisałeś, dokładnie tak, jak napisałeś. Jeśli użyłeś polecenia flag do ustawienia flagi na przypisanym graczu, możesz bezpiecznie założyć, że `player.flag` zwróci później wartość tej flagi.

Gracze nie są jednak maszynami. Są ludźmi. Ludzie popełniają błędy – ludzie czasem też lubią oszukiwać. Tworząc skrypty obsługujące dane od użytkownika, musisz się na to przygotować i to uwzględnić.

Zademonstrujmy różnicę między złym skryptem, który ufa graczom, a dobrym skryptem, który tego nie robi, na przykładzie polecenia „pay” (zapłać) w systemie ekonomii.

```dscript_red
pay_command:
    type: command
    name: pay
    usage: /pay [gracz] [kwota]
    description: Płaci określonemu graczowi.
    script:
    - money take quantity:<context.args.get[2]>
    - money give players:<server.match_player[<context.args.get[1]>]> quantity:<context.args.get[2]>
    - narrate "<blue>Zapłaciłeś <gold><server.match_player[<context.args.get[1]>].name> <green>$<context.args.get[2]>"
```

Ten skrypt jest ładny i prosty. Zajmuje tylko 3 linie i robi wszystko, co trzeba... jeśli gracz używa go dokładnie tak, jak określono, bez żadnych pomyłek ani zamiaru nadużycia systemu.

Zobaczmy, jak ten skrypt wygląda, jeśli starannie zweryfikujemy wszystkie dane od użytkownika. Przeczytaj komentarze w skrypcie, aby zobaczyć, przed czym się zabezpieczamy.

```dscript_green
pay_command:
    type: command
    name: pay
    usage: /pay [gracz] [kwota]
    description: Płaci określonemu graczowi.
    # Użytkownicy mogą być w więzieniu itp. i mieć odebrane uprawnienia,
    # więc upewnijmy się, że wymagamy uprawnienia do użycia polecenia.
    permission: myscript.pay
    script:
    # Gracze mogą po prostu wpisać "/pay", nie pamiętając argumentów,
    # więc jeśli tak zrobią, po prostu powiedz im, jak użyć komendy i przerwij.
    - if <context.args.size> < 2:
        - narrate "<red>/pay [gracz] [kwota]"
        - stop
    # Użyj fallbacka na wypadek, gdyby podane imię gracza było nieprawidłowe.
    - define target <server.match_player[<context.args.get[1]>].if_null[null]>
    # Użytkownik może pomylić się przy wpisywaniu imienia gracza.
    # Jeśli nie dopasowano gracza, poinformuj o tym i przerwij.
    - if <[target]> == null:
        - narrate "<red>Nieznany gracz '<yellow><context.args.get[1]><red>'."
        - stop
    - define amount <context.args.get[2]>
    # Użytkownik może pomylić się przy wpisywaniu liczby.
    # Jeśli tak się stało, poinformuj go i przerwij.
    - if !<[amount].is_decimal>:
        - narrate "<red>Nieprawidłowa kwota (to nie jest liczba)."
        - stop
    # Użytkownik może próbować oszukiwać, wpłacając ujemną wartość
    # (aby otrzymać pieniądze zamiast je stracić).
    # Zweryfikuj więc, czy liczba jest dodatnia.
    # Wykluczamy też zero, bo nie ma sensu płacić $0.
    - if <[amount]> <= 0:
        - narrate "<red>Kwota musi być większa od zera."
        - stop
    # Użytkownik może próbować zapłacić więcej niż posiada, przez oszustwo lub pomyłkę.
    # Upewnij się, że go na to stać i przerwij, jeśli nie.
    - if <player.money> < <[amount]>:
        - narrate "<red>Nie posiadasz <green>$<[amount]><red>."
        - stop
    - money take quantity:<[amount]>
    - money give players:<[target]> quantity:<[amount]>
    - narrate "<blue>Zapłaciłeś <gold><[target].name> <green>$<[amount]>"
```

To strasznie dużo rzeczy do sprawdzenia! Niestety dobre skrypty obsługujące dane od użytkownika stają się dość długie przez całą tę walidację. Na szczęście nikt nie powinien być w stanie popsuć tych dłuższych skryptów!

### Nie porównuj surowych obiektów

Porównywanie surowych obiektów to coś, co na początku wydaje się naturalne, a problemy wychodzą na jaw dopiero znacznie później.

„Porównywanie surowych obiektów” odnosi się do używania polecenia `if` lub podobnego do porównania surowego obiektu Denizen z czymś innym (często czystym tekstem identyfikującym obiekt).

Wygląda to na przykład tak:
```dscript_red
- if <player.item_in_hand> == i@diamond_sword:
    - narrate "Masz diamentowy miecz!"
```

Na pierwszy rzut oka wygląda to całkiem nieźle. Jeśli przetestujesz to w grze, prawdopodobnie nawet zadziała. Więc w czym problem?!

#### Nie zawsze to tylko miecz

![](images/bigfancysword.png)

Pierwszym problemem jest to, że nieunikalne obiekty w Denizen <span class="parens">(te identyfikowane przez swoje szczegóły, jak przedmiot, w przeciwieństwie do obiektów identyfikowanych przez ID, jak encje)</span> często zawierają dodatkowe szczegóły w specyficznych okolicznościach, nawet jeśli nie było ich podczas wczesnych testów.

Polecenie if w powyższym przykładzie przestanie działać, gdy tylko gracz użyje trochę swojego miecza, ponieważ zmieni się wartość wytrzymałości (durability) i teraz będzie on miał `i@diamond_sword[durability=1]`. Zmieni się to również, jeśli zaklęje miecz, zmieni mu nazwę w kowadle lub...

#### Zmiany stylu identyfikatorów w Denizen

Drugim problemem jest to, że to, co jest poprawne teraz, może nie być poprawne w przyszłości. Denizen często się zmienia, a sposób identyfikacji obiektów zmienia się między wersjami. Na przykład `<player>` kiedyś zwracał `p@imie`, a teraz zwraca `p@uuid`. Przez lata nastąpiło wiele innych zmian formatu identyfikacji.

Dla powyższego przykładu, miecz, który dziś jest `i@diamond_sword`, może pewnego dnia być `i@diamond_sword[durability=0]` lub `item@diamond_sword` lub `i@item[material=diamond_sword]` lub `diamond_sword[przyszla_statystyka_swiecenia_minecraft=0]` lub dowolną inną możliwością.

#### Więc co mam zrobić?

Opcja pierwsza: tam gdzie to możliwe, używaj dedykowanego narzędzia dopasowującego (matcher). Typ obiektu `ItemTag` definiuje cechę dopasowywalną po nazwie materiału, więc możesz użyć jej z operatorem `matches`:

```dscript_blue
- if <player.item_in_hand> matches diamond_sword:
    - narrate "Masz diamentowy miecz!"
```

Opcja druga: porównuj obiekty w oparciu o tagi o rozsądnie gwarantowanym formacie. Chodzi o tagi, które zwracają element tekstowy o jasno określonym formacie, a nie obiekt. Tag `.material.name` gwarantuje, że zawsze zwróci *tylko* nazwę materiału, więc poniższe porównanie jest bezpieczne przed wszelkimi zmianami szczegółów przedmiotu czy przyszłymi zmianami w Denizen.

```dscript_blue
- if <player.item_in_hand.material.name> == diamond_sword:
    - narrate "Masz diamentowy miecz!"
```

Przy obu tych opcjach jedynym ryzykiem jest zmiana nazwy materiału w przyszłej wersji Minecrafta <span class="parens">(tego trudniej uniknąć – na szczęście dzieje się to rzadko i zwykle wiadomo o tym wcześniej. Jeśli naprawdę chcesz się przed tym zabezpieczyć, możesz użyć `== <item[diamond_sword].material.name>:`, aby polegać na autokonwersji dodanej w Denizen przy zmianie nazwy, ale zazwyczaj nie jest to konieczne)</span>.

Dla innych typów obiektów znajdź odpowiedni unikalny punkt porównania. Dla materiałów, światów, skryptów, wtyczek... możesz użyć `.name`.

Dla zanotowalnych typów obiektów <span class="parens">(lokalizacje, prostopadłościany, elipsoidy...)</span> nigdy nie powinieneś porównywać ich bezpośrednio. Zamiast tego zanotuj obiekt i sprawdzaj `.note_name` – lub używaj bardziej specyficznych składni, takich jak przełącznik `in:<area>` w zdarzeniach.

#### Uwaga o dokładnie tych samych obiektach

Gdy chcesz porównać dokładnie ten sam obiekt <span class="parens">(zazwyczaj dla unikalnych obiektów, takich jak encje, lub w tagach obsługujących listy itp.)</span> i posiadasz tag dla obu <span class="parens">(zamiast wpisywania jednego jako czysty tekst)</span>, możesz stosunkowo bezpiecznie stosować bezpośrednie porównania.

Na przykład możesz dodać `- if <[target]> == <player>:` do [przykładu polecenia `/pay`](#nie-ufaj-graczom), aby uniemożliwić graczom płacenie samym sobie. Byłoby to akceptowalne, ponieważ obie wartości dosłownie odnoszą się teoretycznie do dokładnie tego samego obiektu, więc nie będą miały żadnych różnic w szczegółach, a wszelkie zmiany identyfikatora zostaną automatycznie zastosowane do obu, a nie tylko do jednego.

Pamiętaj jednak, że zapisana kopia obiektu <span class="parens">(np. obiekt gracza zapisany we fladze)</span> może w pewnych przypadkach zachować nieaktualny format w przyszłości, psując porównania. Porównywanie dokładnie tych samych obiektów jest bezpieczne tylko wtedy, gdy oba obiekty są pobierane z prawidłowego tagu źródłowego.

### Nie nadużywaj fallbacków (wartości zapasowych)

Fallbacki są niesamowicie poręcznym narzędziem w Denizen. Są jednym z głównych narzędzi do obsługi niepewnych sytuacji i przypadków granicznych w Twoich skryptach. Jednak, jak większość rzeczy, najlepiej sprawdzają się z umiarem. Nadmierne używanie fallbacków może przynieść więcej szkody niż pożytku.

#### Błędy są straszne

![](images/somanyerrors.png)

Nastawienie, które często prowadzi do nadużywania fallbacków, to przekonanie, że błędy są straszne. Błąd to problem, więc trzeba się go pozbyć wszelkimi możliwymi sposobami!

W rzeczywistości błędy to po prostu kolejne narzędzie dostarczane przez Denizen. Komunikat o błędzie sam w sobie nie jest problemem; błąd jest po to, aby poinformować Cię, że gdzieś w Twoim skrypcie występuje problem. Jeśli dasz fallback do każdego tagu, skończysz na ukrywaniu błędów bez naprawiania rzeczywistego problemu. Twój skrypt nie wyświetli przerażającego czerwonego tekstu w konsoli, ale nie będzie też robił tego, co powinien!

#### Kiedy używać fallbacków

Fallback powinien być umieszczony przy tagu tylko wtedy, gdy *spodziewasz się*, że ten tag może się nie udać.

Rozważmy na przykład tag `server.match_player`, który służy do konwersji wpisanych przez użytkownika imion na obiekt gracza. Możesz całkiem rozsądnie oczekiwać, że czasem gracz wpisze coś, co nie jest poprawnym imieniem gracza i tag zawiedzie. To jest przypadek, w którym zdecydowanie powinieneś dodać fallback typu `.if_null[null]` lub starszy styl `||null`.

Gdy dodajesz fallback, często będziesz też musiał sprawdzić tę wartość zapasową. Dla definicji określonej jako `<server.match_player[<input>].if_null[null]>`, mógłbyś napisać `- if <[target]> == null:` i wewnątrz tego bloku obsłużyć przypadek nieprawidłowego wejścia. W niektórych sytuacjach, gdy potrzebujesz *tylko* sprawdzenia i nie używasz wartości później, możesz po prostu użyć `.exists`, np. `- if <player.item_in_hand.script.exists>:` <span class="parens">(aby sprawdzić, czy trzymany przedmiot jest w ogóle przedmiotem ze skryptu)</span>.

W innych sytuacjach fallback może być po prostu rozsądną wartością domyślną, np. `<player.flag[coins].if_null[0]>`, której nie będziesz musiał specjalnie obsługiwać dodatkowymi poleceniami `if`.

#### Kiedy nie używać fallbacków

Fallbacków nie należy używać, gdy tag nie ma bardzo dobrego powodu, dla którego mógłby zawieść.

Na przykład tag `<player.name>` prawdopodobnie nie powinien mieć fallbacka w większości skryptów <span class="parens">(chyba że jest to skrypt wielokrotnego użytku, który nie wymaga gracza do działania)</span>.

Jeśli skrypt używający `<player.name>` zostanie uruchomiony, a gracz nie będzie dostępny, wyświetli się komunikat o błędzie. To dobrze! Pozwala Ci to dowiedzieć się, że coś poszło nie tak i skrypt został uruchomiony bez gracza, co oznacza, że możesz zbadać przyczynę braku gracza i naprawić to, co go wywołało.

### „Cudzysłów otacza cały argument”

Wielu użytkowników błędnie rozumie, gdzie w poleceniach Denizen stawia się cudzysłowy.

Składnia Denizen jest tak skonstruowana, że linia zaczynająca się od `-` oznacza linię polecenia. Linia polecenia składa się z nazwy polecenia i argumentów. Nazwa polecenia jest pierwsza, a następnie dodawany jest każdy argument, oddzielony spacjami (i niczym innym niż spacje). Zatem np. `- nazwa_polecenia arg1 arg2 arg3` to linia zawierająca polecenie z trzema argumentami.

Gdy musisz użyć spacji wewnątrz argumentu, musisz otoczyć cały ten argument cudzysłowem, aby wskazać, że jest to jeden pojedynczy argument. Na przykład `- narrate "to jest jeden duży argument"` to polecenie `narrate` z tylko jednym argumentem.

Jako inny przykład: `- flag player "my_flag:moja wartosc"`. Jest to polecenie `flag` z dwoma argumentami: `player` oraz `"my_flag:moja wartosc"`. Zauważ, że `moja wartosc` nie jest oddzielnym argumentem od `my_flag`. Dwukropek (`:`) nie jest znakiem oddzielającym argumenty, lecz jedynie wskazuje prefiks do argumentu (który wciąż jest częścią tego argumentu!).

**NIGDY** nie jest poprawne umieszczanie cudzysłowu *wewnątrz* argumentu. `- flag player my_flag:"moja wartosc"` jest całkowicie niepoprawne i uważane za błąd.

Nie należy również stawiać cudzysłowów wokół argumentu, który nie zawiera spacji. Na przykład `- flag "player" "flag:wartosc"` jest pełne zbędnych, bezsensownych cudzysłowów.

Nigdy nie należy też używać cudzysłowów wokół nazwy polecenia czy klucza skryptu. W poniższym przykładzie każdy cudzysłów jest błędny i powinien zostać usunięty.

```dscript_red
"To jest zle":
    "type": "task"
    "script":
    - "flag" player flag:wartosc
```

Oto jak powinno to wyglądać:

```dscript_blue
To jest dobrze:
    type: task
    script:
    - flag player flag:wartosc
```

### Obserwuj konsolę debugowania

![](images/debugconsole.png)

Kiedy piszesz skrypty, powinieneś zawsze mieć otwartą i gotową konsolę debugowania serwera. Kiedy uruchamiasz skrypt, miej tę konsolę na oku. Jeśli pojawi się tam komunikat o błędzie, poinformuje Cię on zarówno o potrzebie poprawki, jak i o tym, *co* dokładnie trzeba naprawić – znacznie szybciej niż próba samodzielnego przeglądania skryptu w poszukiwaniu błędu.

Kiedy użytkownicy przychodzą na Discord wsparcia prosić o pomoc, zwykle prosimy o nagranie debuga (debug recording). Zbyt często wklejają nagranie z jaskrawoczerwonym komunikatem o błędzie, który mówi dokładnie, co poszło nie tak. Gdyby użytkownik po prostu obserwował swoją konsolę i zobaczył ten komunikat, mógłby szybko rozwiązać problem samodzielnie, bez proszenia o pomoc.

Wyjście debugowania pokazuje też wiele informacji niebędących błędami, które są bardzo przydatne podczas pracy nad skryptem. Twój skrypt na wypadanie przedmiotów wyrzuca ich zbyt wiele – dlaczego? Logi debugowania pokażą Ci, że pętla repeat trwa zbyt długo, lub że wartość ilości (quantity) została ustawiona inaczej niż myślałeś, lub cokolwiek innego, co się wydarzyło. Nie jesteś pewien, jaką wartość przyjmie kontekst zdarzenia przy jego wyzwoleniu... logi debugowania Ci to pokażą!

### Zmieniaj ustawienia debugowania z rozwagą

Przede wszystkim: **NIGDY** nie wyłączaj globalnego wyjścia debugowania. Informacje z debuga są niezwykle ważne. Globalne wyłączenie ukryje wszystko, nawet komunikaty o błędach! Zamiast tego po prostu ustaw `debug: false` w skryptach, dla których chcesz przestać wyświetlać debug.

Oto gdzie umieścić `debug: false` w skrypcie:

```dscript_blue
Skrypt bez debuga:
    type: task
    debug: false
    script:
    - define a b
    - (polecenia tutaj)
```

Powinien on znajdować się zawsze tuż pod linią `type:`, na tym samym poziomie wcięcia.

Gdy ustawione jest `debug: false`, jedyne informacje debugowania z tego skryptu, jakie się pojawią, to komunikaty o błędach. Gdy nie jest ustawione, pokazują się wszystkie informacje.

Ogólna zasada:
- Podczas edycji skryptu / pracy nad nim w jakikolwiek sposób, nigdy nie powinieneś mieć wyłączonego debugowania.
- Kiedy skrypt jest W PEŁNI GOTOWY, działa, jest przetestowany i jesteś z niego zadowolony – WTEDY możesz wyłączyć w nim debugowanie.

### Serwery produkcyjne to nie serwery testowe

Pomagając ludziom na Discordzie, czasem słyszymy rzeczy typu „nie mogę teraz zrestartować serwera, gracze są online” lub „o rany, gracz przypadkowo wyzwolił to zdarzenie”.

Jeśli masz AKTYWNY serwer (LIVE) z PRAWDZIWYMI GRACZAMI online, NIE powinieneś pisać na nim skryptów. ZAWSZE [skonfiguruj lokalny serwer testowy](/guides/first-steps/local-test-server) do pisania skryptów i przenoś je na serwer produkcyjny dopiero wtedy, gdy skrypt w pełni działa.

Zbyt łatwo jest spowodować mnóstwo problemów wielu osobom, edytując aktywny serwer. Nie rób tego.

### Jeśli prawda jest prawdą równą prawdziwej prawdzie, to jest to prawda

Sposób działania polecenia `if` w Denizen polega na przetwarzaniu argumentów przy użyciu technik porównania logicznego, a następnie uruchamianiu kodu wewnątrz, jeśli wynik to `true` (prawda), i nieuruchamianiu go, jeśli wynik jest jakikolwiek inny.

Zatem jeśli skrypt robi `- if <jakistag> == true:`, mówisz zasadniczo `jeśli ( prawda == prawda ) == prawda:` ... co jest dość niedorzeczne, prawda?

**NIGDY** nie wpisuj `== true` do polecenia `if` <span class="parens">(ani `while`, ani żadnego podobnego)</span>. Jest to zawsze zbędne.

Nie wpisuj też `== false`. Zamiast tego, aby zanegować sprawdzenie, użyj `!`. Na przykład `- if !<jakis tag>:` lub `- if <jakis tag> != jakaswartosc:`.

### Tekst to nie tag

Użytkownicy nowi w Denizen często błędnie odczytują dokumentację, np. widząc `<#>` w składni polecenia, myślą, że `<3>` jest poprawnym wejściem.

Nie jest to poprawne. Jak wyjaśnia [opis składni poleceń](https://meta.denizenscript.com/Docs/Languages/command%20syntax), w tym kontekście `<>` oznacza „wstaw tutaj wartość”. Znaki `<>` nie mają być dołączane dosłownie.

Zatem jeśli polecenie mówi, że jego składnia to `- heal [<#>]`, poprawne wejście mogłoby wyglądać tak: `- heal 3`.

Głównym powodem tego nieporozumienia jest fakt, że `<>` jest faktycznie często używane w skryptach Denizen do tworzenia tagów. Należy pamiętać, że tagi nigdy nie są dosłowne – są instrukcją dla silnika skryptowego, aby poszedł i znalazł jakąś inną wartość. Tag `<player.money>` na przykład nie oznacza wstawienia czystego tekstu „player.money” do polecenia, lecz znalezienie kwoty pieniędzy, jaką posiada gracz, i wstawienie tej wartości. Zatem `- heal <player.money>` w skrypcie przetworzyłoby wartość tagu i ostatecznie wykonało polecenie typu `- heal 3` <span class="parens">(jeśli gracz miałby wtedy akurat $3)</span>.

Jeśli w dowolnym momencie chcesz wstawić dosłowny tekst: po prostu go wstaw, bez niczego więcej. `- heal 3` lub `- narrate mojtekst` to całkowicie poprawne sposoby zapisu argumentów, bez potrzeby używania specjalnych symboli <span class="parens">(z wyjątkiem cudzysłowów wymaganych do zawarcia spacji w argumencie)</span>.

Dodatkowo, jeśli potrzebujesz dosłownego tekstu w formie tagu <span class="parens">(aby użyć na nim jakiegoś sub-tagu elementu)</span>, możesz użyć tagu bazowego `element`, np.: `<element[3].div[5]>` <span class="parens">(bierze to surową wartość „3”, tworzy z niej ElementTag, a następnie używa sub-tagu `ElementTag.div`, aby podzielić ją przez 5)</span>.

### Polecenie Adjust nie służy do przedmiotów/materiałów/itp.

Wielu użytkowników, próbując po raz pierwszy zmienić mechanizm na przedmiocie, materiale lub podobnym typie, próbuje użyć do tego polecenia `adjust`, np. `- adjust <[inventory].slot[5]> "lore:Mój nowy opis!"` lub `- adjust <[location].material> lit:true`.

Choć początkowo wydaje się to mieć sens, niestety nie zadziała ze względu na ważne rozróżnienie między typami obiektów: [obiekty unikalne vs generyczne](https://meta.denizenscript.com/Docs/Languages/Unique%20Objects%20vs%20Generic%20Objects). Zaleca się przeczytanie i zrozumienie tamtej strony wyjaśniającej, aby pojąć, dlaczego nie można użyć `adjust` na „przedmiocie” (item), ale krótki opis brzmi: ItemTagi wyglądają jak `stick` (patyk), co jest *opisem* przedmiotu, a nie pojedynczym unikalnym przedmiotem. W rezultacie system nie ma możliwości ustalenia, *który* patyk próbujesz zmienić.

Jeśli użyjesz `adjust` na przedmiocie, nałoży on modyfikację na opis tego przedmiotu i zapisze zmodyfikowany opis do zapisu (save entry). Podobny efekt występuje z obiektem MaterialTag. Choć może to być przydatne w niektórych przypadkach, nie jest pomocne, gdy chcesz zmienić konkretny przedmiot istniejący w świecie.

#### Więc jak mam zmienić konkretny przedmiot?

Sposób poprawnej zmiany konkretnego przedmiotu zależy od tego, gdzie ten przedmiot się znajduje. Jeśli jest w ekwipunku, najlepiej użyć polecenia `inventory` z argumentami `adjust` oraz `slot:<#>` <span class="parens">(np. `- inventory adjust slot:5 "lore:Mój nowy opis!"`)</span>. W innych przypadkach przydatny jest tag `ItemTag.with[...]`. Zwraca on kopię przedmiotu z nałożonym mechanizmem. Jeśli więc np. masz encję `dropped_item`, możesz zmienić mechanizm `item` na tej encji na wynik tagu `with`, np.: `- adjust <[entity]> "item:<[entity].item.with[lore=mój nowy opis!]>"`. Aby zmienić przedmiot w zdarzeniu, możesz również użyć `determine` wraz z tagiem `ItemTag.with`.

Do zmiany `MaterialTag` służy tag `MaterialTag.with[...]`, który działa analogicznie do wersji dla przedmiotów. Najprawdopodobniej jednak chcesz zmienić materiał bloku w świecie – wtedy potrzebujesz polecenia `adjustblock`. Przyjmuje ono lokalizację bloku i nakłada mechanizmy MaterialTag na ten konkretny blok <span class="parens">(np. `- adjustblock <[location]> lit:true`)</span>.

Ta sama logika dotyczy flagowania przedmiotów – nie używaj polecenia `flag`, lecz `inventory flag` <span class="parens">(np. `- inventory flag slot:5 mojaflaga:wartosc`)</span> lub tagu `with_flag`.

### Nie wpisuj surowych lokalizacji w skryptach

Na Discordzie Denizen często padają pytania typu „jak wpisać współrzędne dla lokalizacji” lub „jak sprawić, by NPC szedł do x,y,z 1,5,7”. Czasem formułowane jest to jako „jak podać surowe wartości współrzędnych zamiast używać LocationTag”.

Krótka odpowiedź: nie rób tego.

Z punktu widzenia czystego i poprawnego skryptowania, nigdy nie ma sensu wpisywanie współrzędnych świata bezpośrednio do skryptu zamiast użycia tagu do pobrania lokalizacji.

#### Ale co, jeśli nie ma tagu dla lokalizacji, której chcę?

To go stwórz! Tagi Denizen nie są nieruchomymi głazami. Są narzędziami i pracują dla Ciebie, nie przeciwko Tobie.

Jeśli na przykład masz ozdobny filar z obsydianu w centrum swojej areny i potrzebujesz, aby skrypty korzystały z jego lokalizacji... po prostu stań na szczycie filaru i wpisz `/ex note <player.location.below> as:filar_arena1`. Skoro to zrobiłeś, każdy skrypt potrzebujący tej lokalizacji może dosłownie wpisać `filar_arena1` jako lokalizację. Potrzebujesz, by NPC spojrzał na filar? `- look filar_arena1`. Potrzebujesz pobrać dokładną wysokość Y filaru? `<location[filar_arena1].y>`. Czysto, opisowo i łatwo!

Jeśli na przykład masz NPC, który musi chodzić do konkretnych punktów na ścieżce, możesz wtedy użyć punktów zakotwiczenia (anchors). Zaznacz NPC, następnie stań w każdym punkcie ścieżki i wpisz `/npc anchor --save punkt1` <span class="parens">(potem `punkt2`, `3` itd.)</span>. Wtedy skrypt może zrobić `- ~walk <npc.anchor[punkt1]>` <span class="parens">(i potem `punkt2`, `3` itd.)</span>.

Jeśli potrzebujesz lokalizacji, która zmienia się od czasu do czasu, lub jest wybierana z listy możliwości, albo jest przypisana do gracza zamiast do NPC... w takim przypadku zapisz lokalizację we fladze.

### Nie wpisuj surowej notacji obiektów

Denizen używa notacji obiektów wewnętrznie do śledzenia typów. Na przykład `l@` wskazuje, że wartość to lokalizacja, `p@` wskazuje gracza itp.

Jest to przeznaczone wyłącznie do wewnętrznego śledzenia generowanych wartości. Skrypt NIGDY nie powinien zawierać tych wpisanych ręcznie wartości notacji.

Zamiast wpisywać notację obiektu, użyj jednej z tych trzech opcji:
- Po prostu ją pomiń. Często możesz podać wartość bez określania typu w jakikolwiek sposób i to po prostu zadziała <span class="parens">(sprawdź [Nie nadużywaj tagów konstruktorów](#nie-naduzywaj-tagow-konstruktorow))</span>.
- Użyj tagu, który zwraca odpowiednią wartość, zamiast próbować podawać surową wartość <span class="parens">(sprawdź [Nie wpisuj surowych lokalizacji w skryptach](#nie-wpisuj-surowych-lokalizacji-w-skryptach))</span>.
- Użyj tagu konstruktora, gdy jest to wymagane <span class="parens">(sprawdź [Nie nadużywaj tagów konstruktorów](#nie-naduzywaj-tagow-konstruktorow))</span>.

### Nie nadużywaj tagów konstruktorów

Tagi konstruktorów to bazy tagów, które służą do automatycznej konwersji surowej wartości na odpowiedni typ obiektu. Na przykład `<cuboid[jakas wartosc tutaj]>` to baza tagu konstruktora dla CuboidTags.

Mogą być one przydatne w przypadkach, gdy masz surową wartość, która z jakiegoś powodu nie zna swojego typu, a musisz użyć na niej tagu. Na przykład, jeśli definicja `ent_id` zawiera UUID encji, a chcesz pobrać typ tej encji, możesz użyć `<entity[<[ent_id]>].entity_type>`.

Mogą być też przydatne, gdy wymagana jest dokładna specyfika typu obiektu, a nie jest ona automatycznie dostępna, np. w poleceniu `note`. Na przykład, jeśli masz skrypt ekwipunku o nazwie `moj_skrypt_ekw` i chcesz zanotować ekwipunek jako osobistą kopię gracza, możesz użyć `- note <inventory[moj_skrypt_ekw]> as:moj_ekw_<player.uuid>`.

Tagi konstruktorów są jednak w większości niepotrzebne i nadużywane czynią skrypty mniej czytelnymi i bardziej skomplikowanymi bez dobrego powodu.

Często widzimy niepotrzebne nadużywanie tych tagów, na przykład w liniach takich jak `- give <item[stick]>`, co oczywiście powinno brzmieć po prostu `- give stick`. Parser skryptów Denizen jest dość sprytny i w większości przypadków wie, o jaki typ obiektu chodzi. Polecenie `give` w tym przykładzie oczywiście wie, że próbujesz dać przedmiot <span class="parens">(co innego mógłbyś dawać, nie pasując do specyfikatora innego argumentu?)</span>, więc nie musisz mu o tym mówić.

### Object Hacking (hakowanie obiektów) to zły pomysł

Denizen posiada standardowe formaty dla większości typów obiektów. Na przykład ItemTagi wyglądają jak `i@stick[lore=Fajny patyk]`.

Formaty te służą do celów śledzenia wewnętrznego i tak powinny być traktowane. Są to generowane wartości wewnętrzne, nieprzeznaczone do manipulacji poprzez skrypty.

Czasami widzimy użytkowników próbujących rzeczy typu `<player.item_in_hand>[lore=Fajny patyk]`. Ten przykład, jeśli gracz trzyma patyk, stworzy nowy przedmiot będący patykiem z takim opisem, dokładnie tak, jak chciał użytkownik. Jeśli jednak trzymany przez gracza przedmiot posiada już jakiekolwiek dodatkowe dane, skończy się to przetworzeniem na coś w stylu `i@stick[display_name=Najlepszy patyk][lore=Fajny patyk]`, co nie pasuje już do standardowego formatu i w rezultacie nie zadziała. <span class="parens">(Dla odniesienia: poprawnym sposobem – bez hakowania obiektów – na uzyskanie kopii przedmiotu z dodatkowymi mechanizmami byłoby `<player.item_in_hand.with[lore=Fajny patyk]>`)</span>.

Warto też pamiętać, że standardowe formaty *zmieniają się*, gdy obiekt bazowy potrzebuje przechowywać inne formy danych wewnętrznych. Na przykład MaterialTag był pierwotnie tworzony jako `m@chest` lub `m@chest,2`, ale teraz ma format bardziej zbliżony do `m@chest[direction=north]`. Choć stare dane zapisane w starym formacie będą zazwyczaj wciąż poprawnie przetwarzane <span class="parens">(przynajmniej przez pewien czas po zmianie)</span>, wszelkie skrypty oczekujące konkretnego formatu natychmiast przestaną działać.

Najczęstszym ostatnio przykładem „hakowania obiektów”, który mści się na użytkownikach, jest hakowanie ListTagów. Oryginalny format ListTag to `li@jeden|dwa|trzy`, który wciąż działa idealnie jako wejście, ale nie jest już standardowym formatem wyjściowym. Wielu użytkowników miało skrypty z liniami w rodzaju `<jakas_lista>|dodatkowa|wartosc|tutaj`, co działało przy starym formacie. Format został jednak zmieniony, aby pozwolić na rzeczy takie jak sub-listy poprzez wbudowaną logikę ucieczki (escaping), i wyróżnia się dodatkowym znakiem `|` na końcu. Zmiana ta pozwoliła też na istnienie pustych wpisów w ListTagach. Skutkowało to tym, że skrypty „hakujące obiekty” miały w liście dane z ucieczką, ale dla parsera wyglądały jak stary format listy, przez co ucieczka nie była przetwarzana i nagle zhakowane listy miały uszkodzone dane. <span class="parens">(Dla odniesienia: poprawnym sposobem – bez hakowania obiektów – na dodanie wpisów do listy jest `<jakas_lista.include[dodatkowa|wartosc|tutaj]>`)</span>.

Lekcja z tego płynąca: nigdy nie zakładaj, że pełny format obiektu będzie zawsze taki sam. Zawsze istnieje tag do odczytu lub zmiany dowolnych danych wewnątrz obiektu w lepszy sposób.

### Ekwipunki w trybie kreatywnym (Creative) są po stronie klienta

Wielu administratorów serwerów zostaje w trybie kreatywnym podczas pracy, a nawet podczas testowania rzeczy przeznaczonych dla graczy w trybie przetrwania (survival). Choć zwykle nie ma z tym problemów, istnieją przypadki, w których różnice między trybami mogą Ci zaszkodzić. Niektóre są oczywiste <span class="parens">(np. nie możesz przetestować skryptu, który zadaje Ci obrażenia, jeśli nie możesz ich otrzymywać)</span>, inne wcale takie nie są. Nieoczywistym przypadkiem, który najczęściej dezorientuje skrypciarzy, są **ekwipunki.**

Normalnie, w trybie przetrwania <span class="parens">(lub przygodowym)</span>, ekwipunki są *po stronie serwera* (serverside). Oznacza to, że serwer ma ostateczny głos w kwestii tego, które przedmioty gdzie się znajdują, a to dalej oznacza, że wszelkie skrypty lub wtyczki po stronie serwera mogą modyfikować i kontrolować każdy ekwipunek czy interakcję z nim i ufać, że to zadziała.

Jednak będąc w trybie **kreatywnym**, ekwipunki są *po stronie klienta* (clientside). Oznacza to, że klient <span class="parens">(kod działający na komputerze gracza, czy to czysty klient Minecraft, czy modowany)</span> ma ostateczny głos w kwestii szczegółów ekwipunku. Choć serwery wciąż mogą wprowadzać własne zmiany w ekwipunkach czy interakcjach, klient może te zmiany odrzucić. Prowadzi to do sytuacji takich jak próba anulowania zdarzenia kliknięcia, która skutkuje zduplikowaniem przedmiotu <span class="parens">(serwer powiedział: „nie, A: nie podnosisz przedmiotu i B: ten przedmiot wciąż jest w swoim pierwotnym slocie”... klient decyduje: „odrzucam A, faktycznie podniosłem przedmiot, jest mój, ale zgadzam się na B, ten przedmiot może wciąż być w swoim slocie” – czyli mamy teraz dwie kopie tego samego przedmiotu)</span>.

Powiązaną częścią tego systemu, o której warto pomyśleć, jest to, że gracze w trybie kreatywnym mają możliwość przywołania dowolnego przedmiotu. Na czystym kliencie Minecraft oznacza to, że mogą albo A: wziąć dowolny nowy podstawowy przedmiot lub ich stos z listy przedmiotów kreatywnych w dowolnym momencie, albo B: stworzyć idealną kopię dowolnego przedmiotu, który widzą, poprzez kliknięcie na niego środkowym przyciskiem myszy <span class="parens">(może to być np. użyte do zdobycia specjalnego przedmiotu ze skryptu GUI do własnego ekwipunku)</span>. Modowany klient mógłby jednak potencjalnie przywołać *dowolny* przedmiot, nawet taki z własnymi danymi NBT. Warto o tym pamiętać za każdym razem, gdy wiążesz jakąś moc lub system z danymi na przedmiocie. Rozważ poniższy skrypt:

```dscript_red
niebezpieczne_narzedzia:
    type: world
    events:
        after player right clicks block with:powertool_item:
        - execute as_server <player.item_in_hand.flag[powertool_command]>

powertool_item:
    type: item
    material: stick
    flags:
        powertool_command: broadcast To Dziala!
```

Na pierwszy rzut oka skrypt ten pozwala na tworzenie prostych „powertoolów” wykonujących własne polecenia... jednak ponieważ użyto 'as_server' z danymi przypisanymi do przedmiotu, oznacza to, że gracz w trybie kreatywnym mógłby wygenerować przedmiot z dowolnym poleceniem i ominąć wszelkie wymagania uprawnień <span class="parens">(źle nastawiony gracz w trybie kreatywnym mógłby to wykorzystać, by nadać sobie opa, zbanować właściciela serwera itp.)</span>.

### Polecenie Ex służy wyłącznie do testów

Niektórzy użytkownicy próbowali używać `/ex` jako ogólnego narzędzia skryptowego – ale to nie tak. Jest ono zaprojektowane wyłącznie do testowania i debugowania.

Innymi słowy: jedynym miejscem, w którym powinno pojawiać się `/ex`, jest Twój pasek czatu w grze. Nigdy nie powinno być umieszczane w skrypcie, bloku poleceń, poleceniu innej wtyczki itp.

Jednym z najczęstszych sposobów nadużywania `/ex` jest umieszczanie go w konfiguracji innej wtyczki jako polecenia wyzwalanego, np. wtyczki do sklepów skonfigurowanej tak, by wyzwalała `/ex run nazwa_mojego_zadania player:%player%` przy zakupie przedmiotu. Jest to zły sposób postępowania – za każdym razem, gdy gracz wyzwoli tę opcję, uruchamiany jest cały silnik parsowania, przygotowywane jest wyjście debugowania i tak dalej. Co powinieneś zrobić zamiast tego? To proste: stwórz skrypt typu `command`! Niech skrypt polecenia robi wszystko, co trzeba, a następnie dodaj nazwę tego polecenia do tamtej zewnętrznej wtyczki jako polecenie do wyzwolenia. <span class="parens">(Albo, oczywiście, możesz też po prostu całkowicie zastąpić tę zewnętrzną wtyczkę skryptem Denizen, jeśli masz na to ochotę!)</span>
