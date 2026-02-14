Dodawanie nowych właściwości i narzędzi
-------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

Powinieneś być już zaznajomiony z używaniem tagów i mechanizmów wewnątrz skryptów. Ta strona przeprowadzi Cię przez proces tworzenia własnych tagów, mechanizmów i właściwości (properties) w Javie.

### Rejestracja tagów

Tagi, które nie należą do właściwości, są rejestrowane w definicji klasy danego typu obiektu, konkretnie w statycznej metodzie `registerTags`. Spójrz na klasę `PlayerTag` znajdującą się w pakiecie `objects`.

Każdy typ tagu posiada **procesor tagów** (tag processor), który jest zdefiniowany jako statyczny element klasy danego typu pod nazwą `tagProcessor`. Aby zarejestrować tag, należy wywołać `registerTag` na tym obiekcie. Metoda ta przyjmuje trzy parametry: klasę zwracanego typu, nazwę tagu oraz wyrażenie lambda przyjmujące dwa własne parametry.

```java
tagProcessor.registerTag(ElementTag.class, "nazwa_tagu", (attribute, object) -> {
    return new ElementTag("Witaj, świecie!");
});
```

Wyjaśnijmy tę lambdę dokładniej. Parametr `attribute` jest instancją klasy `Attribute`, która jest ogólnym obiektem narzędziowym zawierającym wszystkie informacje o przetwarzanym tagu oraz szereg metod pomocniczych. Na przykład możesz uzyskać dostęp do parametru przez `attribute.getParam()`. Gdy coś pójdzie nie tak, wywołujesz `attribute.echoError("...")` i zwracasz null.

Parametr `object` to po prostu instancja aktualnej klasy. Oznacza to, że w klasie `PlayerTag`, `object` będzie typu `PlayerTag`.

Korzystając z tych informacji, stwórzmy tag o nazwie `uuid_uppercase`, który zwraca UUID gracza i zamienia wszystkie litery na wielkie. Nie ma to większego sensu użytkowego; służy jedynie celom demonstracyjnym i ćwiczeniowym.

Chcemy zwrócić pojedynczy ciąg znaków, więc naszym typem zwracanym będzie `ElementTag`. Dodatkowo możemy użyć metody `getUUID()` na obiekcie `object`, aby pobrać UUID gracza. `UUID` to klasa narzędziowa Javy posiadająca metodę `toString()`, więc możemy jej użyć wraz z `toUpperCase()`, aby uzyskać zamierzony efekt.

Klasa `ElementTag` ma wiele konstruktorów, w tym jeden przyjmujący pojedynczy `String`, którego użyjemy.

```java
tagProcessor.registerTag(ElementTag.class, "uuid_uppercase", (attribute, object) -> {
    return new ElementTag(object.getUUID().toString().toUpperCase());
});
```

Umieść to w metodzie `registerTags`, zbuduj Denizen i wklej plik jar do folderu `plugins`. Teraz po uruchomieniu serwera i wpisaniu w grze `/ex narrate <player.uuid_uppercase>`, zobaczysz swój UUID zapisany wielkimi literami.

![](images/uuid_uppercase.png)

### Przyjmowanie parametrów tagu

Pobawmy się jeszcze trochę z UUID gracza. Edytujemy nasz tag `uuid_uppercase` tak, aby przyjmował parametr logiczny (boolean) określający, czy UUID ma zostać powtórzony dwukrotnie ze spacją pomiędzy.

Możemy użyć parametru `attribute`, aby sprawdzić, czy tag posiada wejście za pomocą `hasParam()`. Jeśli tak, możemy pobrać to wejście jako element przez `getParamElement()`. Klasa `ElementTag` posiada zestaw metod zwracających różne wartości prymitywne w zależności od wewnętrznej zawartości; możemy pobrać wartość logiczną, wywołując `asBoolean()`.

Powyższą logikę można zamienić w jedną instrukcję if:

```java
if (attribute.hasParam() && attribute.getParamElement().asBoolean()) {
    // ...
}
```

Możemy następnie przenieść nasz ciąg UUID do zmiennej i zmodyfikować go, jeśli warunek jest spełniony <span class="parens">(konkatenacja jest w porządku)</span>. Następnie po prostu zwracamy go jako element, jak wcześniej.

```java
tagProcessor.registerTag(ElementTag.class, "uuid_uppercase", (attribute, object) -> {
    String uuid = object.getUUID().toString().toUpperCase();
    if (attribute.hasParam() && attribute.getParamElement().asBoolean()) {
        uuid = uuid + " " + uuid;
    }
    return new ElementTag(uuid);
});
```

Przy takiej konfiguracji parametr tagu jest opcjonalny; nie musisz nawet używać `[]`. Jeśli jednak przetestujesz to i wpiszesz `true`, tag powinien zwrócić dwa wielkie UUID rozdzielone spacją.

### Dokumentowanie tagów: Wpisy meta

Mamy kompletny i działający tag, ale brakuje nam jeszcze jednej rzeczy: dokumentacji! Jeśli kiedykolwiek zastanawiałeś się, jak działa [strona z meta-dokumentacją](https://meta.denizenscript.com/), to w rzeczywistości przeszukuje ona cały kod Denizen i znajduje specjalne komentarze do przetworzenia. Nazywamy to **meta-dokumentacją**, ponieważ jest to dokumentacja istniejąca obok, ale nie jako bezpośrednia część funkcji, którą opisuje. Dla tagów wygląda to tak:

```text
// <--[tag]
// @attribute <ObjectTag.nazwa_tagu>
// @returns ObjectTag
// @mechanism ObjectTag.nazwa_mechanizmu
// @description
// To jest opis tagu.
// -->
```

Zauważ, że „attribute” to po prostu inny termin na tag. Pamiętaj, że musi to pokazywać dokładnie, jak tag powinien być używany, więc jeśli tag przyjmuje wejście, musisz to uwzględnić. Klucz `@mechanism` jest opcjonalny; jeśli występuje, oznacza to, że podany mechanizm jest bezpośrednim odpowiednikiem tagu.

Klucz `@returns` to typ tagu zwracanej wartości. W naszym przypadku jest to po prostu `ElementTag`, ponieważ zwracamy tekst. Czasem jednak musisz być bardziej precyzyjny; ElementTagi mogą zawierać wartości logiczne, liczby całkowite i dziesiętne. W takich przypadkach oznaczamy konkretny typ w nawiasach: odpowiednio `ElementTag(Boolean)`, `ElementTag(Number)` oraz `ElementTag(Decimal)`. Ta specyfikacja dotyczy również ListTagów: na przykład dla listy lokalizacji typem byłoby `ListTag(LocationTag)`.

Wpisy meta są umieszczane bezpośrednio nad kodem tagu. Oto jak wyglądałoby to przy naszym tagu:

```java
// <--[tag]
// @attribute <PlayerTag.uuid_uppercase[(<repeat>)]>
// @returns ElementTag
// @description
// Zwraca UUID gracza wielkimi literami.
// Opcjonalnie określa, czy UUID ma zostać powtórzony raz.
// -->
tagProcessor.registerTag(ElementTag.class, "uuid_uppercase", (attribute, object) -> {
    String uuid = object.getUUID().toString().toUpperCase();
    if (attribute.hasParam() && attribute.getParamElement().asBoolean()) {
        uuid = uuid + " " + uuid;
    }
    return new ElementTag(uuid);
});
```

Pamiętaj, że choć meta-dokumentacja jest dla Twojego edytora tylko komentarzem tekstowym, w rzeczywistości jest to bardzo rygorystyczny format, którego należy przestrzegać. Wiele usług automatycznie analizuje te metadane. Na przykład, jeśli zapomnisz nawiasów `()` wokół `<repeat>` w powyższym przykładzie, [edytor skryptów](/guides/first-steps/script-editor) wyświetli błąd w każdym skrypcie próbującym użyć tagu bez parametru.

### Tworzenie mechanizmów

**UWAGA**: Ta sekcja może ulec zmianie w przyszłości, gdy mechanizmy zostaną zaktualizowane o system rejestracji podobny do tego dla tagów.

W przeciwieństwie do nowoczesnych tagów, mechanizmy przechodzą serię sprawdzeń w celu „dopasowania” (match). Dzieje się to w metodzie `adjust` definicji klasy, która przyjmuje parametr `mechanism`. Mechanizmy mają różne metody do dopasowywania i sprawdzania wejścia, które zaraz zobaczymy.

Aby dopasować mechanizm, użyj metody `matches(String)` i podaj nazwę mechanizmu. Aby wymagać konkretnego typu wejścia, możesz skorzystać z metod zaczynających się od `require`, takich jak `requireBoolean()` czy `requireObject(Object)`. Otocz te metody instrukcją if, aby rozpocząć blok mechanizmu.

```java
if (mechanism.matches("jakis_mechanizm") && mechanism.requireBoolean()) {
    // wykonaj akcje
}
```

Stwórzmy mechanizm dla `ItemTag` o nazwie `wrap_brackets`, który przyjmuje liczbę całkowitą. Otoczy on nazwę wyświetlaną przedmiotu nawiasami z określoną liczbą spacji pomiędzy. Skorzystamy z metody `requireInteger()` dla wejścia.

Klasa `ItemTag` posiada zmienną instancyjną `Item` o nazwie `item`. Ponieważ `adjust` nie jest metodą statyczną, możemy używać zmiennych instancyjnych bezpośrednio w kodzie mechanizmu. Nazwa wyświetlana, opis (lore), zaklęcia itp. należą do „meta przedmiotu” (item meta), której istnienie możemy sprawdzić przez `hasItemMeta()` i pobrać przez `getItemMeta()`. Jeśli meta przedmiotu nie istnieje, wyrzucimy błąd.

Ogólnie powinieneś starać się sprawdzać możliwe błędne dane i wyświetlać czytelny błąd dla użytkownika. Nie jest to ściśle wymagane, ale zalecane, aby Denizen pozostał przyjazny dla nowych użytkowników, ponieważ niesprawdzone błędy mogą być trudne do namierzenia dla niedoświadczonych osób.

Istnieje metoda `echoError("...")` zarówno dla `attribute` w tagach, jak i dla `mechanism` w mechanizmach. Nie zatrzymuje ona jednak wykonywania kodu, więc w obu przypadkach musisz użyć `return`. Ponieważ w tagu musisz coś zwrócić, zwrócisz `null`, co jest rozumiane przez system jako informacja, że tag był nieprawidłowy.

```java
if (mechanism.matches("wrap_brackets") && mechanism.requireInteger()) {
    if (getItemMeta() == null || !getItemMeta().hasDisplayName()) {
        mechanism.echoError("Ten przedmiot nie ma nazwy wyswietlanej!");
        return;
    }
}
```

Teraz, gdy błędy nam nie grożą, możemy użyć metody `getValue()`, która zwraca wejście jako `ElementTag`. Korzystając z wiedzy z sekcji o wejściach tagów, użyjemy `asInt()` dla liczby spacji. Następnie możemy użyć prostej logiki `StringBuilder` do powtórzenia spacji.

```java
if (mechanism.matches("wrap_brackets") && mechanism.requireInteger()) {
    // ...
    int amount = mechanism.getValue().asInt();
    StringBuilder spaces = new StringBuilder(amount);
    for (int i = 0; i < amount; i++) {
        spaces.append(" ");
    }
}
```

Czas porozmawiać o **NMS** <span class="parens">(skrót od `net.minecraft.server`, rdzennego pakietu serwera Minecraft)</span>. Kod „NMS” w Denizen jest zazwyczaj używany wtedy, gdy Spigot API nie wspiera danej funkcji lub gdy kod musi być napisany inaczej zależnie od wersji Minecrafta. W przypadku nazw wyświetlanych przedmiotów, Spigot API posiada metodę, która niewłaściwie obsługuje zaawansowane funkcje tekstu <span class="parens">(np. alternatywne czcionki)</span> i dlatego w Denizen używa się specjalnej implementacji wspomaganej przez NMS.

Denizen dostarcza narzędzia NMS poprzez klasę `NMSHandler` i jej podklasy. Klasa, do której chcemy uzyskać dostęp, to `ItemHelper`, dostępna przez `NMSHandler.getItemHelper()`. Możemy wtedy użyć `getDisplayName(ItemTag)` oraz `setDisplayName(ItemTag, String)`, aby osiągnąć cel.

Wracając do mechanizmu przedmiotu – to proste wywołanie tych dwóch metod i konkatenacja ciągów. Oto nasza finalna implementacja:

```java
if (mechanism.matches("wrap_brackets") && mechanism.requireInteger()) {
    if (getItemMeta() == null || !getItemMeta().hasDisplayName()) {
        mechanism.echoError("Ten przedmiot nie ma nazwy wyswietlanej!");
        return;
    }
    int amount = mechanism.getValue().asInt();
    StringBuilder spaces = new StringBuilder(amount);
    for (int i = 0; i < amount; i++) {
        spaces.append(" ");
    }
    String newName = "[" + spaces + NMSHandler.getItemHelper().getDisplayName(item) + spaces + "]";
    NMSHandler.getItemHelper().setDisplayName(this, newName);
}
```

Pamiętaj, że będziesz musiał użyć polecenia [`inventory`](https://meta.denizenscript.com/Docs/Commands/inventory) do dostosowania przedmiotu w swoim ekwipunku. Przedmiot musi też mieć wcześniej ustawioną właściwość `display` <span class="parens">(po to jest sprawdzenie błędu)</span>.

Mechanizmy również mają wpisy meta! Spróbuj wypełnić jeden samodzielnie na podstawie tego szablonu:

```text
// <--[mechanism]
// @object ObjectTag
// @name nazwa_mechanizmu
// @input ObjectTag
// @description
// To jest opis mechanizmu.
// @tags
// <ObjectTag.nazwa_tagu>
// -->
```

### Właściwości (Properties)?

Wiele obiektów w Denizen można opisać za pomocą pewnego typu podstawowego połączonego z zestawem konkretnych szczegółów. Każdy taki szczegół, niezbędny do dokładnego określenia tożsamości obiektu, nazywany jest **Właściwością** (Property).

Na przykład `MaterialTag` służący do identyfikacji typów bloków posiada typ podstawowy (Material enum) oraz różne konkretne punkty danych o bloku. Na przykład [`MaterialTag.half`](https://meta.denizenscript.com/Docs/Tags/materialtag.half) to wartość „połowy” materiału takiego jak łóżko – górna (head) lub dolna (foot). Górna połowa łóżka to inny precyzyjny typ bloku niż dolna. W Denizen widać to jako `red_bed[half=head]`. Kiedy ten materiał jest odczytywany przez Denizen, tworzy on instancję `MaterialTag` o typie `red_bed`, a następnie dostosowuje mechanizm `half` z wartością `head`, aby uzyskać ostateczny poprawny obiekt. Gdy na instancji wywoływana jest metoda `identify()`, odczytuje ona wszystkie właściwości i dołącza je do wyniku.

Każda właściwość w Denizen jest zdefiniowana we własnej klasie implementującej interfejs `Property`. Poprawna właściwość musi koniecznie posiadać nazwę, mechanizm oraz funkcję pobierającą wartość (getter) odpowiadającą mechanizmowi. Właściwości powinny prawie zawsze posiadać również jeden lub więcej tagów do bezpośredniego odczytu danych.

Tagi są rejestrowane w statycznej metodzie `registerTags`, a mechanizmy są nakładane w metodzie `adjust`. Możesz zauważyć, że działa to dokładnie tak samo, jak w klasach typów tagów!

Oto metody, które właściwość musi zaimplementować:

- `getPropertyString` – aktualna wartość właściwości jako String, sformatowana tak, by mogła zostać wprowadzona z powrotem do mechanizmu. Dla prostych właściwości typu boolean może to być `"true"` lub `"false"`. Jednak przy bardziej złożonych wynikach tagów możesz użyć metody `identify()` odpowiedniego typu obiektu. Może to zwrócić `null` w przypadkach, gdy wartość właściwości jest domyślna lub nieustawiona. Może też zwrócić `null` dla właściwości rozszerzających (extension properties) <span class="parens">(wyjaśnienie poniżej)</span>.
- `getPropertyId` – nazwa właściwości. Musi być taka sama jak nazwa mechanizmu. Na przykład `half` w `MaterialTag.half`.
- `describes` – statyczna metoda wywoływana przez mechanizm refleksji. Przyjmuje `ObjectTag` i określa, czy może on być użyty dla tej właściwości. Musi ona zarówno sprawdzać typ tagu, jak i weryfikować, czy konkretny podtyp obiektu może być opisany przez tę właściwość. Na przykład `leaf_size` dotyczy tylko bloków `bamboo`, więc `MaterialLeafSize` sprawdza, czy podany materiał to bambus.
- `getFrom` – statyczna metoda wywoływana przez mechanizm refleksji. Musi zwrócić instancję właściwości, jeśli jest to możliwe, lub `null` w przeciwnym razie. Ciało tej metody w praktyce jest niemal zawsze kopiowane z referencyjnych implementacji.

Obecnie przy tworzeniu mechanizmów musisz dodać nazwę mechanizmu do tablicy `handledMechs`. Kiedyś istniał odpowiednik dla tagów, ale został zastąpiony przez system rejestracji. Jeśli Twój mechanizm nie jest rozpoznawany, prawdopodobnie zapomniałeś dodać go do tej tablicy.

Aby właściwość została rozpoznana, musi zostać zarejestrowana. Pakiet `properties` zawiera wszystkie pakiety dla różnych typów właściwości oraz klasę `PropertyRegistry`. Aby zarejestrować właściwość, użyj `PropertyParser.registerProperty()`, podając klasę właściwości i typ tagu, którego dotyczy. Pamiętaj, aby umieścić to wywołanie w kolejności alfabetycznej wraz z innymi właściwościami tego samego typu.

Przejrzyj istniejące klasy właściwości jako przykłady!

#### Właściwości rozszerzające (Extension Properties)

Dodatkowym zastosowaniem systemu właściwości jest rozszerzanie istniejących już typów obiektów. Przykładowo implementacja Denizen dla Spigota posiada kilka rozszerzeń typów rdzennych, takich jak `BukkitElementProperties`. Również Depenizen intensywnie wykorzystuje właściwości rozszerzające, aby dodawać funkcje związane z zewnętrznymi wtyczkami do typów obiektów Spigota, takich jak `PlayerTag`.

Z technicznego punktu widzenia właściwość rozszerzająca:
- implementuje `Property` jak każda inna właściwość
- zazwyczaj nie posiada żadnych sprawdzeń w metodzie `describes` poza typem obiektu
- zawsze zwraca null z `getPropertyString`
- posiada generyczną nazwę (zazwyczaj nazwę klasy) jako wynik `getPropertyId`
- niekoniecznie posiada mechanizm(y)
- istnieje w oddzielnym projekcie niż ten, który definiuje dany obiekt
