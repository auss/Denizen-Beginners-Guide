Konfiguracja lokalnego serwera testowego
------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Potrzebujesz serwera testowego

Prawdopodobnie patrzysz na tę stronę i myślisz: „Mam już serwer, po co innego miałbym tu być?”... Większość ludzi ma *serwer*, ale większość ludzi **powinna** mieć *serwer produkcyjny i serwer testowy*. Jaka jest różnica?

- **Serwer produkcyjny** to ten, na który mogą wchodzić gracze i którego prawdopodobnie nie chcesz nieoczekiwanie zawiesić lub zepsuć błędami.
- **Serwer testowy** to taki, na którym będziesz tylko Ty i ewentualnie jeden przyjaciel, i który można bez obaw zawiesić lub w inny sposób uszkodzić.

Kiedy piszesz skrypty, zwłaszcza gdy jesteś początkujący, całkiem możliwe jest przypadkowe zrobienie czegoś, co zawiesi serwer lub zrobi coś komicznego, jak sprawienie, że trawa eksploduje za każdym razem, gdy ją zniszczysz... ogranicz to do serwera testowego, abyś przypadkowo nie zrujnował dnia żadnemu graczowi swoimi eksperymentami.

### Powinieneś mieć go lokalnie

Niektórzy ludzie mają już *zdalny* serwer testowy... to właściwie określa się mianem „serwera stagingowego” – czyli serwera, na którym wszystko jest skonfigurowane tak, jak na serwerze produkcyjnym, ale bez przeszkadzających graczy. Serwer testowy to taki, który możesz usunąć, zaśmiecić, opróżnić, powykręcać lub zrobić z nim cokolwiek innego pod wpływem najmniejszego kaprysu, bez angażowania kogokolwiek innego.

Najłatwiejszym i najlepszym sposobem na to jest po prostu uruchomienie serwera na własnym komputerze (nie martw się, to naprawdę proste).

### Jak skonfigurować lokalny serwer

To całkiem proste! Przejdźmy przez kolejne kroki...

**Uczciwe ostrzeżenie:** ten przewodnik koncentruje się na komputerach z systemem Windows, ponieważ są one najpopularniejsze (ale zaznaczymy różnice dla innych systemów operacyjnych tam, gdzie ma to zastosowanie).

