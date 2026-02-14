Pamięć specjalna: Obiekty zanotowane (Notable Objects)
------------------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Niektóre obiekty są godne uwagi

Niektóre z obiektów na Twoim serwerze są bardzo ważne! Lokalizacja spawnu jest kluczowa! Region miasta to centrum aktywności graczy!

Prawdopodobnie musisz odwoływać się do tych obiektów bardzo, bardzo często. Co więcej, musisz się do nich odwoływać w kluczowych miejscach, takich jak linie zdarzeń (events) – możesz chcieć na przykład, aby zdarzenie uruchamiało się, gdy gracz wchodzi lub wychodzi z regionu miasta, wyświetlając mu wiadomość powitalną lub pożegnalną. Istnieje mnóstwo takich przypadków użycia.

Najlepszym narzędziem do obsługi tych sytuacji jest system **Obiektów zanotowanych** (Notable Objects) w Denizen.

#### Niektóre typy obiektów mogą być „zanotowane”

Tylko kilka kluczowych typów obiektów w Denizen jest częścią systemu obiektów zanotowanych. Obejmuje to lokalizacje (locations), obszary (areas) oraz ekwipunki (inventories).

Kiedy typ obiektu jest „zanotowany” (notable), oznacza to, że możesz stworzyć „notatkę” (note) dla dowolnego obiektu tego typu. „Notatka” to zasadniczo unikalna w skali globalnej nazwa referencyjna dla tego obiektu. Myśl o tym jak o wariancie flagi serwera, gdzie nazwa notatki to nazwa flagi, a sam obiekt to wartość flagi.

#### Dlaczego więc nie użyć po prostu flag serwera?

Zanotowane obiekty mają kilka kluczowych przewag nad zwykłymi flagami serwera:

- Kiedy obiekt jest zanotowany, zanotowany zostaje *ten konkretny obiekt*, a nie jego kopia. Oznacza to, że jeśli dostosujesz ten obiekt, faktycznie zmieniasz obiekt w notatce i nie musisz ponownie używać polecenia `note` <span class="parens">(w przeciwieństwie do flagi, gdzie po wprowadzeniu zmian musiałbyś ponownie użyć polecenia flag, aby zapisać wynik z powrotem)</span>.
- Obiekty znają swoją własną nazwę notatki. Na przykład zwykły `LocationTag` identyfikuje się jako `l@0,64,0,world`, ale jeśli zanotujesz tę lokalizację pod nazwą `spawn`, od tej pory będzie identyfikować się jako `l@spawn`. Możesz również sprawdzić nazwę notatki dowolnej lokalizacji za pomocą tagu `<[jakas_lokalizacja].note_name>`.
- Do notatek można odwoływać się za pomocą samej nazwy. Jeśli masz `LocationTag` zanotowany jako `spawn`, możesz używać poleceń typu `- teleport <player> spawn`, aby wysłać gracza na spawn – sama nazwa `spawn` wystarczy, aby Denizen wiedział, o co Ci chodzi <span class="parens">(w przypadku flagi musiałbyś napisać `- teleport <player> <server.flag[spawn]>`, używając całego tagu dla wyjaśnienia)</span>.
- Do notatek można odwoływać się w liniach zdarzeń. Jeśli masz `CuboidTag` zanotowany jako `main_town`, możesz pisać zdarzenia typu `on player breaks block in:main_town:`, co automatycznie ogranicza zdarzenie `breaks block` tylko do sytuacji, gdy blok znajduje się wewnątrz miasta zdefiniowanego przez Twój zanotowany obszar. Jest to najważniejszy przypadek użycia notatek, który omówimy szerzej w dalszej części tej strony.
- Notatki posiadają niezawodny, trwały i unikalny stan. To tylko skomplikowany sposób na powiedzenie, że jeśli zanotujesz `InventoryTag`, ekwipunek ten może być otwarty przez wielu graczy, którzy mogą w nim przekładać przedmioty, a każdy gracz będzie widział zmiany innych. Po restarcie serwera wszystko w tym ekwipunku pozostanie dokładnie tak, jak zostawili to gracze – całkowicie automatycznie.

