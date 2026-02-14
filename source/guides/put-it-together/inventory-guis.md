Menu GUI w ekwipunku
---------------------

Ta strona poprowadzi Cię przez proces tworzenia podstawowego menu GUI w ekwipunku za pomocą Denizen. Zakłada się, że zapoznałeś się już z sekcjami [Pierwsze kroki](/guides/first-steps/index) oraz [Podstawy](/guides/basics/index), ponieważ na tej stronie używane będą narzędzia omówione wcześniej.

W szczególności sekcja ta wykorzystuje: [polecenie /ex](/guides/first-steps/ex-command), [skrypty world](/guides/first-steps/world-script), [meta-dokumentację](/guides/basics/finding-tools) oraz [skrypty przedmiotów](/guides/basics/custom-items).

```eval_rst
.. contents:: Spis treści
    :local:
```

### Wstęp

GUI w ekwipunku to jeden z najczęstszych sposobów na implementację prostych systemów menu na serwerach Minecraft. Zazwyczaj opierają się one na interfejsie skrzyni, w której znajdują się przedmioty pełniące rolę przycisków – ich kliknięcie aktywuje określone opcje.

![](images/inv_gui_sample.png)

Takie menu można stworzyć w Denizen na kilka sposobów, z których najprostszym jest użycie dedykowanego narzędzia: [kontenera skryptu `inventory`](https://meta.denizenscript.com/Docs/Languages/inventory%20script%20containers).

### Początkowy przykład

Oto jak wygląda podstawa poprawnego skryptu ekwipunku:

```dscript_blue
my_inventory_script:
    type: inventory
    inventory: chest
    title: <&9><bold>Moje przykładowe menu GUI
    gui: true
    slots:
    - [] [] [] [] [] [] [] [] []
    - [] [] [] [] [stone] [] [] [] []
    - [] [] [] [] [] [] [] [] []
```

Jest to w dużej mierze standardowy kontener skryptu `inventory` opisany w meta-dokumentacji, ze specjalnym uwzględnieniem klucza `gui: true`.

Dla interfejsów opartych na skrzyniach (`chest`), sloty są ułożone w rzędach po 9 przedmiotów każdy. Zauważ, że każdy slot jest ujęty w nawiasy `[` i `]`, na przykład `[stone]` w środkowym slocie tego przykładu, a puste sloty to po prostu `[]`. Można to również zapisać jako `[air]`.

#### Jak to otworzyć?

Możesz otworzyć swoje nowe menu, używając [polecenia `inventory`](https://meta.denizenscript.com/Docs/Commands/inventory) z podpoleceniem `open` oraz parametrem `destination` ustawionym na nazwę skryptu ekwipunku.

W praktyce, używając polecenia `/ex`, napisałbyś coś w stylu: `/ex inventory open d:my_inventory_script`.

Gdy wypróbujesz to polecenie w grze, powinno natychmiast wyskoczyć okno skrzyni z 3 rzędami po 9 slotów, gdzie w samym środku znajduje się jeden kamień. Powinieneś móc kliknąć ten kamień, jednak obecnie nic się nie stanie. Nie będziesz też mógł go wyciągnąć ani włożyć własnych przedmiotów do tego menu. To ograniczenie zawdzięczamy właśnie kluczowi `gui: true` w skrypcie.

### Ale ja chcę przyciski, a nie kamienie!

Zazwyczaj w menu potrzebujesz przycisków, a nie tylko zwykłych przedmiotów... jednak przyciski to tak naprawdę po prostu nieco bardziej skomplikowane przedmioty. Jak powinieneś pamiętać, narzędziem do tworzenia „bardziej skomplikowanych przedmiotów” są skrypty przedmiotów (item scripts)! Zobaczmy nowy przykład z ich wykorzystaniem:

```dscript_green
my_inventory_script:
    type: inventory
    inventory: chest
    title: <&9><bold>Moje przykładowe menu GUI
    gui: true
    slots:
    - [] [] [] [] [] [] [] [] []
    - [] [] [] [] [big_button_item] [] [] [] []
    - [] [] [] [] [] [] [] [] [my_inv_gui_cancel_item]

big_button_item:
    type: item
    material: beacon
    display name: <&2>Wielki Przycisk
    lore:
    - <&7>Kliknij tutaj, aby nacisnąć przycisk.

my_inv_gui_cancel_item:
    type: item
    material: barrier
    display name: <&c>Anuluj
    lore:
    - <&7>Kliknij tutaj, aby nie naciskać przycisku.
```

Gdy użyjesz polecenia `/ex`, aby otworzyć ten skrypt ekwipunku, powinieneś zobaczyć interfejs zawierający dwa unikalne przyciski. Jednak wciąż nic nie robią.

### Więc jak sprawić, by przyciski coś robiły?

Aby przyciski faktycznie zaczęły działać, potrzebujesz innego ważnego narzędzia: skryptu typu `world`, wykorzystującego zdarzenie [player clicks &lt;item&gt; in &lt;inventory&gt;](https://meta.denizenscript.com/Docs/Events/player%20clicks%20in%20inventory).

To zdarzenie posiada dwa parametry do uzupełnienia: przedmiot (`item`) oraz ekwipunek (`inventory`). Zapewne domyślasz się, jak je wypełnić? Użyj nazwy swojego skryptu ekwipunku <span class="parens">(w naszym przykładzie `my_inventory_script`)</span> jako `<inventory>` oraz nazw swoich skryptów przedmiotów <span class="parens">(w przykładzie `big_button_item` oraz `my_inv_gui_cancel_item`)</span> jako `<item>`.

Zobaczmy poprawny skrypt typu `world` dla powyższego menu:

```dscript_green
my_inventory_gui_world:
    type: world
    events:
        after player clicks big_button_item in my_inventory_script:
        - narrate "<&[base]>Wow! Nacisnąłeś przycisk!"
        after player clicks my_inv_gui_cancel_item in my_inventory_script:
        - inventory close
```

Ten przykładowy skrypt rejestruje dwa zdarzenia – po jednym dla każdego z dwóch przycisków w ekwipunku. Kiedy załadujesz ten skrypt i otworzysz swoje menu, będziesz mógł kliknąć latarnię (beacon), aby otrzymać wiadomość, lub barierę, aby zamknąć menu za pomocą podpolecenia `close` polecenia `inventory`.

### To wszystko?

Tak, to takie proste! To w zasadzie wszystko, czego potrzebujesz, aby budować własne menu GUI w ekwipunku. Poświęć trochę czasu na eksperymentowanie z różnymi opcjami – jak wygląda menu z innymi przedmiotami lub w innym układzie? Co jeśli zmienisz liczbę rzędów lub użyjesz innego interfejsu, np. leja (`hopper`)? Co jeśli będziesz miał wiele skryptów ekwipunku, gdzie przycisk w pierwszym menu otwiera drugie?

### Idąc dalej

Aby nadać swoim menu wyjątkowego charakteru, możesz pójść o krok dalej i wykorzystać [Paczki zasobów (Resource Packs)](/guides/non-denizen/resource-packs), by stworzyć całkowicie unikalny wygląd przycisków, a nawet całego interfejsu. Jednym z częstych trików jest przypisanie obrazka całego tła interfejsu do niestandardowego znaku czcionki, który może być następnie użyty jako tytuł ekwipunku, nadpisując jego domyślny wygląd własną grafiką.

### Inne metody

We wstępie wspomniałem, że istnieje wiele sposobów na stworzenie menu GUI. Kilka alternatywnych przykładów to:
- Zastąpienie `gui: true` ogólnym zdarzeniem `on player clicks in my_inventory_script:` + `- determine cancelled` oraz podobnym dla zdarzenia `drags`. Tak robiliśmy, zanim dodano opcję `gui`.
- Zastąpienie kontenera skryptu `inventory` dynamicznie budowanym ekwipunkiem generycznym.
- Zastąpienie skryptów przedmiotów przedmiotami definiowanymi bezpośrednio w linii, np. `stone[display=<&c>Przycisk;lore=<&7>Kliknij mnie!]`.
- Zastąpienie stałych przedmiotów dynamicznie generowanymi, np. w skrypcie sklepu, który wypełnia ekwipunek aktualnym towarem.
- Używanie zanotowanych (`note`) ekwipunków do wcześniejszego przygotowania pojedynczego globalnego/dzielonego menu, które można modyfikować w czasie rzeczywistym.
- Lista mogłaby być znacznie dłuższa. Można nawet wyjść poza ramy ekwipunków, tworząc menu oparte na czacie, książkach, encjach lub...

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się więcej o narzędziach używanych do GUI w ekwipunku, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

- [Dokumentacja kontenerów skryptów ekwipunku](https://meta.denizenscript.com/Docs/Languages/inventory%20script%20containers)
- [Dokumentacja zdarzenia player clicks item in inventory](https://meta.denizenscript.com/Docs/Events/player%20clicks%20in%20inventory)
- [Dokumentacja polecenia Inventory](https://meta.denizenscript.com/Docs/Commands/inventory)
