Zmiana ścieżki: Polecenie If
---------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czym jest polecenie „If”?

Nauczyłeś się używać zdarzeń do uruchamiania zestawu poleceń, gdy na serwerze wystąpi określona sytuacja. Nauczyłeś się używać tagów, aby zmieniać działanie dowolnego polecenia w zależności od szczegółów sytuacji.
Teraz nadszedł czas, aby użyć polecenia `if`, aby połączyć te dwie koncepcje: wybór zestawu poleceń do uruchomienia na podstawie szczegółów sytuacji.

Polecenie `if` (jeśli) robi dokładnie to, co sugeruje nazwa: mówi „*jeśli* coś jest prawdą, to uruchom te polecenia. W przeciwnym razie ich nie uruchamiaj”.

### Jak wygląda polecenie If?

Nie martw się, polecenia `if` są dość łatwe do napisania i wyglądają tak samo, jak wszystko inne w skryptach Denizen.

Oto podstawowy format:
```dscript_blue
- if (jakiś warunek tutaj):
    - (jakieś polecenia)
    - (tutaj)
```

Jak widzisz, `if` to polecenie <span class="parens">(pisane na początku jak każde inne, z warunkiem jako argumentem wejściowym)</span>, ale z dwukropkiem `:` na końcu <span class="parens">(podobnie jak w linii zdarzenia)</span> oraz z poleceniami umieszczonymi wewnątrz z odpowiednim wcięciem. Należy zachować szczególną ostrożność przy wcięciach poleceń wewnątrz bloku. Jeśli polecenia wewnątrz nie zostaną przesunięte o jeden poziom wcięcia, Denizen nie będzie wiedział, że miały należeć do bloku `if`, i po prostu uruchomi je niezależnie od podanego warunku.

Zobaczmy, jak może to wyglądać w prawdziwym skrypcie...
```dscript_green
magic_healing_bell:
    type: world
    events:
        after player right clicks bell:
        - if <player.health_percentage> < 25:
            - heal
            - actionbar "<&[base]>Dzwon cię uleczył!"
```

![](images/magic_bell.png)

Ten przydatny przykładowy skrypt natychmiast uleczy gracza, który kliknie blok dzwonu, ale *tylko jeśli* jego poziom zdrowia jest niebezpiecznie niski.
<span class="parens">(Będziemy rozwijać ten przykład w tej sekcji, a gdy dotrzemy do sekcji [Flagi (Flags)](flags), dodamy do niego limit czasu, aby gracze mogli leczyć się tylko raz na kilka minut).</span>

Skrypt ten wyświetla również wiadomość na pasku akcji (actionbar), aby poinformować gracza o uleczeniu, używając podstawowego koloru tekstu zdefiniowanego w pliku `config.yml` wtyczki Denizen.

### Warunki

Istnieje kilka różnych sposobów tworzenia warunków w poleceniu `if`.
W najprostszym przypadku warunkiem może być tag logiczny (boolean) <span class="parens">(taki, który zwraca „true” lub „false”)</span> i to wystarczy. <span class="parens">(Na przykład: `- if <player.on_fire>:`)</span>.
Podpolecenia `if` zostaną uruchomione, gdy tag zwróci `true`, a nie zostaną uruchomione, gdy zwróci `false`.
Możesz również odwrócić działanie <span class="parens">(uruchomić, gdy tag to „false”, a nie uruchamiać, gdy to „true”)</span> tych prostych poleceń logicznych `if`, używając symbolu `!` <span class="parens">(czytanego jako „not” – nie)</span>.
Na przykład: `- if !<player.on_fire>:` uruchomi się tylko wtedy, gdy gracz **nie** płonie.

Może to być również tag o określonej wartości <span class="parens">(tag, który zwraca coś bardziej złożonego niż wartość logiczna)</span>, w którym to przypadku zostanie on porównany z inną wartością.
Podstawowy format polecenia `if` porównującego dwie wartości to `- if (pierwsza wartość) (porównanie) (druga wartość):` <span class="parens">(na przykład: `- if <player.name> == mcmonkey4eva:`)</span>.
Jedna lub obie wartości mogą być tagami. <span class="parens">(Technicznie rzecz biorąc, obie wartości mogą być również tekstem statycznym, ale oznaczałoby to, że polecenie `if` albo zawsze uruchamia swoje podpolecenia, albo nigdy tego nie robi... a to mało przydatne polecenie)</span>.

