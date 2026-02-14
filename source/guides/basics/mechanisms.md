Mechanizmy i Właściwości (Mechanisms and Properties)
--------------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czym jest mechanizm?

**Mechanizmy** (mechanisms) to sposób na bezpośrednią zmianę pojedynczej wartości obiektu. Są one czymś w rodzaju mini-poleceń, zaprojektowanych do pracy jako część systemu właściwości (properties).

Oto kilka przykładów prostych mechanizmów użytych w kontenerach skryptów:

```dscript_green
ultra_sword:
    type: item
    display name: Ultra Miecz
    material: diamond_sword
    mechanisms:
        unbreakable: true
```

![](images/ultra_sword.png)

Ten skrypt, używając klucza `mechanisms` w kontenerze skryptu typu `item`, ustawia mechanizm o nazwie `unbreakable` (niezniszczalny) na wartość `true`, co sprawia, że przedmiot nigdy nie traci wytrzymałości.

```dscript_green
undead_golden_swordsman:
    type: entity
    entity_type: zombie
    mechanisms:
        item_in_hand: golden_sword
        item_in_offhand: golden_sword
        health_data: 80/80
```

![](images/undead_golden_swordsman.png)

Ten skrypt typu `entity` tworzy zombie i używa mechanizmów `item_in_hand` oraz `item_in_offhand`, aby włożyć mu złote miecze do obu rąk. Zombie odradza się również z 80 punktami życia (i taką samą wartością maksymalną) dzięki mechanizmowi `health_data`.

### Polecenie Adjust

Mechanizmy mogą być używane nie tylko w kontenerach skryptów! Jeśli potrzebujesz zmienić wartość mechanizmu w trakcie działania skryptu, służy do tego polecenie `adjust`.

Na przykład, jeśli chcemy napisać skrypt, który zmienia kolor owcy po kliknięciu jej prawym przyciskiem myszy:

```dscript_green
random_sheep_colors:
    type: world
    events:
        after player right clicks sheep with:stick:
        - adjust <context.entity> color:<context.entity.allowed_colors.random>
```

![](images/rainbow_sheep.gif)

Dzięki temu skryptowi, gdy gracz kliknie prawym przyciskiem myszy dowolną owcę trzymając patyk w dłoni, owca zmieni kolor na losowy.

Polecenie `adjust` może być używane do dostosowywania mechanizmów większości typów obiektów <span class="parens">(graczy, encji, serwera...)</span>, ale niektóre sytuacje wymagają innych sposobów nakładania mechanizmów.

### Polecenie AdjustBlock

