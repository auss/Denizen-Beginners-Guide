Zadanie na przedmiot (Item Quest) (CZĘŚCIOWY OPIS)
----------------------------------------------

**TODO: Informacja o wymaganych sekcjach do przeczytania przed rozpoczęciem.**

**TODO: Opis prowadzący graczy od początku do końca przez proces pisania zadania opartego na NPC polegającego na przyniesieniu przedmiotu.**

### Przykładowy skrypt

Ten przewodnik nie został jeszcze napisany, jednak poniżej znajduje się skrypt z komentarzami wyjaśniającymi wewnątrz pliku, który demonstruje jedną z możliwych implementacji zadania polegającego na przyniesieniu przedmiotu.

Ten przykład zakłada, że posiadasz **nowoczesny plik konfiguracyjny Denizen**. Oznacza to, że Twój plik `plugins/Denizen/config.yml` został wygenerowany po listopadzie 2021 roku (Release 1750 lub nowszy). Jeśli Twoja konfiguracja jest starsza, a chcesz użyć tego skryptu, możesz:
- A: Usunąć plik `config.yml` i zrestartować serwer, aby Denizen wygenerował go ponownie.
- B: Wejść do pliku `config.yml` w edytorze tekstu, znaleźć linię `Queue speed: 0.5s` pod `Interact:` i zmienić `0.5s` na `instant`, a następnie wpisać `/denizen reload config`.

Ten skrypt jest zaprojektowany tak, abyś mógł go skopiować, załadować na serwer i przypisać do NPC za pomocą poleceń wymienionych na jego początku. Możesz go następnie dostosować lub użyć jako punktu odniesienia do nauki.

Skrypt może być łatwiejszy do czytania w [edytorze skryptów](/guides/first-steps/script-editor) niż na tej stronie.