Dostępnych jest kilka różnych typów porównań:
- `==` służy do sprawdzania równości <span class="parens">(`- if 3 == 3:` uruchomi polecenia, ale `- if 3 == 4:` już nie)</span> – można to czytać jako „jeśli trzy równa się trzy, to uruchom polecenia”.

<hr>

- `!=` służy do sprawdzania nierówności <span class="parens">(zatem `- if 3 != 3:` **nie** uruchomi poleceń, a `- if 3 != 4:` **uruchomi** polecenia)</span> – znak `!` czyta się jako „nie”, więc można to czytać jako „jeśli trzy **nie równa się** trzy, to uruchom polecenia”. W rzeczywistym użyciu jedna lub obie wartości pochodziłyby z tagu.

<hr>

- `>`, `>=`, `<` oraz `<=` służą do porównań liczbowych (większy niż, większy lub równy, mniejszy niż, mniejszy lub równy). Zauważ, że `>` i `<=` są przeciwieństwami, podobnie jak `<` i `>=`.
Specjalna uwaga: możesz zauważyć, że znaki `<` i `>` są normalnie używane do oznaczania tagów, ale tutaj mają inne znaczenie – nie jest to problemem, ponieważ nigdy nie będziesz mieć jednocześnie `<` i `>` w pojedynczym argumencie porównania, więc symbole te nigdy nie zostaną błędnie zinterpretowane jako tag.

<hr>

- `contains` (zawiera) oraz `in` (w) służą do sprawdzania zawartości list, np. `- if one|two contains one:` lub `- if one in one|two:`

<hr>

- `matches` (pasuje do) służy do zaawansowanego dopasowywania, np. `- if <player.item_in_hand> matches diamond_sword:`

### Łączenie warunków: Venti Mocha Frap z dodatkowym cukrem i bez śmietanki

Gdy proste porównanie nie wystarcza i musisz uwzględnić kilka dodatkowych rzeczy, nie martw się: polecenie `if` na to pozwala!

Dostępne są dwa typy łączenia:
- Jeśli chcesz, aby zestaw poleceń został uruchomiony, gdy **wszystkie** porównania są prawdziwe, użyj `&&` <span class="parens">(czytane jako „and” – i; symbol to podwójny ampersand)</span>.
Na przykład: `- if <player.on_fire> && <player.health_percentage> < 25:` uruchomi polecenia, jeśli gracz płonie *oraz* jest bliski śmierci.

<hr>

- Jeśli chcesz, aby zestaw poleceń został uruchomiony, gdy **którekolwiek (jedno lub więcej)** z porównań jest prawdziwe, użyj `||` <span class="parens">(czytane jako „or” – lub; symbol to podwójna pionowa kreska, tzw. pipe)</span>.
Na przykład: `- if <player.on_fire> || <player.health_percentage> < 25:` uruchomi polecenia, jeśli gracz płonie <span class="parens">(niezależnie od poziomu zdrowia)</span> lub jeśli zdrowie gracza jest niskie <span class="parens">(niezależnie od tego, czy płonie)</span>.

Jeśli masz wiele rzeczy, które chcesz określić naraz, możesz po prostu łączyć je w szereg: `- if (warunek jeden) && (warunek dwa) && (warunek trzy) && (warunek cztery):`.
Istnieje tylko jedna komplikacja: możesz łączyć je bez przeszkód, jeśli przez cały łańcuch używasz tylko `&&` albo tylko `||`. Nie możesz używać obu typów w jednym łańcuchu... pomyśl o tym: `- if (jeden) && (dwa) || (trzy) && (cztery):` ... co to właściwie znaczy? Czy oznacza to, że musimy mieć albo jeden i dwa, ALBO mieć trzy i cztery? Mogłoby to też oznaczać, że musimy mieć jeden, dwa lub trzy, oraz cztery. My jako ludzie możemy domyślać się intencji, ale Denizen to tylko oprogramowanie, nie potrafi czytać w myślach.

