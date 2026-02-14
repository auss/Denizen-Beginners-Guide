Paczki zasobów – własne przedmioty i dźwięki
------------------------------------------

Ta strona odpowie na kilka typowych pytań od osób zainteresowanych tworzeniem wizualnie niestandardowych przedmiotów i dodawaniem nowych dźwięków do paczki zasobów (Resource Pack). Istnieje mnóstwo poradników dotyczących tworzenia własnych paczek zasobów, ale rzadziej spotyka się takie, które wyjaśniają, jak zaimplementować dane modeli (Custom Model Data) i zarządzać dźwiękami. Ten przewodnik szczegółowo opisuje, jak poprawnie sformatować paczkę zasobów, a następnie zaimplementować ją w Minecraft przy użyciu skryptów Denizen.

```eval_rst
.. contents:: Spis treści
    :local:
```

### Wewnątrz katalogu głównego

Jeśli nie masz jeszcze paczki zasobów: możesz dostać się do katalogu `resourcepacks`, otwierając menu paczek zasobów w kliencie Minecraft i klikając przycisk „Otwórz folder paczek”. Tam możesz utworzyć nowy folder dla swojej paczki.

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/`

Główny katalog wewnątrz folderu paczki zasobów powinien zawierać:
- Folder `assets` – to tutaj umieszczane są wszystkie pliki.
- `pack.mcmeta` – dzięki temu Minecraft wie, w jakim formacie jest Twoja paczka.
Opcjonalnie możesz dołączyć `pack.png` – to ikona Twojej paczki o wymiarach `64x64`!

#### Przykładowy plik: `pack.mcmeta`

Plik `pack.mcmeta` jest sformatowany w formacie `json`. Potrzebujesz dwóch nazwanych ciągów znaków: `pack_format` oraz `description`. Oto jak to wygląda w środku:

```json
{
   "pack": {
      "pack_format": 9,
      "description": "Moja Fajna Paczka Zasobów"
   }
}
```

##### Klucz w pliku pack.mcmeta: `pack_format`

Jest to wskaźnik dla Minecrafta, dla której wersji gry została zbudowana ta paczka. Liczba ta może wydawać się nieco arbitralna, ponieważ jest aktualizowana za każdym razem, gdy Minecraft zmienia wewnętrzny format paczek, czasem nawet kilka razy między aktualizacjami samej gry.
- `6` wskazuje na wersję `1.16.5`,
- `7` wskazuje na wersję `1.17`,
- `8` wskazuje na wersję `1.18`,
- `9` wskazuje na wersję `1.19`,
- `13` wskazuje na wersję `1.19.4`
- `22` wskazuje na `1.20.4`
- `34` wskazuje na `1.21`
- Więcej opcji wersji znajdziesz na [tej stronie Minecraft Wiki](https://minecraft.fandom.com/wiki/Tutorials/Creating_a_resource_pack#Formatting_pack.mcmeta)

##### Klucz w pliku pack.mcmeta: `description`

Może pozostać pusty lub możesz wpisać tam coś ciekawego. Znaki Unicode muszą być zapisane z ucieczką (escaped), np. `\uKOD`; dwa przykłady:
 `\u2588` dla `█` oraz `\u00A7` dla `§` – znaku sekcji używanego do [kodów kolorów](https://minecraft.gamepedia.com/Formatting_codes) na czacie Minecrafta. Jeśli chcesz czerwony tekst, będzie on wyglądał mniej więcej tak: `\u00A74Ciemnoczerwony!`.
*Uwaga: Najpierw kolor, potem formatowanie; kody formatowania zostają po kodzie koloru, ale nie odwrotnie!*

Specjalne znaki znajdziesz w tablicy znaków (Character Map).
W systemie Windows: `Start` > `Akcesoria systemu Windows`.
W systemie Linux z GNOME: `Gucharmap` jest częścią środowiska GNOME.

Możesz też po prostu wyszukać znaki unicode w Google.

### Wewnątrz folderu Assets

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/assets/`

