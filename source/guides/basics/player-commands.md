Obsługa poleceń gracza
------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czym są polecenia gracza?

Polecenie gracza to komenda, którą gracz wpisuje na czacie, aby wykonać określone zadanie. Mogą one obejmować zarówno zabawne interakcje, jak i narzędzia moderacyjne, i są spotykane w całym świecie Minecrafta. Denizen pozwala na tworzenie własnych poleceń, aby jeszcze bardziej wzbogacić Twój serwer. Ta strona przeprowadzi Cię przez proces tworzenia podstawowego polecenia „warp” – komendy, która pozwala użytkownikowi ustawiać punkty, do których inni mogą się teleportować. Przykład ten zilustruje, jak tworzyć własne polecenia oraz na jakie funkcje warto zwrócić uwagę podczas ich projektowania.

### Podstawowa struktura

Na początek: wszystkie własne polecenia w Denizen wymagają tej samej podstawowej struktury. Jest ona podobna do skryptów typu `task`, które widziałeś do tej pory, jednak zawiera kilka dodatkowych wymaganych szczegółów:

```dscript_blue
NazwaKonteneraSkryptu:
    type: command
    name: [nazwaPolecenia]
    description: [krótki opis Twojego polecenia]
    usage: [opis sposobu użycia]
    permission: [klucz uprawnienia]
    script:
    - (polecenia tutaj)
```

W [edytorze skryptów](/guides/first-steps/script-editor) możesz po prostu wpisać `command` i użyć autouzupełniania (klawisz Tab), aby w dowolnym momencie wygenerować szablon skryptu polecenia.

Oprócz określenia typu skryptu jako `command`, musisz również podać nazwę polecenia (`name`). To jest to, co użytkownik będzie wpisywał na czacie, aby uruchomić polecenie <span class="parens">(bez ukośnika `/` na początku)</span>.

Musisz również podać opis (`description`) oraz tekst użycia (`usage`). Służą one głównie domyślnemu poleceniu `/help`.

Zazwyczaj powinieneś również określić węzeł uprawnienia (`permission`) – na razie może to być dowolny wymyślony przez Ciebie klucz. Prawdopodobnie jesteś opem na swoim serwerze testowym, więc i tak ominiesz wymóg posiadania uprawnień.

Zacznijmy więc nasz skrypt polecenia od wypełnienia wymaganych pól:
```dscript_green
warp_command:
    type: command
    name: warp
    description: Teleportuje cię do magicznych miejsc.
    usage: /warp
    permission: dscript.warp
    script:
    - narrate "<&[base]>Tutaj znajdzie się treść mojego polecenia!"
```
Wypróbuj to w grze, wpisując `/warp`. Powinno to po prostu wyświetlić tekst `Tutaj znajdzie się treść mojego polecenia!`. Daleko nam jeszcze do zamierzonego celu, ale nasze własne polecenie działa w tym sensie, że coś robi.

![](images/warp_command_example_1.png)

### Obsługa argumentów gracza

Polecenie to często coś więcej niż tylko sama nazwa. Choć niektóre podstawowe komendy mogą takie być, często polecenie oczekuje od gracza dodatkowych szczegółów. W naszym przypadku z poleceniem warp chcemy tworzyć punkty teleportacji oraz podróżować do nich. Na przykład chciałbym użyć `/warp create spawn`, aby stworzyć nowy punkt o nazwie spawn. W tym przypadku `create` oraz `spawn` byłyby argumentami.

Podobnie jak zdarzenia, skrypt polecenia posiada kilka tagów `context`, które przechowują informacje o tym, jak gracz użył polecenia. Jednym z nich jest `<context.args>`, który zawiera listę wszystkich argumentów użytych przez gracza.

