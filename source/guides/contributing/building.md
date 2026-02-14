Praca z Denizen i jego budowanie
--------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Wymagania wstępne

Upewnij się, że masz zainstalowane następujące narzędzia:

- Git. Polecamy [GitHub Desktop](https://desktop.github.com/) <span class="parens">(jeśli używasz Linuxa, prawdopodobnie znajdziesz odpowiedni pakiet dla swojej dystrybucji)</span>.
- [IntelliJ IDEA](https://www.jetbrains.com/idea/). Ten edytor jest specjalnie zaprojektowany dla projektów w Javie i sam zajmie się obsługą zależności.

### Budowanie Spigota

Aby zbudować Denizen, będziesz musiał również zbudować wspierane wersje Spigota, które znajdziesz w [pliku README projektu](https://github.com/DenizenScript/Denizen#readme). Spigot udostępnia do tego narzędzie zwane [BuildTools](https://www.spigotmc.org/wiki/buildtools/). Nie można go zastąpić gotowymi plikami jar ani forkami, ponieważ biblioteki muszą zostać zbudowane bezpośrednio w Twoim lokalnym repozytorium Maven.

Postępuj zgodnie z instrukcjami konfiguracji, a następnie uruchom plik jar BuildTools dla każdej wersji wspieranej przez Denizen. Powinno to być polecenie typu: `java -jar BuildTools.jar --rev 1.21.1 --remapped`.

### Budowanie Denizen

1. Sklonuj repozytorium Denizen: https://github.com/DenizenScript/Denizen
2. Otwórz sklonowany folder w IntelliJ.
3. Otwórz kartę Maven, wybierz `denizen-parent` i kliknij „Run Maven Build” <span class="parens">(zielona strzałka)</span>.

Po zbudowaniu plik jar będzie dostępny w folderze `target`. Możesz go włożyć do folderu plugins swojego serwera testowego. Możesz powtarzać krok 3, aby budować projekt po wprowadzeniu jakichkolwiek zmian w kodzie.