Ten katalog powinien być pusty poza jednym folderem: `minecraft`. Wrzuć go tam i niczego więcej tutaj nie dodawaj.

### Wewnątrz folderu Minecraft

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/assets/minecraft/`

W zależności od tego, co planujesz zmienić, możesz utworzyć dowolne z poniższych folderów:

- blockstates – tutaj zapisywane są stany bloków materiałów.
- font – tutaj trafiają dane czcionek.
- models – tutaj zapisywane są modele i ich dane.
- textures – tutaj trafiają pliki obrazów tekstur.
- sounds – tutaj zapisywane są dźwięki.
- optifine – tutaj trafiają dane Optifine. Ten przewodnik tego nie obejmuje.

Dla wsparcia Optifine zalecamy dołączenie do ich Discorda i przejrzenie dokumentacji w ich [repozytorium GitHub](https://github.com/sp614x/optifine/tree/master/OptiFineDoc/doc).

### Wewnątrz folderu Sounds

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/assets/minecraft/sounds/`.
Format dźwięku używany przez Minecraft to `.ogg`. Darmowe narzędzia do konwersji znajdziesz online, np. [Audio-Online-Convert.com.](https://audio.online-convert.com/convert-to-ogg). Dla porządku, jeśli dodajesz nowe dźwięki, zaleca się umieszczenie ich w folderze o nazwie `custom`. Domyślne zasoby Minecrafta organizują dźwięki według [kategorii](https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/SoundCategory.html). Indeks domyślnych dźwięków Minecrafta znajdziesz tutaj: `.minecraft/assets/indexes/1.21.json` (gdzie `1.21` to wersja gry). Wszystkie Twoje pliki dźwiękowe <span class="parens">(pliki '.ogg')</span> powinny być zapisane w tym katalogu.

#### Przykładowy plik: `sounds.json`

Ten plik indeksuje miejsca, w których Minecraft ma szukać Twoich dźwięków. Poniżej przykład konfiguracji dla dwóch własnych dźwięków: `defense_levelup0` i `defense_levelup1`.

```json
{
  "entity.player.defense.level": {
    "sounds": [
      {
        "name": "custom/defense_levelup0"
      },
      {
        "name": "custom/defense_levelup1"
      }
    ],
    "subtitle": "Podekscytowane dźwięki trąbki"
  }
}
```

Pierwszy klucz to nazwa dźwięku; w tym przykładzie `entity.player.defense.level`. Jedynym wymaganym obiektem danych wewnątrz są `sounds`. Opcjonalnie możesz określić podtytuł (`subtitle`), który wyświetli się, gdy napisy w grze są włączone. Jeśli umieścisz wiele dźwięków w tablicy `"sounds":[]`, gra będzie wybierać je losowo na podstawie ich wagi. Rozszerzenie pliku to `.ogg` – inne formaty nie są kompatybilne.

Dla każdego pliku potrzebujesz danych: `"name":"SCIEZKA/NAZWA_PLIKU"`, bez rozszerzenia pliku. Opcjonalnie możesz ręcznie dostosować właściwości dźwięku:

1) `volume` – głośność odtwarzania.
    - Domyślnie `1.0`; zakres od `0.0` do `1.0`.
    - Wartość ta akceptuje wyższe liczby przy użyciu polecenia PlaySound w Denizen, ale nie zwiększa to samej głośności, lecz dystans, z którego dźwięk jest słyszalny.
    - Na przykład głośność 5 jest słyszalna z odległości pięciu chunków.
2) `pitch` – wysokość dźwięku.
    - Domyślnie `1.0`; zakres od `0.0` do `2.0`, gdzie `1.0` to wysoki ton, a `0.0` to niski ton.
3) `weight` – szansa na wybranie tego dźwięku spośród innych w tej samej kategorii.
            - Wyższe liczby oznaczają częstsze występowanie.
