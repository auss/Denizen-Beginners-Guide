Czym jest Denizen?
----------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Więc czym jest Denizen?

W skrócie: Denizen to język skryptowy!

Nieco szerzej: Denizen to wtyczka dla serwerów Spigot, która automatycznie ładuje i uruchamia skrypty napisane przez użytkownika, pozwalając właścicielom serwerów szybko i łatwo dostosować swój serwer do własnych potrzeb w sposób podobny do tworzenia własnych wtyczek, ale dziesięć razy szybciej i dziesięć razy łatwiej!

### Podejdźmy do tego nieco bardziej formalnie

Skrypty Denizen mają strukturę przypominającą YAML, dla ułatwienia i poczucia znajomości – czy kiedykolwiek edytowałeś konfiguracje YAML dla wtyczki na swoim serwerze? Wyobraź sobie taką prostą edycję konfiguracji YAML, ale możesz z nią zrobić *wszystko, co chcesz*!

Rzeczywista składnia w sekcjach skryptów to składnia poleceń i tagów (command-tag)...

- **Polecenie (Command):** Ponownie, koncepcja bardzo dobrze znana większości właścicieli serwerów – cały czas używasz poleceń, takich jak `/gamemode creative` lub `/tp player`. To samo, tylko zamiast `/` jest `-`.
- **Tag:** To coś, co jest mieszanką znajomego i dziwnego – jeśli spojrzysz na *dokumentację* polecenia, może tam być napisane coś w stylu `/tp <player>`, co wskazuje, że powinieneś wpisać nazwę gracza w ten pierwszy argument podczas rzeczywistego użycia. **Tag** w Denizen to zasadniczo jeden z tych symboli zastępczych "wypełnij mnie", z tą różnicą, że wypełnia się on automatycznie. Kiedy wpiszesz `<player>` w skrypcie Denizen, system automatycznie wstawi tam odpowiedniego gracza. To magiczna sztuczka, która zmienia proste polecenia w dynamiczne i potężne skrypty.

### Jak to wygląda?

Oto przykład prostego skryptu Denizen:

```dscript_green
my_example_script:
    type: task
    script:
    - narrate "Witaj, <player.name>!"
    - wait 5s
    - hurt <player> 1
```

Ten prosty skrypt, po uruchomieniu: Powita gracza po imieniu <span class="parens">(wypełnionym automatycznie)</span>, poczeka 5 sekund, a następnie zada graczowi obrażenia równe jednemu punktowi życia.

### Czyli to jak programowanie?

**Dla osób zaznajomionych z programowaniem w innych językach,** wiele elementów Denizen można przenieść na terminy znane z tych języków <span class="parens">(instrukcje if, pętle foreach itp. są zasadniczo równoważne)</span>, chociaż ważne jest, aby zauważyć, że istnieje wiele kluczowych różnic między Denizen a językami takimi jak JavaScript, C# czy większość innych popularnych języków. Jeśli nauczyłeś się jednego z tych języków, dość łatwo opanujesz Denizen – po prostu uważaj, aby nie próbować pisać skryptów Denizen w taki sam sposób, w jaki pisałbyś kod w popularnych językach programowania, ponieważ to po prostu nie jest to samo. W zależności od Twoich preferencji dotyczących języków programowania, możesz być zaskoczony tym, o ile łatwiej pracuje się z Denizen niż z językami, których używałeś wcześniej.

**Jeśli nie znasz żadnych języków programowania,** nie martw się – Denizen został zaprojektowany tak, aby był przyjazny i łatwy do nauczenia dla osób bez doświadczenia. Dostępna jest cała masa narzędzi i ułatwień, które sprawiają, że nauka i praca z Denizen jest o wiele łatwiejsza niż w przypadku bardziej onieśmielających języków, takich jak Java.

### Więc Denizen jest łatwiejszy do nauczenia i użycia... Jaki jest haczyk?

Niektórzy czytelnicy mogą teraz pomyśleć: "musi być znacznie mniej potężny, prawda?"... i tu zaczyna się ciekawa część. W Denizen często możesz zrobić *więcej* niż za pomocą wtyczki napisanej w Javie. Istnieją pewne wyjątki <span class="parens">(nie możesz tak po prostu podpiąć jakiejś losowej biblioteki szachowej w Javie czy czegokolwiek innego w Denizen dla zabawy, jak możesz to zrobić w Javie)</span>, ale jeśli chodzi o interakcję z serwerem Minecraft, Denizen nie tylko zapewnia większość funkcjonalności API Bukkit oraz ogromny zestaw dodatkowych narzędzi i ulepszeń poprawiających jakość pracy, ale w rzeczywistości zapewnia również duży zestaw funkcjonalności niedostępnych w Bukkit poprzez wywołania NMS. Na przykład: Bukkit nie posiada API do wbijania strzał w encję, ale Denizen może to zrobić za pomocą jednego szybkiego polecenia. Bukkit nie ma zdarzenia dla wiadomości wysyłanych *do* gracza, ale Denizen może je dla Ciebie bez problemu przechwycić... ale Denizen ma gotowe do użycia w dowolnym momencie polecenie `light`. Jest o wiele więcej takich funkcji, wiele z nich w kategorii "dlaczego Bukkit tego *nie* obsługuje?! To przecież takie przydatne!".

Kolejny haczyk, który może przyjść na myśl: wydajność. Czy skrypty działają tak szybko, jak czysty kod w Javie? Cóż... i tak, i nie. Jeśli dokonasz ścisłego porównania 1 do 1, ciasna pętla w Javie vs ciasna pętla w Denizen... Java wygra bez trudu. Jednak w rzeczywistych przypadkach użycia Denizen jest w stanie nadążyć w stopniu wystarczającym, aby w przypadku 99% skryptów, które możesz napisać, nie było zauważalnego spadku wydajności. Oddani skrypciarze odtworzyli funkcjonalność wtyczek takich jak WorldGuard i utrzymali działanie swoich serwerów przy pełnej prędkości bez żadnych problemów.