Polecenie `adjustblock` to wariant normalnego polecenia `adjust`. Jest zbudowane specjalnie do dostosowywania mechanizmów `material` na blokach <span class="parens">([listę których znajdziesz tutaj](https://meta.denizenscript.com/Docs/Mechanisms/materialtag))</span>.

Chociaż zastosowanie `adjustblock` jest bardziej niszowe, wciąż może być bardzo przydatne. Jako szybki przykład: możesz użyć tego skryptu, aby wymusić natychmiastowe osiągnięcie maksymalnego wieku przez blok rośliny <span class="parens">(np. aby świeżo posadzona pszenica w pełni wyrosła)</span>:

```dscript_green
plant_grower:
    type: world
    events:
        after player right clicks block with:stick:
        - if <player.cursor_on.material.supports[age]>:
            - adjustblock <player.cursor_on> age:<player.cursor_on.material.maximum_age>
```

![](images/crop_growth.png)

### Jak dostosowywać przedmioty

Możesz również dostosowywać właściwości przedmiotów znajdujących się w ekwipunkach. Służy do tego polecenie `inventory` z argumentem `adjust`.

Mógłbyś założyć, że sposobem na zmodyfikowanie przedmiotu trzymanego przez gracza jest dostosowanie samego przedmiotu:

```dscript_red
flint_fancifier_bad:
    type: world
    events:
        after player right clicks block with:flint:
        - adjust <player.item_in_hand> "display:Ozdobny Krzemień"
        - adjust <player.item_in_hand> "lore:Stworzone w Denizen!"
```

Jednak tag `<player.item_in_hand>` zwróci po prostu „flint” (krzemień), bez wskazania, o *który* konkretnie krzemień chodzi. W efekcie powyższy skrypt próbuje zmodyfikować abstrakcyjne pojęcie krzemienia, a nie ten konkretny krzemień w dłoni gracza. Jeśli nie dostosowujesz konkretnego obiektu, który w jakiś sposób istnieje w świecie, nic się w tym świecie nie zmieni!

Dlatego zwykle przedmioty dostosowuje się za pomocą polecenia `inventory adjust`, aby zmodyfikować konkretny przedmiot, który chcesz zmienić.

Poniższy przykład *poprawnie* zmodyfikuje przedmiot trzymany przez gracza, gdy kliknie on prawym przyciskiem myszy:

```dscript_green
flint_fancifier:
    type: world
    events:
        after player right clicks block with:flint:
        - inventory adjust slot:hand "display:Ozdobny Krzemień"
        - inventory adjust slot:hand "lore:Stworzone w Denizen!"
```

![](images/fancy_flint.png)

Polecenie `inventory adjust` zmieni konkretnie ten przedmiot, ponieważ do tego właśnie zostało zaprojektowane! Ponieważ identyfikujesz unikalny obiekt przedmiotu tak, jak istnieje on w ekwipunku gracza <span class="parens">(jako odniesienie do konkretnego slotu w ekwipunku)</span>, polecenie `inventory adjust` jest w stanie go zmodyfikować zgodnie z życzeniem.

#### Tag With

W niektórych przypadkach tag `.with` jest wygodniejszy do dostosowywania mechanizmów na przedmiocie. W przeciwieństwie do polecenia `inventory adjust`, które musi operować na realnie istniejącym przedmiocie, tag `with` bierze dowolny koncepcyjny przedmiot i zwraca jego kopię z nałożonymi mechanizmami. Taka kopia może być następnie użyta w dowolnym poleceniu, które przyjmuje przedmioty.

Załóżmy na przykład, że zamiast zmieniać nazwę przedmiotu trzymanego przez gracza, chcesz mu dać nową kopię tego przedmiotu z inną nazwą lub po naprawie.

```dscript_green
sword_duplicator:
    type: world
    events:
        after player right clicks bedrock with:diamond_sword:
        - ratelimit <player> 1h
        - give <player.item_in_hand.with[display=Twój Darmowy Zduplikowany Miecz].with[durability=0]>
        - narrate "<&b>Miecz został zduplikowany!"
```

![](images/dup_sword.png)

Powyższy przykładowy skrypt pozwala graczom zabrać diamentowy miecz na poziom bedrocka, aby otrzymać naprawiony duplikat, nie częściej niż raz na godzinę.

Jak widać w powyższym przykładzie, podobnie jak każdy inny tag, tag `with` może zostać umieszczony na końcu dowolnego `ItemTag`. Ponieważ zwraca on `ItemTag`, możesz również łączyć wiele tagów `with` pod rząd.

Pamiętaj również, że dostępny jest tag `with` dla obiektów `MaterialTag`, aby osiągnąć podobną logikę z blokami.

### Dostosowywanie definicji

Polecenie `adjust` może być również używane do modyfikowania obiektu przechowywanego w definicji i automatycznego zapisywania wynikowego obiektu z powrotem do tej definicji.

Jest to stosunkowo niszowy przypadek użycia, ale czasem się przydaje. W szczególności pozwala to na użycie poleceń `if` do wybrania, jakie mechanizmy mają zostać nałożone na obiekt przed faktycznym wykonaniem zmian.

Przykład:

```dscript_green
sword_duplicator:
    type: world
    events:
        after player right clicks bedrock with:diamond_sword:
        - ratelimit <player> 1h
        - define sword <player.item_in_hand>
        - adjust def:sword "display:Twój Darmowy Zduplikowany Miecz"
        - if <player.location> matches lava:
            - adjust def:sword "lore:<&c>Wykuty w lawie."
            - adjust def:sword durability:0
        - give <[sword]>
        - narrate "<&b>Miecz został zduplikowany!"
```

![](images/lava_sword.png)

Ten skrypt jest podobny do duplikatora mieczy z przykładu o tagu `with`, ale teraz naprawia zduplikowany miecz tylko wtedy, gdy gracz jest na tyle odważny, by wskoczyć do lawy przed kliknięciem w bedrock. <span class="parens">(Możesz wykomentować `ratelimit`, stawiając znak `#` przed `-`, aby móc przetestować skrypt bez czekania)</span>.

Ten sposób dostosowywania przedmiotów ma również tę zaletę, że jest nieco czytelniejszy przy zmianie wielu mechanizmów naraz, dzięki bardziej liniowemu formatowi.

### Właściwości (Properties)

„Właściwości” (properties) to system śledzenia szczegółów obiektu, który nie posiada własnej unikalnej tożsamości.

Zazwyczaj każda właściwość składa się z jednego tagu i jednego mechanizmu. Tag odczytuje aktualną wartość, a mechanizm nakłada nową wartość. W wielu przypadkach będą one również powiązane z dodatkowymi pomocnymi tagami i/lub mechanizmami, aby ułatwić interakcję z właściwością przez skrypt. Pamiętaj, że choć każda właściwość ma tag i mechanizm, nie każdy tag lub mechanizm jest powiązany z właściwością.

Właściwości są najbardziej widoczne dzięki ich użyciu w wewnętrznych opisach obiektów Denizen. Opisy obiektów to pełne, wielokrotnego użytku opisy dowolnego obiektu, zazwyczaj oznaczone notacją obiektu <span class="parens">(jak `i@`)</span> na początku. Służy to głównie wewnętrznemu śledzeniu danych i najczęściej będziesz to widzieć tylko w konsoli debugowania.

#### Właściwości przedmiotu

Najczęstszym miejscem, w którym widać właściwości, są przedmioty. W Denizen przedmiot to materiał oraz seria właściwości. Na przykład, jeśli uderzysz encję świeżo wytworzonym diamentowym mieczem w trybie przetrwania, a następnie uruchomisz `/ex narrate <player.item_in_hand>` aby zobaczyć wewnętrzny opis Denizen dla tego przedmiotu, zobaczysz, że zwróci on `i@diamond_sword[durability=1]`. W tym przypadku `durability` (wytrzymałość) jest jedyną właściwością przedmiotu, a jej wartość wynosi `1` – tyle wytrzymałości przedmiot stracił do tej pory. Wiele właściwości jest oddzielonych średnikami. Na przykład: `i@diamond_sword[durability=1;display_name=Pogromca Czaszek]`. <span class="parens">(Pamiętaj: [nie wpisuj surowej notacji obiektu](/guides/troubleshooting/common-mistakes#don-t-type-raw-object-notation) do skryptu – to są przykłady wewnętrznych formatów danych, a nie coś, co można wstawić bezpośrednio do skryptu)</span>.

Przedmioty to, fundamentalnie, ich materiał i ich właściwości. Każdy przedmiot, który gracz ma w ekwipunku, może zostać przetworzony w ten sposób, a właściwości każdego przedmiotu mogą być odczytywane lub modyfikowane według uznania.

#### Właściwości materiału

Materiały, podobnie jak przedmioty, nie posiadają unikalnej tożsamości. Blok kamienia to po prostu materiał w danej lokalizacji. Blok pszenicy jest tylko nieco bardziej skomplikowany – to materiał w lokalizacji z właściwością `age` (wiek). Jak widziałeś powyżej w przykładzie z `adjustblock`, użycie mechanizmu `age` pozwala na modyfikację tej właściwości dla bloku w świecie. Kiedy odczytujesz materiały bloków z lokalizacji, zwrócą one wszelkie powiązane właściwości. Można to wykorzystać na przykład do sprawdzenia, czy ognisko wysyła sygnały dymne, do sprawdzenia różnych właściwości redstone'a, a nawet ścianek bloku grzyba.

Materiały z właściwościami, po przetworzeniu przez tag taki jak `<player.cursor_on.material>`, zwrócą wynik w stylu `m@wheat[age=7]` dla w pełni wyrośniętej pszenicy. W tym przypadku `age` pszenicy wynosi `7`.

#### Właściwości encji

Encje również mają właściwości! Właściwości encji mogą być odczytywane i modyfikowane tak samo jak właściwości materiałów, a listę wszystkich właściwości encji możesz zobaczyć za pomocą tagu `<EntityTag.describe>`. Oto przykładowy wynik `/ex narrate <player.target.describe>` po wycelowaniu w owcę zmodyfikowaną przez nasz skrypt koloru:

```
e@sheep[age=0;has_ai=true;is_aware=true;color=orange;equipment=li@i@air|i@air|i@air|i@air|;health_data=8/8;speed=0.23]
```

Jak widać powyżej, wszystkie dane o owcy są przechwytywane przez Denizen i wyświetlane w formacie EntityTag z różnymi właściwościami. Każda właściwość jest oddzielona średnikiem, a dane w nich zawarte różnią się w zależności od natury właściwości.

Właściwości te można regulować za pomocą polecenia `adjust` lub odczytywać za pomocą odpowiadających im tagów. Ponieważ właściwość nazywa się `color`, możemy założyć, że dla obiektów EntityTag istnieje zarówno mechanizm, jak i tag o nazwie `color`. Polecenie `/ex narrate "Encja <player.target.entity_type>, na którą patrzysz, ma kolor <player.target.color>"` wyświetli na przykład `Encja SHEEP, na którą patrzysz, ma kolor orange` na podstawie powyższych danych.

#### Używanie właściwości w skrypcie

Czasami pomocne może być użycie właściwości bezpośrednio w skrypcie. Gdy potrzebujesz tylko obiektu z kilkoma właściwościami, wygodniej może być wpisać je bezpośrednio w linii, zamiast budować dla nich cały kontener skryptu.

Rozważ poniższe przykłady:

```dscript_green
sand_cannon:
    type: world
    events:
        after player right clicks air with:sand:
        - shoot falling_block[fallingblock_type=sand] origin:<player> speed:1
```

Powyższy skrypt, gdy gracz kliknie w powietrze trzymając piasek, wystrzeliwuje encje `falling_block` i określa przy użyciu składni właściwości, że mają one używać materiału `sand`.

```dscript_green
pumpkin_meloner:
    type: world
    events:
        after player right clicks pumpkin_stem with:golden_hoe:
        - modifyblock <context.location> melon_stem[age=7]
```

Powyższy skrypt pozwala graczom kliknąć łodygę dyni złotą motyką, aby zmienić ją w łodygę melona, i używa składni właściwości, aby określić, że łodyga melona powinna być w pełni wyrośnięta.

Zauważ, że ten styl bezpośredniego określania właściwości nie działa, gdy potrzebujesz tagów dla wartości – w takim przypadku użyj odpowiednich tagów, takich jak `with`.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o mechanizmach i właściwościach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Meta-dokumentacja polecenia Adjust](https://meta.denizenscript.com/Docs/Commands/adjust)
- [Meta-dokumentacja polecenia AdjustBlock](https://meta.denizenscript.com/Docs/Commands/adjustblock)
- [Meta-dokumentacja polecenia Inventory](https://meta.denizenscript.com/Docs/Commands/inventory)
- [Meta-dokumentacja tagu PropertyHolderObject](https://meta.denizenscript.com/Docs/Tags/propertyholderobject)
- [Meta-dokumentacja EntityTag.describe](https://meta.denizenscript.com/Docs/Tags/entitytag.describe)
- [Lista wszystkich mechanizmów](https://meta.denizenscript.com/Docs/Mechanisms/)
