System kolejek (Queues)
---------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Wstęp

Głównym celem tej strony jest wprowadzenie Cię w koncepcję *systemu kolejek* (queue system). System ten jest niezwykle ważny nawet na podstawowym poziomie skryptowania, choć wewnętrznie jest dość złożony i umożliwia korzystanie z wielu zaawansowanych funkcji. Z tego powodu wiele zaawansowanych aspektów zostanie wspomnianych lub opisanych w nawiasach w treści tej strony.

Jeśli śledzisz ten przewodnik po raz pierwszy, nie musisz rozumieć każdej części systemu kolejek. Musisz jedynie pojąć podstawową ideę tego, czym są kolejki, ale mimo to powinieneś przeczytać wszystkie informacje na tej stronie, aby poczuć ogólny zakres wpływu tego systemu na działanie Denizen.

### Czym jest kolejka?

W Denizen słowo „**skrypt**” może odnosić się do kilku różnych rzeczy: pliku `.dsc`, kontenera wewnątrz niego <span class="parens">(takiego jak kontener skryptu `world`)</span> lub podsekcji kontenera skryptu, która zawiera listę poleceń do wykonania. Na potrzeby tej strony będziemy używać słowa „skrypt” w tym ostatnim znaczeniu. Skryptem może być pojedyncze polecenie `narrate` lub długa lista poleceń do wykonania jedno po drugim, potencjalnie z użyciem `wait` dla opóźnień między poleceniami, `run` do uruchamiania innych skryptów itp.

Kiedy uruchamiasz skrypt, startuje **kolejka** (queue). Kolejkę można traktować jako aktualnie działającą instancję skryptu. To, co to dokładnie oznacza, może nie być od razu oczywiste, ale wpływ, jaki ma to na sposób działania Denizen, jest znaczący.

Kolejki to oddzielne, tymczasowe, unikalne instancje, które działają i czekają niezależnie od siebie oraz przechowują osobne zestawy danych tymczasowych <span class="parens">(definicje itp.)</span>.

Aby lepiej zrozumieć kolejki, przeanalizujmy kilka przykładów. Podamy ich kilka, więc jeśli nie załapiesz od razu, czytaj dalej – być może zrozumiesz po kolejnym przykładzie.

#### Uwaga terminologiczna

Często mówiąc o rzeczach przypisanych do konkretnych kolejek, wciąż używa się słowa „skrypt” – powiemy na przykład, że skrypt ma definicję, choć w rzeczywistości to kolejka posiada definicję, a skrypt ma tylko surowe polecenie, które zostanie później wykonane w kolejce w celu ustawienia tej definicji. Często łatwiej jest po prostu powiedzieć, że skrypt to zrobił, a przejmować się rozróżnieniem skrypt vs kolejka tylko wtedy, gdy musi to być precyzyjnie określone, by uniknąć pomyłek.

### Ex-Sustained (Utrzymana egzekucja)

Zacznijmy od użycia poleceń `/ex` oraz `/exs`, aby poczuć, czym jest „kolejka”.

W grze wpisz następujące dwa polecenia:
Najpierw: `/ex define example <player.name>`
Następnie: `/ex narrate <[example]>`

Zobaczysz, że drugie polecenie wyrzuci błąd i nie wypełni tagu. Zdefiniowałeś `example` w pierwszym poleceniu, a potem próbowałeś go wyświetlić w drugim, ale to nie zadziałało.

![](images/queue_ex_fail.png)

Dlaczego tak się stało? Ponieważ każde użycie polecenia `/ex` to jego własna, oddzielna *kolejka*. Definicja stworzona w pierwszym poleceniu jest zapisana w kolejce tego pierwszego polecenia i nie jest dostępna dla kolejki drugiego polecenia.

Teraz spróbujmy użyć `/exs` – gdzie `/ex` oznacza „execute” (wykonaj), a `/exs` oznacza „execute-sustained” (wykonaj w sposób ciągły), co oznacza, że utrzymuje on pojedynczą kolejkę.

Najpierw wpisz: `/exs define example <player.name>`
Następnie: `/exs narrate <[example]>`

