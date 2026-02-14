FAQ dla programistów
-------------------

Ta strona odpowie na kilka typowych pytań od programistów uczących się Denizen. Tematy te niekoniecznie będą miały sens dla osób bez doświadczenia w programowaniu.

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czy Denizen jest podobny do (Wpisz Język Programowania)?

Jeśli tym językiem jest C/C#/Java/JavaScript/cokolwiek innego w tym zakresie, odpowiedź brzmi stanowczo „nie”. W przypadku innych rozważ poniższe powszechne porównania:

Pod względem wizualnym często mówi się, że Denizen przypomina Pythona.

Pod względem logiki Denizen jest bardzo zbliżony do Basha lub innych skryptowych języków wiersza poleceń.

Aby zrozumieć dlaczego, warto poznać podstawowe kategorie składni języków programowania:

- Składnia funkcyjna <span class="parens">(nie mylić z programowaniem funkcyjnym, które jest czymś innym)</span> to składnia znana większości nowoczesnych programistów. Jest używana w C, C++, C#, Javie, JavaScript i wielu innych <span class="parens">(wszystkie języki o „składni C” należą do tej kategorii)</span>. Ta kategoria charakteryzuje się tym, że cały kod znajduje się wewnątrz funkcji <span class="parens">(lub „metod”)</span> i składa się głównie z wywołań innych funkcji. Zazwyczaj odbywa się to za pomocą formatu z nawiasami i argumentami oddzielonymi przecinkami, np. `JakasFunkcja(arg1, arg2)`. **Python** nie posiada „składni C”, ale wciąż jest to składnia funkcyjna.
- Istnieją różne inne kategorie składni <span class="parens">(jak te używane w LISP, Haskell, Ruby itp.)</span>, ale nie są one istotne w tym momencie.
- Składnia Polecenie+Tag (Command+Tag) to składnia, którą każdy administrator systemów poznaje w tej czy innej formie. Charakteryzuje się ona tym, że naturalnie dobrze sprawdza się przy wpisywaniu w wierszu poleceń. Składa się z serii linii zawierających polecenie, a po nim listę argumentów (zazwyczaj oddzielonych spacjami). Wszystkie dane wejściowe są interpretowane jako surowe wartości, ale istnieją znaki specjalne oznaczające, że dany fragment ma zostać przetworzony jako wywołanie wyszukiwania, a wynik ma zastąpić to miejsce. Jest to składnia używana przez Bash, Wiersz polecenia, PowerShell <span class="parens">(w większości)</span> i oczywiście... Denizen.

Rozważmy na przykład linię w Bashu: `ls -la $FOLDER` – uruchamia ona polecenie `ls` <span class="parens">(lista plików)</span> z pierwszym argumentem będącym surowym tekstem `-la` <span class="parens">(który zostanie zinterpretowany jako flagi stylu listy)</span> oraz drugim argumentem poprzedzonym symbolem `$`, wskazującym, że należy go wyszukać w liście zmiennych środowiskowych <span class="parens">(prawdopodobnie zmienna zawierająca ścieżkę do folderu)</span>. Wejście `$FOLDER` zostanie zastąpione rzeczywistą ścieżką, a wykonane polecenie będzie wyglądać jak `ls -la moj_katalog/`.

Denizen pasuje do tej samej podstawowej kategorii składni... rozważmy na przykład linię: `narrate "witajcie!" targets:<[players]>` – uruchamia ona polecenie `narrate` z pierwszym argumentem będącym surowym tekstem `witajcie!` <span class="parens">(zinterpretowanym jako wiadomość do wyświetlenia)</span> oraz drugim argumentem używającym `<>`, aby wskazać wyszukiwanie tagu, i `[]`, aby wskazać wyszukiwanie definicji <span class="parens">(prawdopodobnie listy graczy)</span>. Wykonane polecenie ostatecznie będzie wyglądać tak: `- narrate "witajcie!" targets:li@p@bob|p@joe`.

### Czy Denizen jest obiektowy (OOP)?

