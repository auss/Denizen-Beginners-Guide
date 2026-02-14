Zacieranie granicy między poleceniami a tagami: Skrypty procedur (Procedure Scripts)
--------------------------------------------------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Podstawy procedur

Pracując nad projektem, możesz zauważyć, że pewne fragmenty skryptu piszesz bardzo często i chciałbyś móc je schludnie zapakować oraz używać wielokrotnie. Może to być jakieś równanie matematyczne, którego nie chcesz kopiować i wklejać, albo skrypt generujący unikalne nazwy dla przedmiotów lub NPC. Dowiedziałeś się już o używaniu skryptów typu `task` do tworzenia reużywalnych sekcji kodu, które można wywołać jedną linią za pomocą polecenia `run`, ale co jeśli potrzebujesz czegoś jeszcze mniejszego i łatwiejszego w użyciu? Poznaj: skrypty **procedur** (Procedure scripts)!

Składają się one z dwóch części:

#### Kontener skryptu procedury (Procedure Script Container)

Skrypt procedury to miejsce, w którym zapisujesz swoją logikę. Korzystając z wcześniejszych przykładów: to tutaj wpisałbyś równanie matematyczne lub kod generujący losowe imię.

Skrypt procedury zazwyczaj posiada jedną lub więcej definicji wejściowych <span class="parens">(podobnie jak wejścia w skrypcie `task`)</span>, choć nie jest to wymagane. Procedura musi jednak zawsze zwracać wynik za pomocą polecenia `determine`.

Poniżej znajduje się przykładowy skrypt, który pobierze listę 9 lokalizacji, wyśrodkowaną na tej, która została podana skryptowi.

```dscript_green
surrounding_blocks:
    type: procedure
    definitions: center
    script:
    - repeat 3 as:x from:-1:
        - repeat 3 as:z from:-1:
            - define blocks:->:<[center].add[<[x]>,0,<[z]>]>
    # Skrypt procedury MUSI coś ustalić (determine).
    - determine <[blocks]>
```

#### Tag procedury (Procedure Tag)

Tag `proc` służy do pobrania wyniku ze skryptu procedury. Kontynuując przykład z matematyką i generowaniem imion: kiedy potrzebuję wyniku mojego równania lub chcę wygenerować imię, używam tagu procedury w taki sam sposób, w jaki użyłbym każdego innego tagu w Denizen. Poniższy przykład wykorzystuje skrypt procedury `surrounding_blocks` pokazany powyżej w innym skrypcie.

```dscript_green
some_script:
    type: task
    script:
    # Pobierz obszar 3x3 pod stopami gracza
    - define locations <player.location.proc[surrounding_blocks]>
    # zamień je w fałszywy kamień na kilka sekund
    - showfake <[locations]> stone d:2s
```

Tagi procedur dają skrypciarzom sposób na dzielenie kodu na łatwiejsze do zarządzania fragmenty i chronią przed zbyt częstym powtarzaniem tego samego kodu. Udało nam się stworzyć fajny mechanizm przywoływania fałszywej platformy przy użyciu zaledwie kilku linii kodu, schludnie ukrywając bardziej skomplikowane części. Zamiast skupiać się na szczegółach realizacji technicznej, możemy skupić się na tym, co chcemy osiągnąć!

Istnieje kilka sposobów użycia tagu procedury, z pewnymi różnicami, o których warto wiedzieć.

Pierwszy to najprostszy sposób na pobranie wartości ze skryptu procedury bez żadnych dodatkowych wejść <span class="parens">(forma `proc`)</span>:

```dscript_blue
# Zakłada, że 'my_procedure_script' nie posiada żadnych wejściowych 'definitions'
- define my_value <proc[my_procedure_script]>
```

Jeśli chcesz przekazać coś do skryptu procedury, możesz użyć części `.context` tagu, co może wyglądać mniej więcej tak <span class="parens">(forma `proc.context`)</span>:

```dscript_blue
# Zakłada, że 'my_procedure_script' posiada 3 proste wejściowe 'definitions'
- define my_value <proc[my_procedure_script].context[apple|orange|lasagna]>
```

Przekaże to trzy wartości (`apple`, `orange` i `lasagna`) do skryptu procedury i przypisze je do definicji zapisanych w kluczu `definitions`. Do skryptu procedury możesz przekazać praktycznie wszystko.

