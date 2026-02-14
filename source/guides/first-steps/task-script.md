Twój pierwszy skrypt zadania (Task Script)
------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Podstawy skryptu zadania

Skrypt zadania (task script) to samodzielny skrypt, który można uruchomić za pomocą polecenia w grze [/ex](/guides/first-steps/ex-command) lub za pomocą polecenia `run` w innym skrypcie.

Skrypty zadań wykonają wszystkie zawarte w nich polecenia Denizen. Skrypt zadania może być prosty, skomplikowany, a nawet może uruchamiać inne skrypty zadań. Jest to przydatne do tworzenia łańcuchów skryptów opartych na logice, takich jak opcje dialogowe lub losowa zawartość.

### Składnia skryptu zadania

Oto przykład podstawowego skryptu zadania.

```dscript_green
example_task:
    type: task
    script:
    - narrate "To jest podstawowy skrypt zadania!"
```

Ten skrypt wyświetli tekst: `To jest podstawowy skrypt zadania!` graczowi przypisanemu do skryptu. Jeśli użyjesz polecenia `/ex` do uruchomienia tego skryptu – konkretnie wpisując w grze `/ex run example_task` – *Ty* będziesz przypisanym graczem.

Zauważ, że ten przykład skryptu jest podświetlony na zielono. Oznacza to, że jest na tyle poprawny, że możesz go skopiować/wkleić i będzie działał. Dalej na tej stronie znajduje się przykład skryptu podświetlony na niebiesko, co oznacza, że jest to dobry przykład, ale jakaś jego część wymaga uzupełnienia. Później zobaczysz przykłady na czerwono, wskazujące na to, czego *nie* należy robić. Więcej informacji na temat tego systemu podświetlania znajdziesz w sekcji [Ten przewodnik - Przykładowe skrypty](/guides/this-guide/sample-scripts).

### Tworzenie pierwszego skryptu zadania w VS Code

Wcześniej dowiedziałeś się, [jak skonfigurować VS Code](/guides/first-steps/script-editor), edytor, który polecamy do pisania skryptów Denizen.

#### Tworzenie pliku

Aby utworzyć swój pierwszy skrypt zadania, zacznij od otwarcia folderu ze skryptami w VS Code.

![](images/fileopenfolder.png)
![](images/fileopenscriptsfolder.png)

Następnie kliknij prawym przyciskiem myszy folder ze skryptami w menu eksploratora i wybierz opcję „New File” (Nowy plik).

![](images/rightclicknewfile.png)

Wpisz dowolną nazwę pliku, upewnij się tylko, że kończy się ona rozszerzeniem `.dsc` – wymaganym dla plików skryptów Denizen.

![](images/typefilenametest.png)

Teraz możesz zacząć pisać swój pierwszy skrypt zadania!

#### Pisanie skryptu

Zacznijmy od rdzenia skryptu:

```dscript_blue
my_first_task:
    type: task
    script:
    - narrate (tekst)
```

Ten przykład powinien wyglądać znajomo – jest bardzo podobny do przykładu powyżej.

Powinieneś go napisać, wpisując najpierw `my_first_task:` <span class="parens">(pamiętając o użyciu znaków podkreślenia `_` zamiast spacji i dwukropka `:` na końcu)</span>, następnie naciśnij klawisz ENTER, aby przejść do nowej linii, oraz klawisz TAB, aby zrobić wcięcie dla kolejnego bloku. Podczas pisania kolejnych linii poziom wcięcia pozostanie taki sam, co oznacza, że nie musisz ponownie naciskać klawisza TAB, dopóki nie będziesz musiał zrobić jeszcze większego wcięcia, co będzie potrzebne w późniejszych przykładach.

#### Nazwy skryptów

Nazwa skryptu tutaj to `my_first_task`. Znajduje się ona na samym początku linii <span class="parens">(co oznacza, że nie ma spacji na początku linii, podczas gdy wszystkie inne linie mają dokładnie 4 spacje na początku – są one bardziej wcięte)</span>. Każdy wpis na najwyższym poziomie wcięcia to oddzielny skrypt. Oto przykład dwóch różnych skryptów:

```dscript_green
my_first_task:
    type: task
    script:
    - narrate "To jest zadanie numer jeden!"

my_second_task:
    type: task
    script:
    - narrate "To jest zadanie numer dwa!"
```

Ten przykład pokazuje, jak wyglądają dwa różne skrypty w tym samym pliku. `my_first_task` i `my_second_task` to dwa różne skrypty, a każdy z nich jest w pełni samodzielny.

Dodatkowo pamiętaj, że *nazwy skryptów* i *nazwy plików* to dwie różne rzeczy. Zarówno `my_first_task`, jak i `my_second_task` mogą znajdować się w tym samym pliku o nazwie `moje_pierwsze_skrypty.dsc`. Liczy się nazwa skryptu <span class="parens">(ta `my_first_task`)</span>, nazwa pliku <span class="parens">(`moje_pierwsze_skrypty.dsc`)</span> służy wyłącznie Twojej własnej organizacji i nie ma znaczenia dla Denizen. Pamiętaj też, że możesz swobodnie tworzyć podfoldery wewnątrz folderu `scripts`, a wszystkie pliki skryptów w nich zawarte zostaną załadowane tak samo.