4) `stream` – określa, czy dźwięk ma być strumieniowany z pliku.
    - Domyślnie `false`.
    - Zaleca się ustawienie `true`, jeśli dźwięk trwa dłużej niż dwie sekundy, aby uniknąć lagów.
    - Jest to używane przy wszystkich płytach muzycznych.
5) `preload` – określa, czy dźwięk ma być ładowany przy ładowaniu paczki, a nie w momencie odtworzenia.
    - Domyślnie `false`. Używane dla dźwięków otoczenia (ambient).
6) `attenuation_distance` – określa stopień wyciszenia wraz z dystansem.
    - Domyślnie `1.0`. Używane przez portale, beacony, przewodniki (conduits).
7) `type` – określa, czy dźwięk jest wyzwalany przez predefiniowane zdarzenie.
    - Domyślnie `sound`, inna opcja to `event`.
    - `sound` powoduje, że `name` jest interpretowane jako nazwa pliku.
    - `event` powoduje, że `name` jest interpretowane jako nazwa już zdefiniowanego zdarzenia.

#### Używanie własnych dźwięków

Odtwarzanie dźwięku odbywa się poprzez unikalną nazwę, którą mu nadałeś. W naszym przykładzie była to nazwa `entity.player.defense.level`. Możesz odtworzyć ten dźwięk poleceniem `playsound` w ten sposób: `/ex playsound <player> entity.player.defense.level custom`. W skrypcie wyglądałoby to tak:

```dscript_green
MyCustomSound:
    type: task
    script:
        - playsound <player> sound:entity.player.defense.level custom
```

### Wewnątrz folderu Blockstates

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/assets/minecraft/blockstates/`

Aby zmodyfikować każdy poszczególny stan bloku materiału, musisz określić każdy z nich osobno. Nie można dodawać nowych stanów bloków. Wskazując modele stanów bloków, ścieżka prowadzi do katalogu `models`, znajdującego się w `/assets/minecraft/models/`. Modyfikowanie tych elementów nie jest objęte tym przewodnikiem.

### Wewnątrz folderu Models

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/assets/minecraft/models/`

Aby „tworzyć” nowe przedmioty, musisz zmodyfikować te istniejące w Minecraft. Można to robić (i robiono dawniej) za pomocą wytrzymałości (durability), ale najlepiej wykorzystać `custom_model_data`, wprowadzony w wersji 1.14. Trzy typy modeli obiektów to `Block States`, `Block Models` oraz `Item Models`. Dane Block States i Block Model nie są objęte tym poradnikiem.

#### Przykładowy plik: `wooden_sword.json`

Istniejące pliki, jak np. `wooden_sword`, powinny wyglądać tak:

```json
{
    "parent": "item/handheld",
    "textures": {
        "layer0": "item/wooden_sword"
    }
}
```

Powyższy przykład to `wooden_sword.json`, znajdujący się w `/assets/minecraft/models/item/wooden_sword.json`. Klucz `parent` wskazuje na model, do którego ten plik wstrzykuje dane. Wartość dla `parent` to `SCIEZKA/NAZWA_PLIKU` z katalogu `models` (jeśli podajemy model) lub `textures` (jeśli podajemy teksturę).

W powyższym przykładzie `wooden_sword` korzysta z modelu nadrzędnego znajdującego się w: `/assets/minecraft/textures/item/handheld.json` oraz z obrazu tekstury w: `/assets/minecraft/textures/item/wooden_sword.png`.

Pamiętaj, że usunięcie klucza `parent` bez zdefiniowania wszystkich właściwości wyświetlania przedmiotu da nieoczekiwane rezultaty. Aby dodać warunek `custom_model_data`, podajemy go w kluczu `overrides`. Oto przykład:

```json
{
    "parent": "item/handheld",
    "textures": {
        "layer0": "item/wooden_sword"
    },
    "overrides": [
        { "predicate": { "custom_model_data": 1 }, "model": "item/custom/dserver_ubersword" }
    ]
}
```