Nadajmy naszemu poleceniu funkcjonalność, a mianowicie tworzenie nowych punktów warp oraz możliwość teleportacji do nich:
```dscript_yellow
warp_command:
    type: command
    name: warp
    description: Teleportuje cię do magicznych miejsc.
    usage: /warp create|goto [nazwa-warpu]
    permission: dscript.warp
    script:
    - choose <context.args.first>:
        - case create:
            - define name <context.args.get[2]>
            - flag server warps.<[name]>:<player.location>
            - narrate "<&[base]>Stworzono warp <&[emphasis]><[name]><&[base]>!"
        - case goto:
            - define name <context.args.get[2]>
            - teleport <player> <server.flag[warps.<[name]>]>
            - narrate "<&[base]>Przeniesiono do <&[emphasis]><[name]><&[base]>!"
```
Wypróbuj to, wpisując `/warp create test`, następnie odejdź kawałek i wpisz `/warp goto test`. Powinieneś wrócić do pierwszej lokalizacji, w której ustawiłeś warp.

Przeanalizujmy, co się dzieje:
- Sprawdzamy pierwszy podany argument.
- Jeśli tym argumentem jest `create`, tworzymy flagę na serwerze o nazwie opartej na drugim argumencie.
- W przeciwnym razie, jeśli pierwszym argumentem jest `goto`, teleportujemy gracza do lokalizacji zapisanej we fladze określonej przez drugi argument.

Możesz zauważyć, że jeśli gracz wpisze cokolwiek innego niż polecenie dokładnie tak, jak chcieliśmy, skrypt się zepsuje. Więcej o tym, jak sobie z tym radzić, dowiesz się niżej.

Zauważ, że zaktualizowaliśmy również tekst `usage`. Choć nie wpływa to na samo działanie skryptu, dobrze jest dbać o dokładność dokumentacji.

Zauważysz też, że polecenie `narrate` wykorzystuje wiele kolorów <span class="parens">(spośród standardowych kolorów wybranych w pliku `config.yml` wtyczki Denizen)</span>, aby kluczowe części wiadomości były wyraźnie widoczne. Takie małe detale sprawiają, że Twoje polecenia są bardziej przyjazne dla graczy.

#### Obsługa argumentów wskazujących na gracza

Częstą funkcją wielu poleceń jest możliwość zrobienia czegoś komuś innemu, co wymaga podania imienia tej osoby. Na przykład w naszym poleceniu warp możemy chcieć dodać funkcjonalność teleportowania kogoś innego zamiast tylko siebie.

Załóżmy, że chcemy, aby polecenie wyglądało tak: `/warp player (nazwa gracza) (cel)`. Wynikowy skrypt wyglądałby teraz mniej więcej tak:
```dscript_yellow
warp_command:
    type: command
    name: warp
    description: Teleportuje cię do magicznych miejsc.
    usage: /warp create [nazwa-warpu] | goto [nazwa-warpu] | player [gracz] [nazwa-warpu]
    permission: dscript.warp
    script:
    - choose <context.args.first>:
        - case create:
            - define name <context.args.get[2]>
            - flag server warps.<[name]>:<player.location>
            - narrate "<&[base]>Stworzono warp <&[emphasis]><[name]><&[base]>!"
        - case goto:
            - define name <context.args.get[2]>
            - teleport <player> <server.flag[warps.<[name]>]>
            - narrate "<&[base]>Przeniesiono do <&[emphasis]><[name]><&[base]>!"
        - case player:
            - define playerName <context.args.get[2]>
            - define destination <context.args.get[3]>
            - define playerToWarp <server.match_player[<[playerName]>]>
            - teleport <[playerToWarp]> <server.flag[warps.<[destination]>]>
            - narrate "<&[base]>Przeniesiono gracza <&[emphasis]><[playerToWarp].name> <&[base]>do <&[emphasis]><[destination]><&[base]>!"
```
Wypróbuj to na kimś innym na serwerze, a jeśli testujesz lokalnie (localhost), możesz przetestować to, podając własne imię.

Zauważ, że w poleceniu teleport nie używamy bezpośrednio podanego argumentu, lecz tagu `<server.match_player[]>`, aby znaleźć obiekt gracza o danej nazwie. To ważny krok – o ile w Denizen istnieją inteligentne konwertery, którym możesz po prostu podać nazwę rzeczy i one pobiorą właściwy obiekt (np. materiały), o tyle w przypadku graczy to nie działa. Dodatkowo `<server.match_player[]>` pozwala na nieprecyzyjne wejścia, np. jeśli na serwerze jest ktoś o imieniu `bobby`, polecenie `<server.match_player[bob]>` zadziała <span class="parens">(zakładając, że nie ma nikogo o imieniu `bob`)</span>.

