Konfiguracja edytora skryptów
-----------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Edytor

![](images/scripteditor.png)

Oficjalnie zalecanym sposobem edycji skryptów Denizen jest użycie VS Code z rozszerzeniem Denizen!

### Instalacja

- Po pierwsze, pobierz i zainstaluj [VS Code](https://code.visualstudio.com/). Pamiętaj, że to NIE jest to samo co „Visual Studio”, mimo podobnej nazwy. <span class="parens">(Nazewnictwo jest niestety dość mylące. To sprawka Microsoftu...)</span>
- Po drugie, po zainstalowaniu VS Code możesz zainstalować [rozszerzenie Denizen](https://marketplace.visualstudio.com/items?itemName=DenizenScript.denizenscript), klikając zielony przycisk „Install” na podlinkowanej stronie.
- <span class="parens">(Rozszerzenie automatycznie zainstaluje również środowisko [.NET 6.0](https://dotnet.microsoft.com/download/dotnet/6.0). Niektórzy użytkownicy zgłaszali konieczność ponownego uruchomienia komputera, aby ta instalacja została w pełni ukończona)</span>.

### Sposób użycia

- Otwórz folder ze skryptami w VS Code – znajduje się on w `plugins/Denizen/scripts/` w katalogu Twojego serwera <span class="parens">(otwórz sam folder, a nie pojedyncze pliki – dzięki temu zobaczysz drzewo plików po lewej stronie edytora)</span>.
- Rozszerzenie Denizen będzie automatycznie aktywne dla wszystkich plików z rozszerzeniem `.dsc`. <span class="parens">(Uwaga: historycznie dla skryptów używano rozszerzenia `.yml`. Nie jest ono już zalecane, a pliki `.yml` nie będą miały podświetlania składni Denizen. Musisz używać `.dsc`)</span>.
- W większości przypadków po prostu zacznij edytować pliki skryptów w taki sam sposób, w jaki edytowałbyś dowolny plik tekstowy w VS Code.

### Korzystanie z przewodnika

Jeśli korzystasz z przewodnika po raz pierwszy, po prostu zainstaluj edytor zgodnie z powyższymi instrukcjami i przejdź do następnej strony. Reszta tej strony jest dla użytkowników, którzy chcą dowiedzieć się więcej o możliwościach edytora skryptów.

### Funkcje

- Podczas przeglądania pliku `.dsc` składnia będzie automatycznie odpowiednio podświetlana dla skryptów Denizen.

<br>

- Podczas pisania skryptów rozszerzenie będzie sprawdzać błędy i podświetlać je, gdy zostaną znalezione. Zostaną one również wymienione na liście „Problems” (Problemy) w VS Code.

![](images/error-checking.png)

<br>

- Jeśli wpiszesz nazwę kontenera, taką jak `task`, na początku linii, możesz użyć autouzupełniania (tab-complete), aby wstawić szkielet (snippet) tego kontenera.

![](images/snippet_demo.gif)

<br>

- W dowolnym momencie możesz najechać myszą na dowolne polecenie, tag, zdarzenie, typ kontenera..., aby wyświetlić podgląd meta-dokumentacji dla tej wartości.

![](images/hover_meta_doc.png)

<br>

- Podczas wpisywania poleceń/tagów/itp. wyświetlane będą opcje autouzupełniania dla wszystkich prawidłowych tagów/poleceń/itp. wraz z ich dokumentacją.

![](images/docs_as_you_type.gif)

Uwaga: jeśli widzisz tylko autouzupełnianie bez pełnej dokumentacji, kliknij małą strzałkę po prawej stronie.

![](images/expand_meta_docs.png)

<br>

- W kilku miejscach zobaczysz również opcje autouzupełniania dla podstawowych typów danych, takich jak materiały, typy encji itp.

![](images/item_tab_complete.png)

<br>

- Jeśli otworzysz okno ustawień VS Code i wyszukasz `Denizen`, znajdziesz szereg opcji umożliwiających włączenie/wyłączenie różnych funkcji, zmianę kolorów podświetlania składni itp.