Oto [Link do Pastebina ze skryptem](https://paste.denizenscript.com/View/89670) dla poniższego przykładu.

```dscript_green
# | Aby użyć tego skryptu:
# /npc create Questy McQuestface
# /npc assignment --set item_questgiver_assign
# Następnie udaj się do miejsca docelowego
# /npc create Thiefy McThiefface
# /npc assignment --set item_quest_goal_assign
# Początkujący użytkownicy mogą po prostu skopiować/wkleić i uzupełnić szczegóły (nazwa przedmiotu, wiadomości itp.)
# Jeśli używasz skryptu wielokrotnie, pamiętaj o odpowiedniej zmianie nazw kontenerów
# Jeśli testujesz skrypt i chcesz ręcznie zresetować czas odnowienia, użyj:
# /ex zap item_questgiver_interact *
# Nie zapomnij dodać 'debug: false' do każdego kontenera po zakończeniu testów.



# Najpierw definiujemy własny przedmiot dla zadania
my_item_quest_item:
    type: item
    # Najlepiej wybrać coś, czego gracz nie chciałby ukraść
    material: golden_hoe
    # Niech wygląda na coś ważnego
    display name: <&b><bold>Bezcenne Dziedzictwo
    lore:
    - <&7>Bezcenne dziedzictwo rodzinne Questy'ego McQuestface'a.
    - <&6>[Musi powrócić do Miasta Zadań]
    # Możemy dodać bezużyteczne zaklęcie i ukryć je, aby dodać efekt świecenia przedmiotowi
    enchantments:
    - lure:1
    mechanisms:
        hides: all

# Teraz tworzymy skrypt przypisania i interakcji dla zleceniodawcy zadania - tego, który potrzebuje przedmiotu
item_questgiver_assign:
    type: assignment
    actions:
        on assignment:
        - trigger name:click state:true
        - trigger name:chat state:true
        - trigger name:proximity state:true radius:10
    interact scripts:
    - item_questgiver_interact

item_questgiver_interact:
    type: interact
    steps:
        # Domyślny krok to oferowanie zadania przechodniom
        default*:
            # Najpierw: gdy gracz podejdzie blisko, przywitaj się
            proximity trigger:
                entry:
                    script:
                    - chat "Hej! Tutaj! Potrzebuję pomocy!"
            click trigger:
                script:
                - chat "Witaj <player.name>! Czy możesz mi pomóc? Zgubiłem mój cenny przedmiot!"
                # Użyj click_chat, aby ułatwić wybór odpowiedzi, oraz on_hover, aby było jasne, że tekst można kliknąć
                - narrate "<&7>[<element[<&b>Tak, akceptuję].click_chat[tak].on_hover[Kliknij, aby rozpocząć zadanie]> lub <element[<&b>Nie, nie teraz].click_chat[nie].on_hover[Kliknij, aby odmówić]>]"
                # Przejdź do kroku akceptacji z limitem czasowym (jeśli gracz nic nie zrobi przez 5 minut, wróć do kroku domyślnego)
                - zap wait_for_accept 5m
        # Po kliknięciu NPC, ten krok pozwala graczowi zaakceptować lub odrzucić zadanie
        wait_for_accept:
            # Jeśli gracz nic nie powie i po prostu kliknie NPC ponownie...
            click trigger:
                script:
                - chat "... I co? Tak czy nie?!"
                - narrate "<&7>[<element[<&b>Tak, akceptuję].click_chat[tak].on_hover[Kliknij, aby rozpocząć zadanie]> lub <element[<&b>Nie, nie teraz].click_chat[nie].on_hover[Kliknij, aby odmówić]>]"
            chat trigger:
                1:
                    # Jeśli gracz powie tak...
                    trigger: /Tak/ akceptuję
                    script:
                    # Wyjaśnij zadanie
                    - chat "Świetnie! Thiefy McThiefface ma mój przedmiot w Miejscowości Docelowej."
                    - chat "To bezcenne dziedzictwo rodzinne i muszę je odzyskać! Dobrze cię nagrodzę!"
                    - narrate "<&7>[Zadanie <&b><bold>Bezcenne Dziedzictwo Pokoleń<&7> zaakceptowane. Udaj się do Miejscowości Docelowej i porozmawiaj z Thiefym McThiefface.]"
                    # Przestaw skrypt docelowego NPC na odpowiedni krok
                    - zap item_quest_goal_interact can_give_item
                    # Przestaw tego NPC na krok oczekiwania na powrót
                    - zap wait_for_return
                2:
                    # Ale gracz może też odmówić...
                    trigger: /Nie/ nie teraz
                    script:
                    - chat "No dobrze. Może ktoś bardziej bohaterski od ciebie wykona to zadanie."
                    - narrate "<&7>[Zadanie odrzucone]"
                    - zap *
            # Jeśli gracz odejdzie po kliknięciu NPC, zresetuj do kroku domyślnego
            proximity trigger:
                exit:
                    script:
                    - chat "Wow! Niegrzecznie! Nawet zwykłego 'nie'? :("
                    - narrate "<&7>[Zadanie odrzucone]"
                    - zap *
        # Ten krok obsługuje sytuację, gdy gracz ma zadanie, ale jeszcze nie zwrócił przedmiotu
        wait_for_return:
            click trigger:
                # Jeśli gracz ma przedmiot,
                1:
                    trigger: my_item_quest_item
                    script:
                    # Podziękuj i zabierz przedmiot
                    - chat "Ojej! Moje dziedzictwo!"
                    - take item:my_item_quest_item
                    - narrate "<&7>[<&b><bold>Bezcenne Dziedzictwo Rodzinne<&7> usunięte]"
                    - chat "Dziękuję podróżniku! Oto Twoja nagroda!"
                    # Można dodać ładny efekt, aby gracz poczuł się doceniony
                    - toast "Zadanie ukończone: <&b><bold>Bezcenne Dziedzictwo Pokoleń" icon:my_item_quest_item
                    # Daj nagrodę - w tym przypadku XP, można też dać pieniądze itp.
                    - give xp quantity:1000
                    - narrate "<&7>[Otrzymano <&b>1000 XP<&7>]"
                    # Przejdź do kroku 'on_cooldown' na określony czas. Po jego upływie gracz wróci do kroku domyślnego.
                    - narrate "<&7>[Zadanie <&b><bold>Bezcenne Dziedzictwo Pokoleń<&7> ukończone. Można powtórzyć za 3 dni.]"
                    - zap on_cooldown 3d
                    # TODO: Rozważ, co się stanie, gdy gracz zgubi przedmiot. Możesz temu zapobiec skryptem world (wyrzucanie/klikanie w ekwipunku) lub dodać zapasowy limit czasu...
                2:
                    # Jeśli gracz nie ma przedmiotu,
                    script:
                    - chat "No i co? Gdzie on jest!?"
                    # Przypomnij o zadaniu
                    - narrate "<&7>[Udaj się do Miejscowości Docelowej i porozmawiaj z Thiefym McThiefface.]"
        # Ten krok to oczekiwanie na odnowienie zadania
        on_cooldown:
            click trigger:
                script:
                # Poinformuj gracza o czasie oczekiwania
                - narrate "Dzięki za zwrócenie mojego przedmiotu :D"
                - narrate "<&7>[Możesz powtórzyć to zadanie za <&b><script.step_expiration.from_now.formatted><&7>]"

# Skrypt przypisania i interakcji dla celu podróży - NPC, który ma przedmiot do odzyskania
item_quest_goal_assign:
    type: assignment
    actions:
        on assignment:
        - trigger name:click state:true
        - trigger name:chat state:true
    interact scripts:
    - item_quest_goal_interact

item_quest_goal_interact:
    type: interact
    steps:
        # Krok domyślny: jeśli nie masz zadania, precz!
        default*:
            click trigger:
                script:
                - chat "Kim jesteś? Czego chcesz?"
        # Ten krok jest dla graczy z aktywnym zadaniem
        can_give_item:
            click trigger:
                script:
                # Daj graczowi opcje interakcji
                - chat "Questy McQuestface cię przysłał, prawda?"
                - narrate "<&7>[<element[<&b>Tak, oddawaj jego przedmiot!].click_chat[tak].on_hover[Kliknij, aby kontynuować zadanie]> lub <element[<&b>Nie, nie wiem o czym mówisz].click_chat[nie].on_hover[Nie klikaj tego]><&7>]"
                - zap give_item
        # Krok rozmowy z NPC w celu odebrania przedmiotu
        give_item:
            click trigger:
                script:
                - narrate "<&7>[<element[<&b>Tak, oddawaj jego przedmiot!].click_chat[tak].on_hover[Kliknij, aby kontynuować zadanie]> lub <element[<&b>Nie, nie wiem o czym mówisz].click_chat[nie].on_hover[Nie klikaj tego]><&7>]"
            chat trigger:
                1:
                    trigger: /Tak/ mogę go zabrać
                    script:
                    - chat "O rany, no dobra, bierz go, i tak go nie chciałem"
                    - give my_item_quest_item
                    - narrate "<&7>[Zwróć <&b><bold>Bezcenne Dziedzictwo Rodzinne<&7> do Questy'ego McQuestface'a]"
                    # Przejdź do kroku oczekiwania w razie dalszych interakcji
                    - zap please_return_it 1h
                2:
                    trigger: /Nie/ nie wiem o czym mówisz
                    script:
                    - chat "... o, no dobra, to idź sobie"
                    - zap can_give_item
        # Krok dla graczy, którzy już zabrali przedmiot
        please_return_it:
            click trigger:
                script:
                - chat "No co?! Masz już to swoje dziedzictwo! Idź sobie!"
```