![](images/warp_command_example_2.png)

### Dodawanie autouzupełniania (Tab Completions)

Fajnym sposobem na ulepszenie skryptów poleceń jest dodanie autouzupełniania – systemu w Minecraft do automatycznego kończenia argumentów klawiszem Tab. Aby to osiągnąć, możesz dodać następującą sekcję:
```dscript_blue
warp_command:
    type: command
    name: warp
    description: Teleportuje cię do magicznych miejsc.
    usage: /warp create [nazwa-warpu] | goto [nazwa-warpu] | player [gracz] [nazwa-warpu]
    permission: dscript.warp
    tab completions:
        1: create|goto|player
        2: <context.args.first.equals[goto].if_true[<server.flag[warps].keys>].if_false[]>
        3: <context.args.first.equals[player].if_true[<server.flag[warps].keys>].if_false[]>
    script:
    #...tutaj nic się nie zmieniło
```
Wypróbuj to – podczas wpisywania polecenia zobaczysz menu autouzupełniania Minecrafta.

Działa to tak, że gdy gracz wpisuje pierwszy argument, wyświetlane są sugestie `create`, `goto` oraz `player`, z podświetleniem tej najlepiej dopasowanej.

Gdy gracz wpisuje drugi argument, jeśli pierwszym argumentem było `goto`, skrypt pobierze wszystkie klucze zapisane we fladze warpów, w przeciwnym razie nic nie zostanie zasugerowane. Podobnie dzieje się dla trzeciego argumentu, ale sprawdzając, czy pierwszym argumentem było `player`.

![](images/warp_command_tabcomplete.png)

Warto pamiętać, że autouzupełnianie nie zapewnia żadnej dodatkowej funkcjonalności skryptu poza dawaniem sugestii graczowi. Wciąż musisz napisać resztę skryptu, aby obsłużyć podane argumenty.

Możesz zauważyć w tym punkcie, że dodanie autouzupełniania dla imion graczy w podpoleceniu 'player' wymagałoby bardzo długiej linii dla argumentu nr 2 – napotykanie takich barier to pierwszy sygnał, że polecenie staje się zbyt skomplikowane i warto rozważyć podzielenie go na kilka mniejszych <span class="parens">(na przykład oddzielny skrypt polecenia `createwarp [nazwa]` i oddzielny `warpto [nazwa] (gracz)`)</span>.

### Nie ufaj graczowi

Skrypt w obecnej formie technicznie działa dobrze, zakładając, że wszyscy używają go prawidłowo. Nie ma jednak gwarancji, że tak będzie. Prowadzi to do ważnego tematu: *nigdy nie ufaj graczowi*. Nie zakładaj, że gracz zawsze zrobi to, co chcesz lub czego od niego oczekujesz.

Niektórzy gracze mogą próbować oszukiwać lub wykorzystywać luki w systemie, inni mogą po prostu popełniać błędy. Ludzie są niedoskonali i Twój skrypt polecenia musi być na to przygotowany.

Z naszego prostego polecenia może wyniknąć szereg problemów, takich jak:
- Co jeśli ktoś spróbuje przenieść się do warpu, który nie istnieje?
- Co jeśli ktoś spróbuje stworzyć warp, który już istnieje?
- Co jeśli ktoś zapomni podać argumenty?
- Co jeśli ktoś poda zbyt wiele argumentów?
- Co jeśli ktoś poda nieprawidłowy argument?
- Co jeśli gracz, którego wskazałeś, nie istnieje?
- Co jeśli nazwa warpu zawiera niekompatybilne znaki, takie jak `.` lub spację?

![](images/warp_command_errors.png)