Tym razem zobaczysz swoje imię wyświetlone tak, jak zamierzano. Ponieważ oba polecenia zostały uruchomione w tej samej, utrzymanej kolejce, drugie polecenie faktycznie „wie” o definicji z pierwszego.

![](images/queue_exs_success.png)

### Przykładowy skrypt

Użyjmy prawdziwego, napisanego wcześniej skryptu, aby zrozumieć różnicę między „skryptem” a „kolejką”.

```dscript_green
queue_demo:
    type: task
    script:
    - define choice <util.random_decimal>
    - narrate "<queue.id> jest instancją <script.name> i wybrał <[choice]>"
    - wait 5s
    - narrate "<queue.id> pamięta, że wybrał <[choice]>"
```

Załaduj ten skrypt, a następnie w grze wpisz dwukrotnie, szybko po sobie, polecenie `/ex run queue_demo`.

Powinieneś zobaczyć wynik podobny do tego:

![](images/queue_demo_script.png)

Na powyższym zrzucie ekranu widać, że wystartowały dwie kolejki, jedna oznaczona jako „`QUEUE_DEMO_4_MakeupToday`” i druga jako „`QUEUE_DEMO_7_ReferenceEfficient`”. Te identyfikatory (ID) są generowane z połączenia nazwy skryptu, numerycznego ID <span class="parens">(liczba, która rośnie o 1 za każdym razem, gdy startuje nowa kolejka)</span> oraz dwóch słów z generatora losowych wyrazów. W logu konsoli zobaczysz, że ID kolejek mają również przypisane losowe kolory do tych wyrazów <span class="parens">(„`MakeupToday`” ma biały, potem purpurowy, „`ReferenceEfficient`” ma pomarańczowy, potem zielony)</span>, co pomaga szybko odróżnić kolejki w logach debugowania.
<span class="parens">(Uwaga: możesz dostosować format ID kolejki w pliku `Denizen/config.yml`, jeśli chcesz)</span>.

Kolejka oznaczona „`MakeupToday`” wybrała losową liczbę „`0.315`...”, podczas gdy „`ReferenceEfficient`” wybrała „`0.251`...”.

Obie kolejki wystartowały z tego samego skryptu typu `task` o nazwie `queue_demo`, ale są od siebie niezależne. Działały w nieco innym czasie <span class="parens">(zależnie od tego, jak szybko powtórzyłeś polecenie run)</span>, każda wybrała osobną losową liczbę i zapisała ją w swojej własnej, oddzielnej kopii definicji `choice`, obie odczekały 5 sekund od momentu swojego unikalnego startu, a następnie wyświetliły końcową wiadomość we własnym czasie, każda pokazując swoje własne ID i swój własny wybór liczby.

Możesz też zauważyć, że każde użycie `/ex` miało swoją własną kolejkę, która wyłącznie wykonała polecenie `run`, a następnie zakończyła się, niezależnie od kolejki stworzonej dla działającego skryptu zadania.

### Kolejki są oddzielne

Każda kolejka jest oddzielona od każdej innej kolejki. Oznacza to, że działają/startują/zatrzymują się niezależnie oraz niezależnie śledzą dane.

#### Startowanie niezależnie

Jak pokazaliśmy wcześniej, możesz wywołać `/ex run ...` dla skryptu wielokrotnie i sprawić, że ten sam skrypt wystartuje nową kolejkę wiele razy, nawet podczas gdy poprzednie wciąż trwają. Te wcześniejsze kolejki nie mają żadnego związku z nową kolejką.

#### Działanie niezależnie

W naszym powyższym przykładowym skrypcie widzieliśmy, że kolejki czekały niezależnie od siebie. Jeśli nie było to od razu oczywiste, spróbuj uruchomić `/ex run queue_demo`, odczekaj dwie sekundy, a potem uruchom to drugi raz. Powinieneś zobaczyć start pierwszej kolejki, pauzę, start drugiej kolejki, kolejną pauzę, zakończenie pierwszej kolejki, jeszcze jedną pauzę równą czasowi, który odczekałeś między startami, a na końcu zakończenie drugiej kolejki. Każda kolejka śledziła swój czas `wait` oddzielnie.

