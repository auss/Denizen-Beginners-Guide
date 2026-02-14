Co Denizen potrafi zrobić z Citizens?
----------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Połączenie dwukierunkowe

Integracja Denizen z Citizens w rzeczywistości działa na dwa sposoby:

- Po pierwsze, jak można się domyślić, Denizen posiada wiele poleceń/tagów/itp. specyficznych dla Citizens.

- Ale oprócz tego, Denizen dodaje również szereg dodatkowych narzędzi bezpośrednio do Citizens. Na przykład: `/npc mirrorskin` to polecenie udostępnione przez Denizen (które sprawia, że NPC automatycznie odzwierciedla skórkę gracza, który na niego patrzy). Denizen udostępnia kilka poleceń "podstawowego użycia", takich jak `/npc sit`, `/npc sneak` i `/npc invisible`. Denizen zapewnia również kilka podstawowych, przydatnych cech (traits), takich jak cecha `health` czy cecha `fishing`.

- Te dwa sposoby mogą czasem współpracować, jak np. [ten przydatny skrypt](https://forum.denizenscript.com/viewtopic.php?f=13&t=241), który pozwala użytkownikom na wykonanie `/npc skin --url [url tutaj]`, aby ustawić skórkę NPC z adresu URL obrazka, lub na prostszym poziomie [ten szybki skrypt pomocniczy](https://forum.denizenscript.com/viewtopic.php?f=13&t=149), który pozwala użytkownikom dodawać polecenia, które NPC wykona po kliknięciu prawym przyciskiem myszy <span class="parens">(obie te funkcje są teraz wbudowane w Citizens, ale wcześniej te skrypty były jedynym sposobem na ich realizację)</span>.

### Tworzenie zadań (Questing)

Oczywiście najpopularniejszym zastosowaniem połączenia Denizen-Citizens jest posiadanie NPC, którzy zlecają i biorą udział w zadaniach w stylu RPG. Denizen posiada dość potężny zestaw systemów dedykowanych temu konkretnemu zadaniu <span class="parens">(ale wciąż na tyle ogólnych, że można je wykorzystać w innych przypadkach)</span>.

### Rozmowy

Narzędzia udostępnione głównie z myślą o zadaniach w stylu RPG świetnie nadają się również do wszystkiego, co pasuje do modelu prowadzenia przez graczy rozmowy z NPC. Obejmuje to na przykład rozmowę będącą punktem dostępu do sklepu NPC <span class="parens">(który byłby następnie obsługiwany przez skrypt Denizen z GUI ekwipunku lub podobny)</span>.

### Scenki przerywnikowe (Cutscenes)

Jedną z ciekawych rzeczy, które są realizowane rzadziej, ale zawsze są brane pod uwagę na wielu serwerach, są scenki przerywnikowe prowadzone przez NPC. Przy kilku starannych użyciach polecenia `walk`, być może polecenia `rotate` i opcjonalnie funkcjonalności widza (spectator), dość prosto jest napisać skrypt Denizen, który wykorzystuje NPC do odegrania sceny przed graczami.

### Wyjątkowość

Z Citizens na serwerze masz posągi. Jeśli zainstalujesz Sentinel, możesz mieć strażników. Z dołączonym Denizen możesz mieć unikalny serwer, który wydaje się naprawdę żywy. Jedynym ograniczeniem jest to, ile dialogów/ścieżek scenek itp. jesteś gotów napisać!