Choć niektóre problemy mogą być w większości niegroźne <span class="parens">(jak podanie zbyt wielu argumentów)</span>, inne mogą powodować błędy w skrypcie <span class="parens">(jak brakujący warp)</span> lub psuć coś innego <span class="parens">(jak nadpisanie istniejącego warpu)</span>. We wszystkich tych przypadkach ważne jest, abyś przechwytywał te błędy graczy, dbając o to, by wszystko działało jak najsprawniej dla każdego.

Zobaczmy, jak wygląda nasz skrypt z gruntownym sprawdzaniem błędów:
```dscript_green
warp_command:
    type: command
    name: warp
    description: Teleportuje cię do magicznych miejsc.
    usage: /warp create [nazwa-warpu] | goto [nazwa-warpu] | player [gracz] [nazwa-warpu]
    permission: dscript.warp
    tab completions:
        1: create|goto|player
        2: <context.args.first.equals[goto].if_true[<server.flag[warps].keys.parse[unescaped].if_null[]>].if_false[]>
        3: <context.args.first.equals[player].if_true[<server.flag[warps].keys.parse[unescaped].if_null[]>].if_false[]>
    script:
    # Jeśli polecenie zostanie użyte bez wejścia, gracz prawdopodobnie nie wie, co zrobić
    # Więc mu to powiedz
    - if <context.args.is_empty>:
        - narrate "<&[error]>/warp create [nazwa-warpu]"
        - narrate "<&[error]>/warp goto [nazwa-warpu]"
        - narrate "<&[error]>/warp player [gracz] [nazwa-warpu]"
        - stop
    - choose <context.args.first>:
        - case create:
            # Sprawdź liczbę argumentów
            - if <context.args.size> < 2:
                - narrate "<&[error]>Musisz podać nazwę warpu!"
                - stop
            - else if <context.args.size> > 2:
                - narrate "<&[error]>Podałeś zbyt wiele argumentów!"
                - stop
            - define name <context.args.get[2]>
            # Nie pozwól użytkownikom nadpisywać istniejących warpów
            # Użyj ".escaped", aby dziwne symbole w nazwie nie powodowały problemów
            - if <server.has_flag[warps.<[name].escaped>]>:
                - narrate "<&[error]>Warp o nazwie <&[emphasis]><[name]><&[error]> już istnieje!"
                - stop
            # To oznacza, że będziesz potrzebować też 'warp delete [nazwa]'
            - flag server warps.<[name].escaped>:<player.location>
            - narrate "<&[base]>Stworzono warp <&[emphasis]><[name]><&[base]>!"
        - case goto:
            - if <context.args.size> < 2:
                - narrate "<&[error]>Musisz określić, dokąd chcesz się przenieść!"
                - stop
            - else if <context.args.size> > 2:
                - narrate "<&[error]>Podałeś zbyt wiele argumentów!"
                - stop
            - define name <context.args.get[2]>
            # Upewnij się, że warp istnieje
            - if !<server.has_flag[warps.<[name].escaped>]>:
                - narrate "<&[error]>Warp o nazwie <&[emphasis]><[name]> <&[error]>nie istnieje!"
                - stop
            - teleport <player> <server.flag[warps.<[name].escaped>]>
            - narrate "<&[base]>Przeniesiono do <&[emphasis]><[name]><&[base]>!"
        - case player:
            - if <context.args.size> < 3:
                - narrate "<&[error]>Musisz podać gracza oraz cel teleportacji!"
                - stop
            - else if <context.args.size> > 3:
                - narrate "<&[error]>Podałeś zbyt wiele argumentów!"
                - stop
            - define playerName <context.args.get[2]>
            - define destination <context.args.get[3]>
            # Użyj fallbacka, aby przechwycić błędną nazwę gracza
            - define playerToWarp <server.match_player[<[playerName]>].if_null[null]>
            - if <[playerToWarp]> == null:
                - narrate "<&[error]>Nie można znaleźć gracza o nazwie '<&[emphasis]><[playerName]><&[error]>'!"
                - stop
            - if !<server.has_flag[warps.<[destination].escaped>]>:
                - narrate "<&[error]>Warp o nazwie <&[emphasis]><[destination]> <&[error]>nie istnieje!"
                - stop
            - teleport <[playerToWarp]> <server.flag[warps.<[destination].escaped>]>
            - narrate "<&[base]>Przeniesiono gracza <&[emphasis]><[playerToWarp].name> <&[base]>do <&[emphasis]><[destination]><&[base]>!"
        # Obsłuż nieprawidłowy pierwszy argument prostym komunikatem o błędzie dla gracza
        - default:
            - narrate "<&[error]>Nieznany argument '<&[emphasis]><context.args.first><&[error]>'!"
```
Wypróbuj to teraz, celowo podając błędne dane, i zobacz, jak polecenie Cię powstrzymuje.