### Więc jak zrobić notatkę?

Cóż, jak w przypadku większości pytań wykraczających poza poziom początkujący w Denizen, odpowiedzią jest wzięcie słowa, o które pytasz, i wpisanie go w [wyszukiwarkę dokumentacji](https://meta.denizenscript.com/). W tym przypadku bez trudu znajdziesz polecenie `note`, które robi dokładnie to, co sugeruje nazwa.

Podstawowa składnia polecenia `note` to: `- note [<obiekt>/remove] [as:<nazwa>]`

Wejście `obiekt` przyjmuje oczywiście dowolny obiekt będący typem zanotowalnym. Wejście `as:<nazwa>` to nazwa, jaką chcesz nadać notatce.

Spróbujmy przykładu.

### Notowanie lokalizacji

Notatki lokalizacji nie są najbardziej użytecznym typem notatek, ale są szybkie i łatwe do zrobienia, a mogą uczynić wiele skryptów czystszymi i prostszymi.

Stań w ważnym miejscu w swoim świecie <span class="parens">(albo po prostu postaw złoty blok na środku pustkowia i uznaj go za ważny na potrzeby testu)</span> i wpisz: `/ex note <player.location> as:wazne_miejsce`

![](images/ex_note.png)

Zazwyczaj `/ex` służy do testów, ale w tym przypadku to właściwie wszystko, czego potrzebujesz. Po wykonaniu `/ex note` ta notatka istnieje na Twoim serwerze na zawsze <span class="parens">(dopóki jej nie usuniesz)</span> i posiada już wszystkie poprawne dane.

Teraz użyjmy `/ex` ponownie, aby przetestować notatkę. Odejdź kawałek od tego miejsca i wpisz `/ex teleport <player> wazne_miejsce`. Jeśli wszystko poszło zgodnie z planem, zostaniesz przeteleportowany dokładnie w to samo miejsce.

Od teraz możesz używać tej samej komendy `/ex note` dla dowolnej ważnej lokalizacji z dowolną nazwą i stosować prostą składnię wpisywania nazwy do polecenia `teleport` <span class="parens">(lub `modifyblock`, lub dowolnego innego przyjmującego lokalizację)</span> w skrypcie lub w testach `/ex`. Pamiętaj, że możesz również używać `- note` bezpośrednio w skrypcie, jeśli chcesz tworzyć notatki dynamicznie.

#### Częste pytania dotyczące zanotowanych lokalizacji

Kiedy zostałeś przeteleportowany w to samo miejsce w powyższym przykładzie, mogłeś zauważyć, że jest to **dokładnie** to samo miejsce. Jeśli stałeś na krawędzi bloku, zostaniesz tam przeniesiony; jeśli patrzyłeś w dół, po teleportacji wciąż będziesz patrzeć w dół. Jeśli chcesz to zmienić, najprostszym rozwiązaniem jest po prostu stanąć inaczej i ponownie stworzyć notatkę. Jeśli chcesz być bardziej precyzyjny, możesz oczywiście zmienić tag użyty przy tworzeniu notatki. Na przykład, aby wyśrodkować lokalizację w bloku, użyj `/ex note <player.location.center> as:wazne_miejsce`. Jeśli chcesz wyrównać kąty patrzenia, możesz użyć np. `/ex note <player.location.with_pitch[0].with_yaw[90]> as:wazne_miejsce`.

Możesz być zainteresowany zanotowaniem lokalizacji dźwigni, przycisku, drzwi itp. do użytku w poleceniu `switch`, aby skrypt automatycznie coś aktywował. Może się jednak okazać trudne precyzyjne zanotowanie lokalizacji tylko przez stanie w niej. Co zrobić? To proste! Skieruj kursor na blok i wpisz `/ex note <player.cursor_on> as:dzwignia`. Tag `cursor_on` zwróci lokalizację bloku, na który wskazujesz, zamiast miejsca, w którym stoisz.

### Notowanie obszaru

Prawdopodobnie najważniejszym, a na pewno najczęstszym typem obiektu do zanotowania jest obiekt obszaru (area). Jest to kategoria typów obiektów obejmująca `CuboidTag`, `EllipsoidTag` oraz `PolygonTag` – różne sposoby ograniczania wycinka świata: wewnątrz prostopadłościanu, obszaru kulistego lub wielokąta o wielu wierzchołkach.

Polecenie i składnia do notowania obszarów są takie same jak w przypadku lokalizacji, jedyną różnicą jest sposób tworzenia obiektu.

#### Notowanie prostopadłościanów (Cuboids)

Prostopadłościany (cuboids) to niezwykle powszechny sposób definiowania obszarów. Jeśli kiedykolwiek korzystałeś z WorldGuard, prawdopodobnie znasz podstawową ideę cuboida. Cuboid posiada dwa narożniki i zawiera każdy blok w przestrzeni o kształcie pudełka pomiędzy tymi dwoma punktami. Zauważ, że granice cuboida są definiowane w oparciu o bloki i nie mogą mieć wartości dziesiętnych.

Dla bardzo szybkiego przykładu stworzenia cuboida: stań w jednym rogu pokoju, spójrz na przeciwległy róg i wpisz `/ex note <player.location.to_cuboid[<player.cursor_on>]> as:moj_pokoj`.

![](images/basic_cuboid_note.png)

Aby sprawdzić, czy zadziałało, przejdź się po pokoju i wpisz `/ex narrate <player.location.is_within[moj_pokoj]>` ... dopóki jesteś w pokoju, powinno wyświetlać `true` (prawda), a gdy go opuścisz – `false` (fałsz).

### Skrypt pomocniczy

Prawdopodobnie myślisz teraz: „**To taki niewygodny sposób, chcę magiczną różdżkę, żeby po prostu kliknąć i gotowe**” – i masz szczęście! To w końcu Denizen, a notowanie obszarów wiąże się z tagami i poleceniami, co jest problemem łatwym do rozwiązania przez skrypt. W rzeczywistości, jako że jest to częsta prośba, istnieje wysokiej jakości gotowy skrypt do tego celu – daje Ci on magiczną różdżkę do klikania i notowania obszarów. Możesz go pobrać [tutaj z sekcji zasobów forum Denizen](https://forum.denizenscript.com/resources/area-selector-tool.1/).

![](images/cuboid_wand.png)

Aby go użyć, po prostu pobierz plik `.dsc` i umieść go w swoim folderze ze skryptami. Możesz przejrzeć ten plik w edytorze, jeśli chcesz zobaczyć, jak działa. Kiedy będziesz gotowy, wejdź na serwer i wpisz `/ex reload`, aby załadować skrypt, a następnie `/seltool`, aby otrzymać różdżkę. Możesz wtedy kliknąć lewym przyciskiem myszy na dowolny blok, aby zacząć zaznaczanie, i prawym przyciskiem, aby je rozszerzyć. Skrypt automatycznie stworzy podświetlenie cząsteczkowe (particles), aby pokazać Ci zaznaczony obszar. Gdy masz zaznaczony region, wpisz `/selnote nazwa_mojego_obszaru`. Właśnie stworzyłeś zanotowany cuboid o tej nazwie.

#### Używanie zanotowanego obszaru

Jak możemy wykorzystać taką notatkę obszaru? Po pierwsze, oczywiście w tagach, takich jak pokazany wcześniej `is_within`. Możesz ich również używać w poleceniach – częstym przykładem dla cuboidów jest polecenie `schematic`, które przyjmuje cuboid jako wejście. Jednak najczęstszym zastosowaniem są zdarzenia (events).

Jednym z popularnych zdarzeń korzystających z notatek obszaru jest *zdarzenie wejścia/wyjścia z obszaru*. Używa się go ze składnią `[on/after] player [enters/exits] <nazwa-notatki>:`. Wypróbujmy skrypt to wykorzystujący:

```dscript_green
cuboid_note_sample:
    type: world
    events:
        after player enters moj_pokoj:
        - narrate "cześć <player.name>, witaj w moim pokoju!!!"
        after player exits moj_pokoj:
        - ratelimit <player> 10s
        - narrate "no to pa <player.name>"
```

Z tym załadowanym skryptem, za każdym razem gdy wejdziesz w obszar zanotowany jako `moj_pokoj`, otrzymasz powitanie. Gdy wyjdziesz, otrzymasz wiadomość pożegnalną, ale nie częściej niż co 10 sekund od ostatniego pożegnania, dzięki użyciu polecenia `ratelimit`.

Innym niezwykle powszechnym zastosowaniem notatek obszaru jest przełącznik `in:<area>`. Przeglądając dokumentację zdarzeń, zobaczysz, że wiele z nich posiada linię `Has Location: True - This adds the switches 'in:<area>', 'location_flagged:<flag>', ...`. Dla każdego takiego zdarzenia możesz stworzyć coś na wzór poniższego przykładu:

```dscript_green
in_area_sample:
    type: world
    events:
        after player places block in:moj_pokoj:
        - narrate "hej <player.name>! zabieraj ten blok stąd!"
```

Dzięki temu skryptowi za każdym razem, gdy postawisz blok w moim pokoju, mistyczny głos wrzaśnie na Ciebie, żebyś przestał. Jeśli postawisz blok gdziekolwiek indziej, nic się nie stanie.

Ograniczanie zdarzeń w ten sposób jest przydatne w niezliczonych scenariuszach. Możesz go użyć do implementacji chronionych regionów – niektóre obszary mogą powstrzymywać Cię przed stawianiem/niszczeniem bloków za pomocą `determine cancelled` lub blokować PvP itp. Możesz sprawić, by w pewnych miejscach rzeczy działały inaczej – np. łowienie ryb w specjalnym jeziorze daje dwa razy więcej łupów, używając zdarzenia `player fishes in:specjalne_jezioro`. Może gracze ginący w regionie areny powinni odradzać się natychmiast przy wejściu na arenę zamiast na globalnym spawnie? Jedynym ograniczeniem jest Twoja wyobraźnia!

#### Notowanie elipsoid (Ellipsoids)

Notatki elipsoid działają niemal tak samo jak cuboidy, są tylko bardziej zaokrąglone. Również w przeciwieństwie do cuboidów, wartości elipsoid mogą posiadać miejsca dziesiętne.

Przejdę od razu do rzeczy: możesz robić w zasadzie to samo, używając słowa `ellipsoid` zamiast `cuboid`. Jeśli używasz wspomnianego wcześniej gotowego skryptu, użyj `/seltool ellipsoid` lub `/etool`, aby otrzymać różdżkę, lewym przyciskiem zacznij, prawym rozszerz, a potem `/selnote`, aby to zanotować.

![](images/ellipsoid_note.png)

Możesz używać elipsoid w zdarzeniach i poleceniach dokładnie tak samo jak cuboidów.

#### Notowanie wielokątów (Polygons)

Wielokąty (polygons) są nieco bardziej skomplikowane. Są definiowane przez minimalną i maksymalną wartość Y (wysokość) oraz listę wierzchołków (corners). Wierzchołki powinny wyznaczać zewnętrzne granice wielokąta. Jeśli krawędzie wielokąta będą na siebie nachodzić, wielokąt będzie miał w sobie dziury. Lokalizacje wierzchołków mogą mieć wartości dziesiętne.

Poza skomplikowanym procesem tworzenia, są one używane tak samo jak cuboidy i elipsoidy – istnieją polecenia i tagi operujące na wielokątach, a każde wejście zdarzenia przyjmujące „obszar” (area) akceptuje wielokąt tak samo jak elipsoidę czy prostopadłościan.

Jeśli używasz gotowego skryptu selector tool, możesz wpisać `/seltool polygon` lub `/ptool`, aby otrzymać różdżkę, następnie lewym przyciskiem myszy zaznacz pierwszy wierzchołek regionu, a prawym każdy kolejny. Uważaj, które bloki wybierasz – upewnij się, że poruszasz się w jednym kierunku rotacji (zgodnie lub przeciwnie do wskazówek zegara) wokół obszaru, nie zawracaj ani nie krzyżuj linii ze sobą, nie zaznaczaj punktów poza punktem startowym. Gdy postawisz przynajmniej 3 wierzchołki, zaczną pojawiać się cząsteczki. Jeśli chcesz rozszerzyć wielokąt w górę lub w dół, po prostu podleć na żądaną wysokość i wpisz `/selheight`. Jeśli to wszystko brzmi myląco... cóż, po prostu spróbuj i poeksperymentuj chwilę – prawdopodobnie zrozumiesz, gdy zobaczysz granice cząsteczkowe swojego regionu. Jeśli się zgubisz, nie bój się dołączyć do naszego [Discorda](https://discord.gg/Q6pZGSR) i poprosić o pomoc.

![](images/polygon_note.png)

### Notowanie ekwipunku

Aby zanotować ekwipunek, potrzebujesz konstruktora ekwipunku. Może to być skrypt typu `inventory` lub konstruktor generyczny.

Oto przykład skryptu ekwipunku:

```dscript_green
my_inventory_script:
    type: inventory
    inventory: chest
    size: 27
    title: Plecak
```

Aby użyć konstruktora generycznego, wpisałbyś coś w stylu `<inventory[generic[size=27;title=MojEkw]]>`. Jest to nieco nieczytelne, więc skrypty ekwipunku są lepsze, ale generyki bywają przydatne do szybkich testów.

Aby stworzyć notatkę z ekwipunku, polecenie wygląda tak: `/ex note <inventory[my_inventory_script]> as:notatka_mojego_ekw`. Używa to tagu konstruktora `inventory`, aby wyjaśnić poleceniu `note`, jakiego typu obiekt próbujemy zanotować.

Skrypt ekwipunku reprezentuje sposób na skonstruowanie ekwipunku – za każdym razem, gdy otwierasz `my_inventory_script`, *generuje* on nowy ekwipunek. Natomiast notatka ekwipunku to jeden, konkretny, rzeczywisty ekwipunek.

Oznacza to w praktyce, że jeśli wielu graczy uruchomi `- inventory open d:my_inventory_script`, każdy z nich zobaczy swój własny, oddzielny ekwipunek. Jeśli jednak wielu graczy uruchomi `- inventory open d:notatka_mojego_ekw`, wszyscy będą mieli otwarty ten sam ekwipunek. Oznacza to, że jeśli jeden gracz przełoży przedmiot, każdy inny gracz również zobaczy, że ten przedmiot się poruszył.

Zawartość zanotowanego ekwipunku jest trwała. Oznacza to, że jeśli ręcznie przełożysz przedmioty w notatce ekwipunku, zamkniesz go i otworzysz ponownie, zawartość będzie taka sama. Nawet jeśli zrestartujesz serwer, wciąż zobaczysz zawartość taką, jaką zostawiłeś przy ostatniej zmianie. Częstym przypadkiem użycia jest skrypt plecaka – połączenie prostego skryptu ekwipunku (jak powyżej) i skryptu polecenia typu `/backpack`, który otwiera notatkę ekwipunku unikalną dla gracza – zazwyczaj generowaną z nazwą typu `- note <inventory[my_backpack_script]> as:plecak_<player.uuid>` i otwieraną przez `- inventory open d:plecak_<player.uuid>`. Dzięki temu każdy gracz otrzymuje swój własny trwały plecak – jakby miał drugą skrzynię kresu (enderchest).

Nazw notatek ekwipunku można używać również w zdarzeniach, we wszystkich tych samych miejscach, w których można używać nazw skryptów ekwipunku. Na przykład `after player opens my_inventory_script:` oraz `after player opens notatka_mojego_ekw:` oba zostaną uruchomione, gdy notatka zostanie otwarta (ponieważ notatka została wygenerowana ze skryptu, więc obie linie zdarzeń mają zastosowanie).

![](images/backpack_inv.png)

### Edytowanie notatki

Istnieją dwa sposoby edycji notatki:

Pierwszy to po prostu nadpisanie notatki nowym poleceniem note. Nie podoba Ci się umiejscowienie Twojej notatki `LocationTag` o nazwie `spawn`? Po prostu wpisz `/ex note <player.location> as:spawn` ponownie w lepszej pozycji.

Drugi sposób bywa przydatny w zaawansowanych zastosowaniach, szczególnie przy notatkach ekwipunku: polecenie `adjust`. Każdy mechanizm użyty na zanotowanym obiekcie zostanie nałożony bezpośrednio na oryginalny obiekt w notatce. Przy poleceniu `adjust` będziesz musiał sprecyzować typ obiektu wejściowego za pomocą tagu konstruktora. Na przykład, aby zmienić tytuł zanotowanego ekwipunku: `/ex adjust <inventory[notatka_mojego_ekw]> title:NowyTytul`. Możesz też swobodnie używać innych poleceń na obiekcie. Na przykład `/ex give stick to:notatka_mojego_ekw` doda patyk do tego zanotowanego ekwipunku.

### Usuwanie notatki

Jeśli notatka nie jest już potrzebna, jej usunięcie jest proste: `/ex note remove as:<nazwa>`. Zamiast podawać obiekt, wpisz dosłownie słowo `remove`. To wszystko – notatka o podanej nazwie przestaje być zanotowana.

Pamiętaj, że nie niszczy to automatycznie samego obiektu bazowego. Na przykład, jeśli usuniesz notatkę ekwipunku, ale jakiś gracz ma ten ekwipunek otwarty, pozostanie on otwarty i poprawny dopóki gracz go nie zamknie – po prostu nazwa notatki już do niego nie prowadzi.

### Częste pytania

- **Czy stanie się coś złego, jeśli użyję polecenia `note` z nazwą, która już istnieje?** Spowoduje to usunięcie istniejącej notatki i zapisanie nowej. Nic się nie popsuje, chyba że potrzebowałeś tej pierwotnej notatki.
- **Jak powinienem nazywać swoje notatki?** Ogólnie – jak tylko chcesz. Kluczową rzeczą do zapamiętania jest to, że każdy skrypt na Twoim serwerze widzi tę samą listę notatek, więc warto nazywać je tak, aby uniknąć ryzyka użycia tej samej nazwy przez dwa różne skrypty. Zatem przykładowe nazwy jak `wazne_miejsce` czy `dzwignia` prawdopodobnie nie są najlepszymi nazwami do rzeczywistego użytku. Możesz wybrać nazwy typu `zadanie_ratujkrowy_dzwignia` <span class="parens">(ten przykład zakłada, że Twój serwer ma oskryptowane zadanie o nazwie „ratuj krowy”, a dźwignia jest ściśle z nim powiązana)</span>. Jednak często w przypadku notatek proste nazwy się sprawdzają – na przykład zanotowana lokalizacja o nazwie `spawn` jest całkiem sensowna – ile punktów spawnu może mieć jeden serwer? Oczywiście zawsze przestrzegaj ogólnych zasad czystego nazewnictwa – używaj prostych słów, bez spacji, bez dziwnej wielkości liter, bez symboli innych niż podkreślnik `_`. Nie jest to *wymagane*, ale zalecane dla zachowania porządku.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się więcej o obiektach zanotowanych, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

- [Dokumentacja polecenia Note](https://meta.denizenscript.com/Docs/Commands/note)
- [Dokumentacja kontenerów skryptów ekwipunku](https://meta.denizenscript.com/Docs/Languages/inventory%20script%20containers)
- [Skrypt różdżki do zaznaczania prostopadłościanów](https://forum.denizenscript.com/resources/cuboid-selector-tool.1/)
- [Skrypt różdżki do zaznaczania elipsoid](https://forum.denizenscript.com/resources/ellipsoid-selector-tool.3/)
- [Skrypt różdżki do zaznaczania wielokątów](https://forum.denizenscript.com/resources/polygon-selector-tool.2/)