- **Krok 0: Zainstaluj Javę.** Możliwe, że nie masz jeszcze zainstalowanej Javy na swoim komputerze – jeśli tak, będziesz musiał ją zainstalować. Możesz pobrać [OpenJDK 21 stąd](https://adoptium.net/?variant=openjdk21). <span class="parens">(dla użytkowników Linux/Mac: po prostu sprawdź w Google, jak pobrać OpenJDK 21 dla Twojego konkretnego systemu/dystrybucji)</span>. Podczas instalacji upewnij się, że zaznaczyłeś opcje „Add to PATH” oraz „Set JAVA_HOME variable”. Pamiętaj, że poprawna wersja Javy do zainstalowania zależy od wersji Twojego serwera Minecraft. Obecne wersje używają JDK 21, w niedalekiej przeszłości używano JDK 17, a w odległej przeszłości standardem był Java 8.

![](images/javainstall.png)

- **Krok 1: Pobierz plik jar serwera.** Konwencjonalnie byłby to Spigot... jednak pobranie Spigota w poprawny sposób jest nieco uciążliwe. Możesz przeczytać [stronę Wiki BuildTools](https://www.spigotmc.org/wiki/buildtools/), jeśli chcesz to zrobić, ale zamiast tego polecam po prostu użyć Paper, który pobiera się jednym kliknięciem – [Pobierz Paper tutaj](https://papermc.io/downloads). Dla wyjaśnienia: Paper to fork Spigota zaprojektowany do wydajniejszego działania, z dodatkową zaletą w postaci znacznie łatwiejszego startu. W 99% przypadków pliki jar Paper i Spigot są w praktyce zamienne bez żadnych problemów.

Zauważ też, że wielu użytkowników lubi zmieniać nazwę pliku jar na `paper.jar` <span class="parens">(czyli usunąć wersję z nazwy pliku)</span>, aby uniknąć konieczności modyfikowania skryptu startowego, który utworzysz w kroku 3.

![](images/paperdownload.png)

- **Krok 2: Przygotuj folder serwera.** Możesz po prostu utworzyć folder w dowolnym miejscu, nawet na pulpicie, upewnij się tylko, że w folderze nie ma nic innego <span class="parens">(ponieważ serwer wygeneruje wokół siebie mnóstwo plików)</span>. Włóż pobrany plik jar serwera do tego folderu.

![](images/myserverfolder.png)

- **Krok 3: Utwórz skrypt startowy.** Najprostszym sposobem na to jest otwarcie Notatnika i utworzenie pliku o nazwie `start.bat` (pamiętaj, aby zapisać jako „wszystkie pliki”, a nie „dokumenty tekstowe”). W tym pliku wpisz: `java -Xms1G -Xmx1G -jar paper.jar nogui`... możesz skonfigurować szczegóły – „1G” powtórzone dwa razy to ilość pamięci RAM, którą chcesz przydzielić serwerowi <span class="parens">(„1G” oznacza jeden gigabajt pamięci RAM, możesz też wpisać wartości takie jak „2G” dla 2 gigabajtów, „500M” dla 500 megabajtów itd. Jeśli to zmienisz, upewnij się, że zmienisz obie wartości: „Xms” i „Xmx”)</span>, a „paper.jar” to nazwa pliku jar serwera. <span class="parens">(Na Linuxie lub Macu utwórz plik `start.sh` i edytuj go w swoim ulubionym edytorze tekstu. Polecenie pozostaje takie samo)</span>.

![](images/startbat.png)

- **Krok 4: Uruchom serwer po raz pierwszy.** Aby to zrobić, po prostu kliknij dwukrotnie plik skryptu startowego. Jeśli wszystko pójdzie dobrze, na chwilę pojawi się okno wiersza poleceń, wyświetli kilka komunikatów, a następnie zniknie, a Ty otrzymasz nowy plik `eula.txt`. Ponieważ Mojangiem rządzą teraz prawnicy, musisz otworzyć ten plik i zmienić `false` na `true`, aby wskazać, że akceptujesz EULA Minecrafta. Nie uruchamiaj jeszcze serwera ponownie.

![](images/seteulatotrue.png)

- **Krok 5: Dodaj wtyczki.** Utwórz folder `plugins`, jeśli jeszcze go nie masz, pobierz najnowszy plik jar dla Denizen, Citizens itd. <span class="parens">(wszelkie wtyczki, które chcesz dołączyć)</span> i umieść je w tym folderze. Dla serwera testowego prawdopodobnie będziesz chciał zainstalować najnowszą [rozwojową wersję Denizen (Developmental Build)](https://ci.citizensnpcs.co/job/Denizen_Developmental/) – choć dla serwera produkcyjnego zazwyczaj będziesz chciał najnowszą [wersję stabilną Denizen (Release Build)](https://ci.citizensnpcs.co/job/Denizen/).

![](images/denizeninpluginsfolder.png)

- **Krok 6: Uruchomienie na poważnie po raz pierwszy.** Uruchom ponownie skrypt startowy... za pierwszym razem załadowanie wszystkiego zajmie chwilę, ale przy kolejnych uruchomieniach będzie szybciej. Foldery wtyczek, dane świata itp. zostaną wygenerowane automatycznie.

![](images/firstlaunch.png)

- **Krok 7: Dołącz do serwera.** Dodaj serwer w menu gry wieloosobowej Minecraft, ustawiając adres na `localhost`. Jest to automatyczny adres, który oznacza po prostu „serwer na tym samym komputerze, na którym gram”. Powinieneś móc bez problemu dołączyć, jeśli nic nie poszło nie tak (a jeśli coś poszło nie tak, powinieneś mieć gdzieś komunikat o błędzie do sprawdzenia lub zgłoszenia). Możesz użyć okna wiersza poleceń, aby nadać sobie opa lub cokolwiek innego, czego potrzebujesz. Trzymaj to okno na widoku, ponieważ wyjście debugowania przesyłane do tego okna przyda się później.