Rozwiązanie tego problemu ukryło się w sposobie, w jaki zadałem pytanie: jeśli spojrzysz na to, jak opisałem te dwie interpretacje, zauważysz, że starannie pogrupowałem części razem. Denizen zawiera składnię, która pozwala na to samo: nawiasy `()` z dodatkowymi spacjami mogą służyć do zaznaczania grup. Jak tego użyć?

Oto długi, ale stosunkowo prosty przykład:
```dscript_blue
- if ( <player.name> == mcmonkey4eva && <player.health_percentage> > 90 ) || ( <player.on_fire> && <player.health_percentage> < 25 ):
    - narrate "Wow, mcmonkey ma się całkiem nieźle! Albo ktoś właśnie ginie w ogniu..."
```

Używając grupowania jak pokazano powyżej, możesz stworzyć dowolną kombinację warunków, jakiej tylko będziesz potrzebować. Możesz nawet umieszczać grupy wewnątrz innych grup, jeśli naprawdę chcesz, ale pamiętaj, że przesada może uczynić Twój skrypt trudnym do odczytania i lepsza może być inna organizacja <span class="parens">(sprawdź następną podsekcję poniżej jako dobrą alternatywę)</span>.

### Najczęstsze użycie „If”

Zaleta polecenia `if` w przykładzie z „magicznym leczniczym dzwonem” jest dość oczywista: pozwoliła nam dokładnie określić, kiedy zestaw poleceń powinien się uruchomić. Zamiast „zawsze, gdy gracz kliknie dzwon”, mamy teraz „zawsze, gdy gracz kliknie dzwon, *a ten gracz* ma niski poziom zdrowia”. Zawężanie wymagań dla poleceń jest ogólnie rzecz biorąc najczęstszym zastosowaniem polecenia `if`.

W prawdziwych skryptach bardzo często zobaczysz to w nieco innej formie, ale realizującej ten sam cel:
```dscript_green
magic_healing_bell:
    type: world
    events:
        after player right clicks bell:
        - if <player.health_percentage> > 25:
            - stop
        - heal
        - actionbar "<&[base]>Dzwon cię uleczył!"
```

Używając polecenia `stop` wewnątrz bloku `if`, możemy zatrzymać skrypt przed wykonaniem polecenia uleczenia, jeśli zdrowie gracza jest wyższe niż 25%. Jest to funkcjonalnie to samo co oryginalny skrypt, ale wyrażone „od tyłu” <span class="parens">(zamiast „jeśli gracz ma poniżej 25% zdrowia, ulecz go”, mówimy „jeśli gracz ma powyżej 25% zdrowia, nie zostaje uleczony”)</span>. Jest to bardzo przydatne, gdy masz wiele wymagań, które chcesz sprawdzić przed uruchomieniem właściwych poleceń skryptu, ponieważ można je zapisać schludnie jedno po drugim <span class="parens">(zamiast utknąć z bardzo długą linią wymieniającą wszystkie warunki)</span>.

### Rozwidlenie na drodze

Do tej pory wyjaśniliśmy podstawy polecenia `if` w kontekście tego, jak uruchomić lub nie zestaw poleceń na podstawie warunku. To świetnie nadaje się do dodawania dodatkowych wymagań do zdarzenia, ale to nie wszystko, co `if` potrafi! Obecnie rozumiemy, że polecenie to mówi: „jeśli warunek jest prawdziwy, to uruchom te polecenia, w przeciwnym razie nie”. Rozszerzmy to na: „jeśli warunek jest prawdziwy, to uruchom te polecenia, w przeciwnym razie *uruchom te inne polecenia*”. Teraz nasz skrypt może pójść jedną z dwóch ścieżek, zależnie od warunku opartego na tagach. Zrobimy to za pomocą polecenia `else`.

![](images/bell_fork.png)

### Jak wygląda polecenie Else?

Polecenie `else` (w przeciwnym razie) wygląda prawie tak samo jak `if`.

Oto podstawowy format:
```dscript_blue
- if (jakiś warunek tutaj):
    - (polecenia dla sytuacji 'prawda')
- else:
    - (polecenia dla sytuacji 'fałsz')
```

To całkiem proste! To po prostu `else` bez parametrów wejściowych, sformatowane tak samo jak `if`, z tym specjalnym wymogiem, że musi znajdować się bezpośrednio po bloku `if`. Zawsze, gdy warunek polecenia `if` jest fałszywy, zostaną uruchomione polecenia w bloku `else`.