Jeśli masz do przekazania tylko jedną wartość, wygodniejsza może być skrócona wersja tagu <span class="parens">(forma `ObjectTag.proc`)</span>:

```dscript_blue
# Zakłada, że 'my_procedure_script' posiada 1 prostą definicję wejściową
- define fruit apple
- define my_value <[fruit].proc[my_procedure_script]>
```

Przekaże to `apple` do skryptu procedury i będzie to pierwsza definicja w kluczu `definitions`. Możesz również łączyć wersję skróconą z kontekstem, jeśli tak zdecydujesz.

Ważne jest, aby zauważyć, że klucz context może nie działać dokładnie tak, jak się spodziewasz, gdy przekazujesz listę. Jeśli użyjesz skróconej wersji i przekażesz listę, zadziała to zgodnie z oczekiwaniami – pierwsza definicja z klucza `definitions` zostanie ustawiona na tę listę. Jednak jeśli umieścisz listę w kluczu context, nie przekaże on całej listy jako jednej wartości, lecz jako oddzielne, poszczególne wartości. Oto dwa wizualne przykłady. Zakładając, że `colors` to lista zawierająca `red`, `brown` i `green`:

```dscript_blue
# Naszymi trzema definicjami będą: lista colors, 'apple' oraz '14'
- define my_value <[colors].proc[my_procedure_script].context[apple|14]>
```

```dscript_red
# Naszymi trzema definicjami będą trzy kolory z listy, natomiast 'apple' i '14' zostaną zignorowane lub uszkodzone
- define my_value <proc[my_procedure_script].context[<[colors]>|apple|14]>
```

Istnieją sposoby na obejście tego problemu, takie jak owinięcie listy w `list_single` lub zamiana listy w element rozdzielony przecinkami (a potem ponowne rozdzielenie jej wewnątrz procedury), albo po prostu użycie jednego argumentu typu map, który zawiera wszystkie potrzebne definicje. Ostatecznie to Ty decydujesz, co jest najlepsze dla Twojego przypadku.