#### Zatrzymywanie się niezależnie

Każda kolejka może zatrzymać się oddzielnie. Jak widzieliśmy wyżej, pierwsza kolejka zatrzymała się i zakończyła pracę, ale druga działała dalej.

W skrypcie możesz w dowolnym momencie użyć polecenia `stop`, aby zatrzymać kolejkę – zatrzyma ono tylko tę kolejkę, która dotarła do tego polecenia; inne kolejki tego samego skryptu nie zatrzymają się, dopóki same nie dotrą do swojego polecenia `stop`.

#### Oddzielne dane

Każda kolejka ma przypisany własny, oddzielny zestaw danych. Najprostszym tego przykładem są definicje – jak widziałeś w powyższym skrypcie, definicja `choice` była przypisana unikalnie do każdej kolejki.

Oznacza to również, że *przypisany gracz i NPC* są unikalni dla kolejki – ten sam skrypt może działać wielokrotnie dla wielu różnych graczy i każdy gracz będzie miał swoją własną kolejkę.

Dotyczy to również *danych kontekstowych*, np. w skrypcie typu `world` to samo zdarzenie może zostać uruchomione wiele razy z różnymi danymi kontekstowymi w każdej kolejce.

Obejmuje to również [argument `save`](https://meta.denizenscript.com/Docs/Languages/the%20save%20argument).

### Kolejki są tymczasowe

Kolejki istnieją tylko tak długo, jak długo trwają. W momencie zakończenia kolejki znika ona na zawsze <span class="parens">(choć w niektórych zaawansowanych przypadkach można wciąż pobierać wartości determinacji lub inne informacje z zapisanego odniesienia do instancji `QueueTag`)</span>.

Kolejki siłą rzeczy nie mogą istnieć po restarcie serwera – gdy serwer się wyłącza, wszystkie kolejki zostają utracone. Oznacza to, że nie możesz sprawić, by kolejka „poczekała” (`wait`) na wykonanie kolejnych poleceń po restarcie serwera. To, co możesz zrobić w zamian, to użyć [polecenia `RunLater`](https://meta.denizenscript.com/Docs/Commands/runlater), aby zaplanować uruchomienie skryptu w przyszłości, nawet po restartach.

### Kolejki są unikalne

Każda kolejka jest z natury unikalna. W związku z tym każda kolejka posiada unikalny identyfikator ID – oznacza to, że żadna inna kolejka nie może mieć w tym samym czasie takiego samego identyfikatora. Zauważ, że dotyczy to generowanych słów, ale nie obejmuje generowanych kolorów <span class="parens">(kolory służą wyłącznie przejrzystości debugowania)</span>.

Po zniknięciu kolejki, kolejka o tym samym ID nie może zostać wygenerowana ponownie aż do restartu serwera. <span class="parens">(Ostrzeżenie: jeśli wyłączysz numeryczną część ID w swojej konfiguracji, możliwe jest, choć rzadkie, że kolejka wygeneruje takie samo ID jak ta, która już wcześniej zakończyła działanie podczas tej samej sesji serwera, ale nigdy w czasie, gdy inna kolejka o tym samym ID wciąż trwa)</span>.

Po restarcie serwera mogą wygenerować się identyfikatory ID kolejek takie same jak poprzednie <span class="parens">(jeśli masz włączoną część generatora z losowymi słowami, powtórne identyfikatory będą bardzo rzadkie nawet po restarcie)</span>. Jest to kolejny powód, dla którego odniesienia do kolejek nie powinny być przechowywane długoterminowo.

### Kolejki tylko czasem na siebie nachodzą

Jeśli kolejka nigdy nie używa polecenia `wait` ani żadnego innego źródła opóźnienia, może zakończyć się natychmiast. „Natychmiast” nie oznacza, że nie upływa czas rzeczywisty, ale oznacza, że nie upływa czas wewnątrz gry. Innymi słowy, nic innego na serwerze nie jest przetwarzane – ani w Denizen, ani w Minecraft – dopóki kolejka nie skończy pracy. Cały serwer czeka na wykonanie poleceń z danej kolejki.

Jeśli jednak podasz kolejce źródło opóźnienia <span class="parens">(polecenie `wait`, element oczekujący `~` itp.)</span>, kolejka będzie zamiast tego natychmiast wykonywać polecenia aż do momentu napotkania opóźnienia, po czym wstrzyma swoje działanie i pozwoli reszcie serwera pracować. Dotyczy to również innych kolejek, które same czekają na wykonanie kolejnych poleceń. Gdy określone opóźnienie dobiegnie końca, kolejka wznowi działanie i natychmiast wykona oczekujące polecenia, aż albo skończy, albo dotrze do kolejnego polecenia powodującego opóźnienie.

#### Uwaga techniczna 1: Kolejki a wątki

Jeśli masz doświadczenie w innych językach programowania, prawdopodobnie myślisz, że kolejki brzmią podobnie do wątków (threads). To prawda, są one podobne, jednak nie są tym samym. Najważniejszą różnicą jest to, że podczas gdy kolejka wykonuje polecenia bez opóźnień, żadna inna kolejka nie może działać. Jest to przeciwieństwo wielowątkowości (multithreading), gdzie wiele wątków może działać jednocześnie i muszą one ostrożnie operować na danych, z których inne wątki również mogą korzystać.

Kolejki mogą niezawodnie odczytywać/modyfikować dowolne dane bez obaw o konflikty. Jedynym wyjątkiem jest sytuacja, gdy odczytasz jakieś dane, potem użyjesz `wait`, a następnie wykonasz akcje oparte na tych wcześniejszych danych – źródło mogło ulec zmianie podczas czekania. Na przykład, jeśli odczytasz, że materiał bloku to drzwi, potem odczekasz chwilę (`wait`), a następnie wykonasz polecenie `switch`, aby je otworzyć, możliwe jest, że gracz zniszczył te drzwi podczas Twojego czekania i w rezultacie polecenie switch wyrzuci teraz błąd.

#### Uwaga techniczna 2: InstantQueue a TimedQueue

Możesz czasem zobaczyć w logu debugowania komunikat `Forcing queue (NAZWA) into a timed queue...`. Dzieje się tak, ponieważ wewnętrznie większość kolejek jest domyślnie kolejkami natychmiastowymi („instant”), a mogą stać się kolejkami czasowymi („timed”), jeśli muszą poczekać, np. przy użyciu polecenia `wait`. Rozróżnienie między rodzajami kolejek istnieje głównie z powodów wewnętrznych/technicznych – nie musisz się tym martwić, po prostu wiedz, że taki komunikat debugowania jest normalny i oznacza po prostu, że system wewnętrzny wykonuje swoją pracę.

### Kolejki nie zawsze pochodzą ze skryptu

Choć zazwyczaj mówimy o kolejkach w kontekście pojedynczego skryptu, z którego zostały stworzone, istnieją wyjątki.

Pierwszym wyjątkiem są oczywiście polecenia `/ex` oraz `/exs`, gdzie kolejki są tworzone z polecenia wpisanego bezpośrednio w oknie czatu w grze.

Drugim wyjątkiem jest użycie polecenia `inject`, co zostanie wyjaśnione szerzej na [stronie opcji uruchamiania (Run Options)](/guides/basics/run-options).

Powiązana uwaga dodatkowa: każde polecenie uruchamiane w Denizen zawsze znajduje się wewnątrz jakiejś kolejki, jednak tagi mogą być czasem przetwarzane poza jakąkolwiek kolejką.

### Czy ta kolejka działa?

Jako bardziej zaawansowany przykład tego, co można zrobić z systemem kolejek, oto przykładowy skrypt, który uruchamia inną kolejkę i śledzi, czy wciąż ona działa.

```dscript_green
start_a_queue:
    type: task
    script:
    - run wild_queue save:myqueue
    - while <entry[myqueue].created_queue.is_valid>:
        - narrate "Kolejka posiada jak dotąd <entry[myqueue].created_queue.determination.size> determinacji"
        - wait 1s
    - narrate "Kolejka zakończyła się i ustaliła: <entry[myqueue].created_queue.determination.formatted>"

wild_queue:
    type: task
    script:
    - while true:
        - determine passively <util.random_boolean>
        - if <util.random_chance[20]>:
            - stop
        - wait 0.5s
```

Użyj `/ex run start_a_queue`, aby wypróbować ten przykład.

Pokazuje on uruchomienie oddzielnej kolejki, a następnie śledzenie, czy wciąż trwa i jakie decyzje (determinations) podjęła. Przykład „`wild_queue`” po prostu działa przez losowy czas, ustalając losowe wartości w trakcie swojego trwania.

Zauważ, że pierwsza narracja może czasem od razu brzmieć „Kolejka zakończyła się...”, a gdy tak nie jest, zawsze brzmi „Kolejka posiada jak dotąd 1 ...”

Dzieje się tak, ponieważ polecenie `run` uruchamia nową kolejkę *natychmiastowo* – zatem kolejka zadania `start_a_queue` wstrzymuje się i czeka, aż `wild_queue` albo skończy, albo sama się wstrzyma. Gdy `wild_queue` zostanie wstrzymana, kontrola nad serwerem natychmiast wraca do kolejki skryptu `start_a_queue`, która przechodzi do wykonania polecenia `while` i pierwszej `narrate`, a następnie dociera do własnego polecenia `wait`. W tym momencie serwer może wreszcie przetworzyć inne rzeczy, w tym standardową logikę gry Minecraft.

Jeśli `wild_queue` przejdzie pomyślnie pierwszą szansę `random_chance` i zatrzyma się, sekwencja zdarzeń wygląda tak: `start_a_queue` uruchamia `wild_queue`, `wild_queue` startuje pętlę, podejmuje jedną decyzję, zatrzymuje się, następnie `start_a_queue` sprawdza warunek `while` ale nie wchodzi w pętlę, wykonuje swoją końcową `narrate` i kończy pracę – wtedy i tylko wtedy reszta gry może kontynuować działanie.

### Denizen jest szybki

Na koniec warto zauważyć, że niektóre sekcje powyższej strony omawiają koncepcję wstrzymywania pracy serwera i czekania na zakończenie kolejek. Może to sprawiać wrażenie, że gra może się „zamrozić” lub lagować, jednak ważne jest, aby zrozumieć, że mówimy o skalach czasowych na poziomie procesora – komputery są bardzo szybkie, przeciętny nowoczesny procesor wykonuje dosłownie miliardy operacji na sekundę. Czas, w którym serwer czeka na kolejkę, to zazwyczaj kilka nanosekund, czasem kilka milisekund. Prawie wszystko przetwarzane na serwerze normalnie powoduje, że serwer czeka na zakończenie danej operacji – tak po prostu działają komputery, przetwarzają one rzeczy w określonej kolejności <span class="parens">(wyłączając sytuacje, gdy używana jest wielowątkowość, co nie dotyczy serwerów Minecraft, które są w dużej mierze jednowątkowe)</span>.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o kolejkach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Polecenia ex oraz exs](https://meta.denizenscript.com/Docs/Languages/ex%20command)
- [Typ obiektu QueueTag](https://meta.denizenscript.com/Docs/ObjectTypes/queuetag)
- [Lista tagów QueueTag](https://meta.denizenscript.com/Docs/Tags/queuetag)
- [Tag ScriptTag.queues](https://meta.denizenscript.com/Docs/Tags/ScriptTag.queues)
- [Tag util.queues](https://meta.denizenscript.com/Docs/Tags/util.queues)
- [Polecenie RunLater](https://meta.denizenscript.com/Docs/Commands/runlater)
- [Dokumentacja języka „The Player and NPC arguments”](https://meta.denizenscript.com/Docs/Languages/the%20player%20and%20npc%20arguments)
- [Dokumentacja języka „the save argument”](https://meta.denizenscript.com/Docs/Languages/the%20save%20argument)
- [Dokumentacja języka „waitable commands”](https://meta.denizenscript.com/Docs/Languages/waitable)