Wykorzystajmy `else` w naszym skrypcie magicznego dzwonu...
```dscript_green
magic_healing_bell:
    type: world
    events:
        after player right clicks bell:
        - if <player.health_percentage> < 25:
            - heal
            - actionbar "<&[base]>Dzwon cię uleczył!"
        - else:
            - actionbar "<&[error]>Dzwon nic nie robi: jesteś już wystarczająco zdrowy."
```

Dotychczasowy skrypt leczył graczy z poziomem zdrowia poniżej 25%. Teraz wyświetli również wiadomość graczom, którzy są zdrowi, aby nie czuli się zdezorientowani, gdy dzwon nic nie zrobi.

Możesz zapytać: „no dobrze, dlaczego po prostu nie użyć polecenia `stop` w sposób pokazany wcześniej i nie umieścić wiadomości o odrzuceniu tuż przed poleceniem `stop`?”. I możesz to zrobić! To świetny sposób. Więc po co jest polecenie `else`? Jest ono znacznie bardziej przydatne, gdy masz instrukcje, które znajdują się *po* blokach `if`/`else` i które muszą zostać wykonane niezależnie od tego, co stało się wewnątrz tych bloków. Rozważ ten skrypt:

```dscript_green
magic_healing_bell:
    type: world
    events:
        after player right clicks bell:
        - if <player.health_percentage> > 90:
            - actionbar "<&[error]>Dzwon nic nie robi: jesteś już wystarczająco zdrowy."
            - stop
        - if <player.health_percentage> < 25:
            - actionbar "<&[base]>Dzwon cię uratował!"
        - else:
            - actionbar "<&[base]>Dzwon cię uleczył!"
        - heal
```

Ten skrypt odrzuci gracza, jeśli ma co najmniej 90% zdrowia, a w każdym innym przypadku go uleczy. Wyświetla unikalną wiadomość zależnie od tego, czy gracz jest bliski śmierci, czy ma tylko trochę mniej zdrowia. Gdybyśmy nie użyli tutaj polecenia `else`, musielibyśmy zrobić to tak:

```dscript_red
magic_healing_bell:
    type: world
    events:
        after player right clicks bell:
        - if <player.health_percentage> > 90:
            - actionbar "<&[error]>Dzwon nic nie robi: jesteś już wystarczająco zdrowy."
            - stop
        - if <player.health_percentage> < 25:
            - actionbar "<&[base]>Dzwon cię uratował!"
            - heal
            - stop
        - actionbar "<&[base]>Dzwon cię uleczył!"
        - heal
```

To znacznie gorsze rozwiązanie! Musieliśmy napisać polecenie `heal` dwa razy <span class="parens">(nie wspominając o dodatkowym poleceniu `stop`)</span>. Jeśli myślisz „to nie jest takie złe”, pomyśl, o ile gorzej mogłoby to wyglądać w prawdziwym skrypcie, gdzie zamiast jednego polecenia `heal` jest cała lista instrukcji, które mają się uruchomić niezależnie od warunku. Musiałbyś duplikować całą tę listę – na szczęście polecenie `else` pozwala nam tego uniknąć!

### Czy rozwidlenia nie mają zwykle 3 lub 4 zębów?

![](images/trifork.png)

Polecenie `else` jest bardzo przydatne, jak widzieliśmy, ale ma w zanadrzu jeszcze więcej mocy! Zaczęliśmy od samego `if`, które pozwala uruchomić lub nie podzbiór kodu. Potem dodaliśmy `else`, aby wybierać jedną z dwóch ścieżek. Możesz pewnie zgadnąć, co będzie dalej: sposób na wybranie jednej z wielu możliwych ścieżek. To jest `else if`!

### Jak napisać „Else If”?

Oto podstawowy format:
```dscript_blue
- if (pierwszy warunek tutaj):
    - (polecenia dla pierwszego warunku 'prawda')
- else if (drugi warunek):
    - (polecenia dla drugiego warunku 'prawda')
- else:
    - (polecenia, gdy wszystkie warunki były 'fałszem')
```