#### Typy skryptów

Poniżej nazwy skryptu – na drugim poziomie wcięcia – zobaczysz klucz `type`.

Klucz `type` służy do określenia, jakiego typu skrypt Denizen piszesz. W tym przypadku piszemy skrypt typu `task`. Mogłeś już widzieć skrypty `world`, `item`, `inventory` lub którykolwiek z wielu innych istniejących typów. Na razie skupimy się na skryptach `task`. Dowiesz się o różnych typach w miarę czytania tych przewodników, a wyjaśnienia na temat ich wszystkich znajdziesz w meta-dokumentacji na [stronie wyjaśniającej język](https://meta.denizenscript.com/Docs/Languages/container).

Upewnij się, że w swoim skrypcie na drugim poziomie wcięcia wpisałeś `type: task` <span class="parens">(naciśnij **enter**, aby zacząć nową linię, a następnie **tab**, aby zrobić wcięcie. To doda 4 spacje w linii, a po każdym kolejnym naciśnięciu enter automatycznie znów pojawią się 4 spacje, dopóki nie naciśniesz backspace, aby je usunąć)</span>, pod nazwą skryptu, w ten sposób:

```dscript_blue
my_first_task:
    type: task
```

#### Polecenia skryptu

Poniżej klucza `type` zobaczysz klucz `script`. W przypadku skryptu zadania, pod kluczem `script` wpisujesz treść skryptu – zestaw instrukcji, które mówią Denizen, co ma robić. Inne typy skryptów używają innych kluczy, o których dowiesz się w dalszej części tego przewodnika. Większość rzeczywistej pracy, którą wykonasz w Denizen, będzie odbywać się pod kluczami takimi jak ten.

Spójrzmy na inny przykładowy skrypt:

```dscript_green
my_first_task:
    type: task
    script:
    - narrate "To jest poprawny skrypt zadania!"
    - narrate "Gratulacje z okazji napisania pierwszego skryptu!"
```

Zauważ, że tekst, który chcemy wyświetlić – `To jest poprawny skrypt zadania!` – jest ujęty w cudzysłów `""`. Ponieważ wiadomość, którą chcemy wyświetlić, zawiera spacje, umieszczamy ją w cudzysłowie, aby upewnić się, że polecenie ma tylko jeden argument. Polecenia Denizen mają argumenty oddzielone spacjami. Na przykład `run jeden dwa trzy` ma argumenty `jeden`, `dwa` i `trzy`, ale `run "jeden dwa trzy"` ma tylko jeden argument: `jeden dwa trzy`.

### Ukończony produkt

W tym momencie powinieneś mieć skrypt zadania gotowy do uruchomienia! No dalej, spróbuj – najpierw wpisz w grze `/ex reload`, aby załadować nowy skrypt, a następnie użyj polecenia `/ex run (NazwaTwojegoZadania)`, aby uruchomić zadanie w grze. Na przykład polecenie w grze do uruchomienia powyższego skryptu to `/ex run my_first_task`.

Jeśli widzisz tekst, który napisałeś dla polecenia `narrate`, pomyślnie napisałeś swój pierwszy skrypt zadania. Gratulacje!

![](images/runfirsttaskscript.png)

### Krok dalej

Aby nieco rozwinąć to, czego się tutaj nauczyłeś – pamiętając, że chociaż używaliśmy polecenia `run` przez `/ex`, jest to polecenie skryptowe Denizen <span class="parens">(polecenie `/ex` to tylko narzędzie w grze do szybkiego uruchamiania dowolnego polecenia skryptowego)</span> – spójrzmy na przykład, który robi nieco więcej niż poprzednie.

Możesz również użyć jednego skryptu zadania, aby uruchomić inny skrypt zadania. Wygląda to tak:

```dscript_green
my_first_task:
    type: task
    script:
    - narrate "To jest poprawny skrypt zadania!"
    - narrate "Gratulacje z okazji napisania pierwszego skryptu!"
    - run my_second_task

my_second_task:
    type: task
    script:
    - narrate "To jest Twój drugi skrypt zadania!"
```

To tylko podstawowy przykład, a będziesz mógł tworzyć bardziej skomplikowane i potężne skrypty, w miarę jak będziesz uczyć się więcej o Denizen.

Warto zauważyć, że w powyższym przykładzie `my_first_task` i `my_second_task` to oddzielne *kontenery skryptów* i oba mogą znajdować się w jednym *pliku skryptu* lub w różnych plikach – dla Denizen nie ma to znaczenia.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o skryptach zadań, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja kontenerów skryptów zadań](https://meta.denizenscript.com/Docs/Languages/task%20script%20containers)
- [Dokumentacja polecenia Run](https://meta.denizenscript.com/Docs/Commands/run)