Więcej na temat tego potencjalnego problemu znajdziesz w sekcji częstych błędów dotyczącej [Object Hackingu](/guides/troubleshooting/common-mistakes#object-hacking-is-a-bad-idea).

### Zadania (Tasks) a Procedury (Procedures)

Procedury nie różnią się drastycznie od zadań, ale istnieje kilka istotnych różnic, o których należy wiedzieć.

#### Efekty uboczne (Side Effects)

Skrypty procedur **nie mogą** zmieniać stanu zewnętrznego. Oznacza to, że skrypt procedury nie może niczego zmienić, a jedynie wyznaczyć wartość. Rzeczy, które liczą się jako efekt uboczny, to np. postawienie bloku, usunięcie/dodanie moba, ładowanie plików z komputera, ustawienie flagi i tak dalej. Ważne jest, aby uruchomienie tagu procedury nie wpływało na nic; skrypt ten może (i najpewniej będzie) odczytywać wartości, ale *nie powinien* zapisywać żadnych wartości. Oznacza to, że w tagu procedury można używać bardzo niewielu poleceń. `define` oraz `determine`, polecenia sterujące przepływem jak `if` czy `foreach`, oraz debugowanie – to właściwie wszystko, czego możesz użyć.

Jest to to samo ograniczenie, które dotyczy wszystkich **tagów** w Denizen. Dotyczy ono procedur, ponieważ skrypt procedury to dokładnie to: własny, niestandardowy tag!

#### Zwracanie wartości

Podczas gdy skrypty zadań (task scripts) *mogą* opcjonalnie zwrócić wartość po zakończeniu, tag procedury **musi** zwrócić wartość. Zadania są jak czynność, którą trzeba wykonać, podczas gdy procedura jest bardziej jak zadane pytanie. Gdybyś musiał skosić trawnik, wykonałbyś **zadanie** koszenia trawnika, a po skończeniu mógłbyś zająć się czymś innym. *Mógłbyś* poinformować kogoś o zakończeniu lub powiedzieć, ile czasu Ci to zajęło, ale nie jest to *WYMAGANE* do samego wykonania zadania koszenia. Jeśli jednak nauczyciel zada Ci pytanie, to udzielenie odpowiedzi jest nie tylko oczekiwane, ale wręcz **wymagane**.

Chociaż skrypty zadań mają możliwość zwracania wartości, jest to bardziej uciążliwe niż użycie procedury. W poniższych przykładach mamy procedurę i zadanie – oba znajdują wszystkie owoce w zdaniu i zwracają je w formie listy. Następnie wybierzemy jeden losowo i wyświetlimy go graczowi.

Oto wersja z zadaniem:

```dscript_yellow
get_fruits:
    type: task
    definitions: message
    script:
    - determine <[message].split.filter[is_in[orange|apple|banana]]>

task_example:
    type: world
    events:
        on player chats:
        # uruchom zadanie i zapisz wynik. Musimy też określić czekanie na zakończenie zadania
        - ~run get_fruits def:<context.message> save:fruit_list
        # pobierz zapisaną listę, pobierz kolejkę, następnie ustaloną listę i losowy wpis z tej determinacji
        - narrate "Nie gadaj, ja też lubię <entry[fruit_list].created_queue.determination.first.random.if_null[nothing]>!"
```

A oto wersja z procedurą:

```dscript_green
get_fruits:
    type: procedure
    definitions: message
    script:
    - determine <[message].split.filter[is_in[orange|apple|banana]]>

proc_example:
    type: world
    events:
        on player chats:
        # odczytaj zdanie
        - narrate "Nie gadaj, ja też lubię <context.message.proc[get_fruits].random.if_null[nothing]>!"
```

Zauważ, że `get_fruits` jest w obu przypadkach w zasadzie takie samo, ale zdarzenie `proc_example` jest o wiele prostsze.

#### Kolejność operacji

Choć zarówno zadania, jak i procedury posiadają komponent skryptu oraz sposób na jego uruchomienie, zadania są uruchamiane poleceniami takimi jak `inject` lub `run` i mogą działać równolegle z innym kodem. Procedury natomiast są uruchamiane za każdym razem, gdy ich tag jest przetwarzany, co może dziać się częściej lub rzadziej niż myślisz, albo w momentach, których się nie spodziewałeś. Procedura nie może czekać: musi wypełnić swój tag natychmiastowo i „zamrozi” serwer, jeśli zajmie jej to zbyt dużo czasu.

Ostatecznie oznacza to, że będziesz musiał bardziej uważać na skrypty, które mogą powodować lagowanie serwera. Poniżej znajduje się przykład, który nie sprawiłby problemu jako skrypt zadania, ale *sprawiłby* problem jako skrypt procedury.

To użycie zadania powinno być całkiem płynne:

```dscript_blue
smooth_example:
    type: task
    script:
    - narrate "Jaka jest milionowa cyfra liczby pi? Hmm..."
    # Pobierz milionową cyfrę pi. Użycie operatora czekania (~) poczeka na zakończenie zadania,
    # ale inne rzeczy na serwerze będą wciąż przetwarzane, więc nie będzie laga.
    - ~run digit_of_pi def:1000000 save:digit
    - narrate "No tak, milionowa cyfra to <entry[digit].queue.determination.first>."
```

To użycie procedury może jednak nieco „przyciąć” serwer:

```dscript_red
laggy_example:
    type: task
    script:
    - narrate "Jaka jest milionowa cyfra liczby pi? Hmm..."
    # Serwer zatrzyma się, ponieważ będzie zajęty przetwarzaniem tego skryptu procedury, aby móc wykonać narrate.
    - narrate "No tak, milionowa cyfra to <proc[digit_of_pi].context[1000000]>."
```

### Kiedy używać

Choć zadania i procedury są do siebie podobne, oba mają swoje zastosowania.

Skrypt zadania (task script) jest zazwyczaj lepszy, jeśli masz coś, co:

- nie wymaga odpowiedzi
- może zająć dużo czasu przed udzieleniem odpowiedzi
- wpływa na świat w jakiś sposób

A skrypt procedury (procedure script) jest zazwyczaj lepszy, jeśli masz coś, co:

- nie wpływa na świat
- zawsze udzieli Ci odpowiedzi
- musi być użyte wewnątrz tagu

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o skryptach procedur, tagu procedury i jego opcjach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja kontenerów skryptów procedur](https://meta.denizenscript.com/Docs/Languages/procedure%20script%20containers)
- [Tag proc](https://meta.denizenscript.com/Docs/Tags/proc)
- [Tag proc.context](https://meta.denizenscript.com/Docs/Tags/proc.context)
- [Tag ObjectTag.proc](https://meta.denizenscript.com/Docs/Tags/objecttag.proc)
- [Dokumentacja polecenia Determine](https://meta.denizenscript.com/Docs/Commands/determine)