*Uwaga: Pamiętaj, że obiekty i tablice są oddzielone przecinkami.* Powyższy przykład rozszerza przedmiot `wooden_sword` o dodatkowy model, gdy przedmiot w grze ma nałożony odpowiedni mechanizm. Plik ten znajduje się w: `/assets/minecraft/models/item/custom/dserver_ubersword.json`. Prawidłowe wpisy `custom_model_data` to liczby całkowite. Przykład z wieloma modelami wygląda tak:

```json
{
    "parent": "item/handheld",
    "textures": {
        "layer0": "item/wooden_sword"
    },
    "overrides": [
        { "predicate": { "custom_model_data": 1 }, "model": "item/custom/dserver_ubersword" },
        { "predicate": { "custom_model_data": 2 }, "model": "item/custom/dserver_prosword" },
        { "predicate": { "custom_model_data": 3 }, "model": "item/custom/dserver_greatsword" },
        { "predicate": { "custom_model_data": 4 }, "model": "item/custom/dserver_decentsword" }
    ]
}
```

#### Przykładowy plik: `custom_item.json`

Plik modelu Twojego własnego przedmiotu to coś, co możesz dostosować samemu. Istnieje wiele opcji oprogramowania do modelowania, najczęściej polecanym jest darmowy i bardzo funkcjonalny [BlockBench](https://blockbench.net/) lub płatny [Cubik Pro](https://cubik.studio/). Pamiętaj, że program musi potrafić wyeksportować model do formatu `.json`. Początek Twojego pliku modelu powinien wyglądać mniej więcej tak:

```json
{
	"textures": {
		"particle": "item/custom/handheld/dserver_ubersword",
		"texture": "item/custom/handheld/dserver_ubersword"
	},
```

W powyższym przykładzie klucze `particle` i `texture` wskazują na obrazy tekstur, które zapiszemy w katalogu: `/assets/minecraft/textures/item/custom/handheld/dserver_ubersword.png`.

#### Używanie własnych przedmiotów

Danie sobie przedmiotu jest proste. Jeśli potrzebujesz go jednorazowo lub do testów, użyj [polecenia `/ex`](/guides/first-steps/ex-command) tak:
`/ex give wooden_sword[custom_model_data=1]`.
Skrypt przedmiotu wygląda po prostu tak:

```dscript_green
UberSword:
    type: item
    material: wooden_sword
    mechanisms:
        custom_model_data: 1
```

`custom_model_data` jest wpisywany obok innych mechanizmów przedmiotu. Możesz dać sobie taki przedmiot jak każdy inny skrypt przedmiotu: `/ex give UberSword` lub w dowolnym skrypcie poleceniem `give` czy `inventory`.

### Wewnątrz folderu Textures

Katalog: `.minecraft/resourcepacks/MojaPaczkaZasobow/assets/minecraft/textures/`.
To tutaj zapisywane są Twoje obrazy. Muszą się one znajdować w relatywnej ścieżce określonej w pliku modelu, któremu odpowiadają.

### Tworzenie własnych czcionek

Jednym z wielu użytecznych narzędzi w paczce zasobów są własne czcionki! Pozwala to nie tylko na zmianę wyglądu tekstu, ale także na tworzenie emoji, a nawet całych własnych obrazów! Czcionka z obrazem wielkości ekwipunku, umieszczona w tytule ekwipunku, pozwala na stworzenie własnego ekranu GUI!

#### Jak stworzyć własną czcionkę?

To proste!

Krok 1: W swojej paczce zasobów upewnij się, że masz folder specyficzny dla paczki. Wiele innych funkcji oczekuje nadpisania folderu `minecraft`, ale ta pozwala na stworzenie własnego. Więc zamiast `assets/minecraft/font`, będziesz mieć `assets/przykładowa_paczka/font`.
Krok 2: Wewnątrz `assets/przykładowa_paczka/font` utwórz plik o nazwie np. `mojaczcionka.json` (lub `gui.json`).
Krok 3: Wewnątrz tego pliku dodaj:
```json
{
  "providers": [
      {
          "type": "bitmap",
          "file": "przykładowa_paczka:folder_testowy/obrazek.png",
          "ascent": 8,
          "height": 8,
          "chars": [
              "a"
          ]
      },
      {
          "type": "bitmap",
          "file": "przykładowa_paczka:folder_testowy/obrazek2.png",
          "ascent": 8,
          "height": 8,
          "chars": [
              "b"
          ]
      }
  ]
}
```
Krok 4: W `assets/przykładowa_paczka/textures/folder_testowy/` dodaj `obrazek.png` jako dowolny obraz o dowolnym rozmiarze (8x8, 32x32 itp.). Nazwy folderów i plików powinny składać się tylko z małych liter a-z i cyfr 0-9.

Jeśli potrzebujesz obrazków do testów, oto para poprawnych plików: [sampleimage.png](https://github.com/DenizenScript/Denizen-Beginners-Guide/raw/master/source/guides/non-denizen/images/denizen_pack_image.png), [sampleimage2.png](https://github.com/DenizenScript/Denizen-Beginners-Guide/raw/master/source/guides/non-denizen/images/denizen_pack_image2.png)

Krok 5: Załaduj paczkę w grze.

Krok 6: W grze spróbuj `/ex narrate <&font[przykładowa_paczka:mojaczcionka]>ab`

Zauważ, że tekst `ab` odpowiada liniom `"a"` i `"b"` w pliku JSON. Powinieneś zobaczyć swoje nowe obrazy na czacie.

#### Jak dodać więcej obrazów do czcionki?

Po prostu skopiuj i wklej blok zaczynający się od `{` a kończący na `}`, pamiętając o przecinku między blokami. Zmień `chars` na nową literę lub cyfrę i ustaw `file` na ścieżkę do nowego obrazka.

#### Jak tworzyć duże lub małe obrazy?

Zmiana rzeczywistego rozmiaru pliku `.png` nic nie daje – aby zmienić skalę obrazu w grze, edytuj wartości `height` i `ascent` w pliku JSON. `height` to rozmiar obrazu, a `ascent` to przesunięcie go w górę lub w dół względem linii tekstu. Domyślny rozmiar tekstu to `8` dla obu tych wartości. Obrazy większe niż `8` będą nachodzić na inne linie na czacie.

#### Jak stworzyć własne GUI ekwipunku?

Oto przykład paczki dla własnych GUI:

Możesz dodać ten json np. jako `assets/examplepack/font/gui.json`

```json
{
  "providers": [
      {
          "type": "bitmap",
          "file": "examplepack:gui/vanilla_inventory_reference.png",
          "ascent": 13,
          "height": 127,
          "chars": [
              "a"
          ]
      },
      {
          "type": "bitmap",
          "file": "examplepack:gui/custom_inventory.png",
          "ascent": 13,
          "height": 127,
          "chars": [
              "b"
          ]
      },
      {
          "type": "bitmap",
          "file": "examplepack:gui/space.png",
          "ascent": -32768,
          "height": -10,
          "chars": [
              "-"
          ]
      },
      {
          "type": "bitmap",
          "file": "examplepack:gui/space.png",
          "ascent": -32768,
          "height": -170,
          "chars": [
              "="
          ]
      }
  ]
}
```

W tej paczce `-` używa „space” – obrazka 1x1, który służy do przesunięcia grafiki tła GUI na odpowiednie miejsce w tytule ekwipunku, a `=` przesuwa tekst z powrotem na normalną pozycję. [Tutaj jest obrazek space, którego możesz użyć](https://github.com/DenizenScript/Denizen-Beginners-Guide/raw/master/source/guides/non-denizen/images/space.png).

Znak `a`, `vanilla_inventory_reference`, to obrazek domyślnego ekwipunku służący jako szablon. [Możesz go pobrać tutaj](https://github.com/DenizenScript/Denizen-Beginners-Guide/raw/master/source/guides/non-denizen/images/vanilla_inventory_reference.png). Znak `b`, `custom_inventory`, to edytowany obrazek pokazujący użycie. [Pobierz demo tutaj](https://github.com/DenizenScript/Denizen-Beginners-Guide/raw/master/source/guides/non-denizen/images/custom_inventory.png).

Zastosuj to w skrypcie ekwipunku w ten sposób:

```dscript_green
example_gui:
    type: inventory
    debug: false
    gui: true
    inventory: chest
    title: <&f><&font[examplepack:gui]>-b=<&font[minecraft:default]><&0>Witaj!
    slots:
    - [] [] [] [] [] [] [] [] []
    - [] [] [] [] [stick] [] [] [] []
    - [] [stone] [] [] [] [] [] [] []
    - [] [] [] [] [] [] [] [] []
    - [] [] [] [] [] [] [] [] []
    - [] [] [] [] [] [] [] [] []
```

Otwórz to poleceniem `/ex inventory open d:example_gui`, aby zobaczyć efekt.

![](images/example_inv_demo.png)

Mam nadzieję, że to wystarczy, by pobudzić Twoją wyobraźnię! Jeśli nie, dołącz do Discorda i zajrzyj na kanał `#showcase`, gdzie pokazano wiele niesamowitych rzeczy stworzonych tą metodą.

#### Podsumowanie czcionek

Jeśli postępowałeś zgodnie z poradnikiem bez zmiany nazw, struktura plików Twojej paczki powinna wyglądać tak:

![](images/examplepack_structure.png)

### Porady i uwagi podczas tworzenia

Bardzo przydatną techniką debugowania jest to, że możesz edytować paczkę i widzieć zmiany w grze na bieżąco. Paczka zasobów NIE musi być spakowana do pliku `.zip`. Możesz trzymać ją jako zwykły folder, edytować pliki i po prostu przeładować w grze. Domyślny skrót klawiszowy do przeładowania paczek to `F3 + T`.

Jeśli zobaczysz teksturę w różowo-czarną szachownicę, oznacza to brakujące dane w Minecraft:
- Jeśli przedmiot jest płaski z taką teksturą, ścieżka do pliku modelu jest błędna lub pliku brakuje.
- Jeśli przedmiot ma kształt, ale nie ma tekstury, ścieżka do obrazka w modelu jest błędna.
- Jeśli przedmiot wygląda normalnie, Twoja paczka nie rejestruje wprowadzonych zmian.

Do sprawdzania danych JSON polecamy walidator online: [tutaj](https://jsonformatter.curiousconcept.com/).

Własne tekstury, modele i dźwięki mogą być umieszczane w dowolnej liczbie podfolderów. Pamiętaj o używaniu tylko małych liter w nazwach plików i folderów.

Domyślny folder paczek zasobów znajduje się w katalogu Minecrafta:
`%appdata%/.minecraft/resourcepacks` na Windows lub `/home/[użytkownik]/.minecraft/resourcepacks` na Linuxie.

Najlepszym wzorcem do modyfikacji modeli są zasoby domyślne gry, które znajdziesz w pliku Jar danej wersji w katalogu `/.minecraft/versions/`. Możesz go wypakować i odnaleźć folder `assets` wewnątrz.

### Powiązana dokumentacja techniczna i linki

* [Meta-dokumentacja polecenia Playsound](https://meta.denizenscript.com/Docs/Commands/playsound)
* [Formatowanie i walidator JSON](https://jsonformatter.curiousconcept.com/)
* [Online-Convert.com | Konwersja audio do formatu OGG](https://audio.online-convert.com/convert-to-ogg)