Jak widać, to zasadniczo wstawienie `if` wewnątrz bloku `else`. Możesz łączyć `else if` w szeregu dowolną ilość razy, jedynym ograniczeniem jest to, że musisz zawsze zacząć od zwykłego `if`. Nie musisz posiadać końcowego `else` (bez `if`), ale możesz jeśli chcesz – w takim przypadku musi on znajdować się na samym końcu.

Jak to może wyglądać w praktyce?

```dscript_green
magic_healing_bell:
    type: world
    events:
        after player right clicks bell:
        - if <player.health_percentage> > 90:
            - actionbar "<&[error]>Dzwon nic nie robi: jesteś już wystarczająco zdrowy."
        - else if <player.health_percentage> < 25:
            - actionbar "<&[error]>Dzwon nie może cię uratować: jesteś w zbyt złym stanie."
        - else:
            - actionbar "<&[base]>Dzwon cię uleczył!"
            - heal
```

Ta wersja skryptu magicznego dzwonu nie uleczy Cię, jeśli jesteś już zdrowy, a także nie uratuje Cię przed śmiercią – uleczy Cię tylko, jeśli jesteś umiarkowanie ranny. Dzięki tej nowej konstrukcji nie potrzebujemy już wcale polecenia `stop`, ponieważ polecenie `heal` uruchamia się tylko w jednej z możliwych „gałęzi”.

### Zaraz, kto mówił o drzewach?

![](images/logic_tree.png)

Jeśli masz doświadczenie w projektowaniu zaawansowanych systemów komputerowych – cóż, po pierwsze: dlaczego w ogóle czytasz ten przewodnik? ale bardziej na temat: możesz rozpoznać termin „gałąź” (branch) używany tutaj. Jeśli nie, nadal możesz kojarzyć wizualizację ścieżek jako drzew z wieloma gałęziami. Jeśli nie masz pojęcia, o czym mówię, nie martw się, wyjaśnię.

Różne drogi, którymi może pójść polecenie if, nazywa się czasem „gałęziami”, a przy użyciu tej terminologii ogólną strukturę skryptu nazywa się „drzewem”. Kiedy skrypt jest uruchamiany, zaczyna od „korzenia” (root) <span class="parens">(czyli pierwszego polecenia)</span> i „rozgałęzia się” za każdym razem, gdy napotka polecenie `if` lub podobne <span class="parens">(dowolny podzbiór poleceń, który uruchamia się tylko czasami)</span>.

Nie musisz znać ani używać żadnego z tych terminów ani innych podobnych, które wprowadzamy, ale jeśli zobaczysz, że ktoś ich używa i nie będziesz wiedział, o czym mówi, miejmy nadzieję, że zapamiętasz o istnieniu tej podsekcji i tutaj zajrzysz!

Inne popularne sformułowania, o których warto wiedzieć, to „pass” (zaliczenie) i „fail” (niepowodzenie) – polecenie `if` „zalicza”, gdy jego warunki są prawdziwe <span class="parens">(i podpolecenia się uruchamiają)</span>. Polecenie `if` „kończy się niepowodzeniem”, gdy warunki są fałszywe <span class="parens">(i podpolecenia się nie uruchamiają)</span>.

### Idąc dalej

Jeśli siedzisz tu i myślisz: „no dobrze, to wszystko brzmi nieźle, ale chcę rozgałęziać skrypty na znacznie bardziej skomplikowane sposoby”, cóż... dotarłeś do końca ogólnego omówienia polecenia `if`. Zależnie od tego, co chcesz osiągnąć, możesz po prostu wstawić polecenie `if` wewnątrz innego polecenia `if` <span class="parens">(nic Cię nie powstrzyma, poza tym, że Twój skrypt zacznie wyglądać na wielki i groźny)</span>, albo jeśli to nie wystarczy... czytaj dalej przewodnik, jedna z kolejnych sekcji prawdopodobnie będzie zawierać to, czego szukasz <span class="parens">(np. sekcja [Pętle (Loops)](/guides/basics/loops))</span>.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o poleceniu if, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja polecenia if](https://meta.denizenscript.com/Docs/Commands/if)
- [Dokumentacja polecenia else](https://meta.denizenscript.com/Docs/Commands/else)
- [Dokumentacja języka Comparable](https://meta.denizenscript.com/Docs/Languages/comparable)
- [Dokumentacja języka Operator](https://meta.denizenscript.com/Docs/Languages/operator)