Niektóre środki bezpieczeństwa zastosowane tutaj:
- Sprawdzanie rozmiaru listy argumentów, aby zweryfikować, czy gracz faktycznie wpisał dany argument.
- Sprawdzanie, czy serwer posiada daną flagę, aby uniknąć konfliktów.
- Zatrzymywanie skryptu w przypadku wykrycia problemu oraz informowanie użytkownika, co jest nie tak.
- Dodanie sprawdzenia `null` do autouzupełniania, aby w przypadku braku warpów Denizen nie wyświetlał błędów.
- Dodanie sprawdzenia `null` do tagu `match_player`, aby zweryfikować, czy gracz faktycznie istnieje.
- Użycie tagu `.escaped`, aby zagwarantować, że symbole wpływające na składnię (jak `.`) nie zostaną wstawione bezpośrednio do nazwy flagi.

Choć rozmiar skryptu znacznie wzrósł, jest to niezbędny krok dla poleceń i ogólnie dla interfejsów gracza. Możesz przeczytać więcej o tym na stronie [Częste błędy (Common Mistakes)](/guides/troubleshooting/common-mistakes#don-t-trust-players).

![](images/warp_command_safe.png)

### Chwila, czy mogę zobaczyć Twoje uprawnienia?

Oprócz zwykłej weryfikacji, czy gracz poprawnie wpisał argumenty, chcesz również upewnić się, że ma on uprawnienia do użycia polecenia. Domyślnie Minecraft nie posiada systemu uprawnień <span class="parens">(poza nadawaniem statusu operatora)</span>, jednak istnieje wiele wtyczek, które pomagają nimi zarządzać – jeśli prowadzisz już serwer, prawdopodobnie masz taką zainstalowaną.

Pierwszym etapem sprawdzania uprawnień jest sam dostęp do polecenia. Obsługuje to linia `permission: dscript.warp`, którą zamieszczaliśmy w każdym przykładzie. Dzięki niej każdy gracz, który nie jest opem i nie posiada uprawnienia `dscript.warp`, nie będzie mógł użyć tego polecenia. Ogólnie zaleca się dodawanie takiego uprawnienia do każdego własnego polecenia, nawet jeśli planujesz, by było ono dostępne dla każdego.

Konkretna nazwa klucza uprawnienia może być dowolna. Tworzysz nowy węzeł uprawnienia, więc sam wybierasz jego nazwę.

Często używa się podstawowego formatu typu `dscript.[NazwaPolecenia]` lub `[NazwaProjektu].[NazwaPolecenia]`. Zazwyczaj chcesz, aby Twoje węzły uprawnień miały stosunkowo oczywiste nazwy.

![](images/warp_command_noperms.png)

Czasami potrzebujesz nieco więcej sprawdzania uprawnień niż tylko podstawowy dostęp do polecenia – na przykład możemy chcieć, aby każdy gracz mógł korzystać z warpów, ale nie mógł tworzyć nowych ani zmuszać innych graczy do teleportacji. Dodanie takiego sprawdzenia uprawnień mogłoby wyglądać tak:
```dscript_green
warp_command:
    type: command
    name: warp
    description: Teleportuje cię do magicznych miejsc.
    usage: /warp create [nazwa-warpu] | goto [nazwa-warpu] | player [gracz] [nazwa-warpu]
    permission: dscript.warp
    tab completions:
        1: create|goto|player
        2: <context.args.first.equals[goto].if_true[<server.flag[warps].keys.parse[unescaped].if_null[]>].if_false[]>
        3: <context.args.first.equals[player].if_true[<server.flag[warps].keys.parse[unescaped].if_null[]>].if_false[]>
    script:
    - if <context.args.is_empty>:
        - narrate "<&[error]>/warp create [nazwa-warpu]"
        - narrate "<&[error]>/warp goto [nazwa-warpu]"
        - narrate "<&[error]>/warp player [gracz] [nazwa-warpu]"
        - stop
    - choose <context.args.first>:
        - case create:
            #+ Wymagaj specjalnego uprawnienia do tworzenia warpów
            - if !<player.has_permission[dscript.warp.create]>:
                - narrate "<&[error]>Nie masz uprawnień, aby tego użyć!"
                - stop
            - if <context.args.size> < 2:
                - narrate "<&[error]>Musisz podać nazwę warpu!"
                - stop
            - else if <context.args.size> > 2:
                - narrate "<&[error]>Podałeś zbyt wiele argumentów!"
                - stop
            - define name <context.args.get[2]>
            - if <server.has_flag[warps.<[name].escaped>]>:
                - narrate "<&[error]>Warp o nazwie <&[emphasis]><[name]><&[error]> już istnieje!"
                - stop
            - flag server warps.<[name].escaped>:<player.location>
            - narrate "<&[base]>Stworzono warp <&[emphasis]><[name]><&[base]>!"
        - case goto:
            - if <context.args.size> < 2:
                - narrate "<&[error]>Musisz określić, dokąd chcesz się przenieść!"
                - stop
            - else if <context.args.size> > 2:
                - narrate "<&[error]>Podałeś zbyt wiele argumentów!"
                - stop
            - define name <context.args.get[2]>
            - if !<server.has_flag[warps.<[name].escaped>]>:
                - narrate "<&[error]>Warp o nazwie <&[emphasis]><[name]> <&[error]>nie istnieje!"
                - stop
            - teleport <player> <server.flag[warps.<[name].escaped>]>
            - narrate "<&[base]>Przeniesiono do <&[emphasis]><[name]><&[base]>!"
        - case player:
            #+ Wymagaj specjalnego uprawnienia do teleportowania innych graczy
            - if !<player.has_permission[dscript.warp.other]>:
                - narrate "<&[error]>Nie masz uprawnień, aby tego użyć!"
                - stop
            - if <context.args.size> < 3:
                - narrate "<&[error]>Musisz podać gracza oraz cel teleportacji!"
                - stop
            - else if <context.args.size> > 3:
                - narrate "<&[error]>Podałeś zbyt wiele argumentów!"
                - stop
            - define playerName <context.args.get[2]>
            - define destination <context.args.get[3]>
            - define playerToWarp <server.match_player[<[playerName]>].if_null[null]>
            - if <[playerToWarp]> == null:
                - narrate "<&[error]>Nie można znaleźć gracza o nazwie '<&[emphasis]><[playerName]><&[error]>'!"
                - stop
            - if !<server.has_flag[warps.<[destination].escaped>]>:
                - narrate "<&[error]>Warp o nazwie <&[emphasis]><[destination]> <&[error]>nie istnieje!"
                - stop
            - teleport <[playerToWarp]> <server.flag[warps.<[destination].escaped>]>
            - narrate "<&[base]>Przeniesiono gracza <&[emphasis]><[playerToWarp].name> <&[base]>do <&[emphasis]><[destination]><&[base]>!"
        - default:
            - narrate "<&[error]>Nieznany argument '<&[emphasis]><context.args.first><&[error]>'!"
```

Zauważ teraz dodatkowe sprawdzenie `if` przy próbie tworzenia warpu oraz przy teleportowaniu innych graczy, używające tagu `has_permission`. Wypróbuj to – albo z kimś innym, albo jeśli jesteś lokalnie, odbierz sobie uprawnienia.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o poleceniach gracza, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja kontenerów skryptów poleceń](https://meta.denizenscript.com/Docs/Languages/command%20script%20containers)
- [Tag match_player](https://meta.denizenscript.com/Docs/Tags/server.match_player)
- [Tag match_offline_player](https://meta.denizenscript.com/Docs/Tags/server.match_offline_player)