Krótka odpowiedź: **Nie.**

Średnia odpowiedź: Denizen bazuje na obiektach, ale nie jest wyłącznie zorientowany obiektowo.

Długa odpowiedź: Denizen nie pasuje do kategorii „czystego OOP” <span class="parens">(jak Java)</span> ani „wieloparadygmatowego, ale mogącego być OOP” <span class="parens">(jak C#)</span>.

Denizen używa obiektów do reprezentowania wszystkich danych, co czyni go językiem opartym na obiektach (object-based). Pozwala on również na definiowanie własnych typów obiektów, ale aktywnie to odradza.

Denizen został zaprojektowany do pracy z bardzo zwięzłymi, szybkimi liniami kodu <span class="parens">(możesz osiągnąć stosunkowo złożone cele w skrypcie o jednocyfrowej liczbie linii dzięki licznym, wszechstronnym poleceniom narzędziowym)</span>, a naturalnie rozwlekła natura OOP stoi w sprzeczności z tym celem. Zadawanie sobie trudu sztywnego definiowania własnych typów obiektów po prostu nie przynosi wielkich korzyści w języku, w którym większość metod/funkcji i tak miałaby tylko 1 lub 2 linie długości. Ponadto definiowanie długich, starannych obiektów zmusiłoby Cię do pisania długich, skomplikowanych skryptów, które w składni Denizen byłyby znacznie trudniejsze do śledzenia niż bezpośrednio napisana logika imperatywna.

### Czy Denizen jest Open Source?

Denizen to w 100% wolne i otwarte oprogramowanie (FOSS) na licencji MIT.

Głęboko wierzymy, że publikowanie pełnego, czytelnego i możliwego do modyfikacji kodu źródłowego naszej pracy jest z natury korzystne dla dobra ogólnego: daje niemal nieograniczoną wolność naszym użytkownikom, pozwala każdemu posiadającemu umiejętności przyczynić się do tego, by Denizen był jak najlepszy, i gwarantuje, że projekt przetrwa i wciąż będzie dostępny dla użytkowników, nawet jeśli wydarzy się coś, co zakłóci jego normalny rozwój <span class="parens">(np. obecni deweloperzy zaprzestaną prac lub projekt zostanie przejęty przez kogoś, kto nie chciałby go publikować na uczciwych warunkach)</span>.

Implementacja Denizen dla Spigot znajduje się na GitHubie [tutaj](https://github.com/DenizenScript/Denizen). Rdzeń kodu Denizen jest [tutaj](https://github.com/DenizenScript/Denizen-Core). Możesz również znaleźć źródła tego przewodnika [tutaj](https://github.com/DenizenScript/Denizen-Beginners-Guide). Może Cię również zainteresować bot pomocniczy do dokumentacji na Discorda [tutaj](https://github.com/DenizenScript/DenizenMetaBot) lub rozszerzenie do VS Code [tutaj](https://github.com/DenizenScript/DenizenVSCode).

### Czy mogę zgłosić Pull Request do Denizen?

Jasne! Przez lata zaakceptowaliśmy mnóstwo pull requestów od wielu różnych współtwórców. Prosimy jednak o przedyskutowanie planowanych zmian [na Discordzie](https://discord.gg/Q6pZGSR) przed ich zgłoszeniem, aby uniknąć marnowania czasu na coś, co jest już dostępne, lub po prostu aby upewnić się, że wiesz, jak podejść do danego zadania w poprawny sposób.

Kiedy tworzysz Pull Request, zostaniesz automatycznie poproszony o zaakceptowanie umowy CLA, która mówi, że zgadzasz się, aby Twoje wkłady zostały w pełni przekazane projektowi Denizen oraz że akceptujesz fakt, iż nigdy nie będziesz mógł zabronić ani ograniczyć użycia tego wkładu po jego włączeniu do kodu. Stosujemy to, aby pomóc zagwarantować, że Denizen zawsze pozostanie wolny i nieograniczony dla wszystkich.
