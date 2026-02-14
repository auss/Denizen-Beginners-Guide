Zadanie typu Kill Quest (CZĘŚCIOWY OPIS)
----------------------------------------

**TODO: Informacja o wymaganych sekcjach do przeczytania przed rozpoczęciem.**

**TODO: Opis prowadzący graczy od początku do końca przez proces pisania zadania opartego na NPC polegającego na zabiciu potworów.**

### Wersja historyczna

Nadal możesz znaleźć [stary film instruktażowy tutaj](https://one.denizenscript.com/denizen/vids/Putting%20It%20Together:%20A%20Kill%20Quest), jednak ostrzegamy, że niektóre z przedstawionych tam technik są nieaktualne, zwłaszcza tagi flag. Przeczytaj listę [Aktualizacji od czasu filmów instruktażowych](/guides/troubleshooting/updates-since-videos), jeśli zdecydujesz się obejrzeć ten film.

### Przykładowy skrypt

Ten przewodnik nie został jeszcze napisany, jednak poniżej znajduje się skrypt z komentarzami wyjaśniającymi wewnątrz pliku, który demonstruje jedną z możliwych implementacji zadania opartego na NPC.

Ten przykład zakłada, że posiadasz **nowoczesny plik konfiguracyjny Denizen**. Oznacza to, że Twój plik `plugins/Denizen/config.yml` został wygenerowany po czerwcu 2021 roku (Release 1743 lub nowszy). Jeśli Twoja konfiguracja jest starsza, a chcesz użyć tego skryptu, możesz:
- A: Usunąć plik `config.yml` i zrestartować serwer, aby Denizen wygenerował go ponownie.
- B: Wejść do pliku `config.yml` w edytorze tekstu, znaleźć linię `Queue speed: 0.5s` pod `Interact:` i zmienić `0.5s` na `instant`, a następnie wpisać `/denizen reload config`.

Ten skrypt jest zaprojektowany tak, abyś mógł go skopiować, załadować na serwer i przypisać do NPC za pomocą `/npc assign --set npc_killquest` lub `/ex assignment set npc_killquest`. Możesz go następnie dostosować lub użyć jako punktu odniesienia do nauki.

Skrypt może być łatwiejszy do czytania w [edytorze skryptów](/guides/first-steps/script-editor) niż na tej stronie.

```dscript_green
# Główny skrypt przypisania dla NPC
npc_killquest:
    type: assignment
    actions:
        on assignment:
        # Włącz oba wyzwalacze, które będą używane
        - trigger name:click state:true
        - trigger name:chat state:true
    interact scripts:
    # Powiąż poniższy skrypt interakcji
    - npc_killquest_interact

# Alternatywny format czatu dla NPC, jeśli nie lubisz domyślnego 'chat'
# jeśli tworzysz wielu NPC na bazie tego skryptu, pamiętaj, że skrypty formatowania są potrzebne tylko *raz* (chyba że chcesz inny format dla każdego NPC)
cchat:
    type: format
    format: <&b><npc.name> <&f>do ciebie<&co> <&2><[text]>

# Czytelny, prosty format instrukcji przekazywanych graczowi przez skrypt, a nie przez NPC
# (Wyjaśnienie Out-Of-Character tego, co NPC powiedział In-Character).
instruction_format:
    type: format
    # Jasnoszary + Kursywa
    # Można też zapisać jako <gray><italic>
    format: <&7><&o>[<[text]><&7><&o>]

npc_killquest_interact:
    type: interact
    steps:
        # Krok domyślny: oczekiwanie na interakcję gracza
        waiting*:
            # Początkowa interakcja gracza z tym NPC to zawsze kliknięcie prawym przyciskiem myszy
            click trigger:
                script:
                # Przykład blokowania interakcji (engage) dla konkretnego gracza za pomocą flagi
                # 'Engaging' to sposób na powstrzymanie graczy przed spamowaniem interakcji z NPC i psuciem skryptów.
                # Zmusza to gracza do poczekania na kolejną interakcję, dopóki obecna się nie zakończy.
                # Działa poprzez użycie sprawdzenia has_flag na początku każdej interakcji, aby przerwać skrypt wcześniej, oraz dodawanie/usuwanie flagi przed/po wolnych interakcjach (jak wiadomości z wait).
                - if <player.has_flag[npc_engaged]>:
                    - stop
                # Sprawdź czas odnowienia (cooldown) przed zajęciem się blokadą interakcji, aby nie musieć jej zdejmować wewnątrz if
                - if <player.has_flag[kill_zombie_quest_cooldown]>:
                    # Choć cooldown *mógłby* być krokiem lub poleceniem `cooldown`, użycie flagi pozwala śledzić i wyświetlać licznik czasu, co gracze docenią.
                    - narrate format:instruction_format "Możesz powtórzyć to zadanie za <player.flag_expiration[kill_zombie_quest_cooldown].from_now.formatted>."
                    - stop
                # Dodaj flagę blokady na jedną minutę - zostanie wyczyszczona poleceniem flag lub wygaśnie sama po minucie, jeśli coś pójdzie nie tak
                - flag player npc_engaged expire:1m
                # Alternatywnie, zamiast flag gracza, możesz użyć poleceń engage dla konkretnego NPC,
                # co jest prostsze, ale często przeszkadza innym graczom
                #- engage
                - narrate format:cchat "Witaj. Czy reflektujesz na specjalną nagrodę?"
                - wait 5t
                - narrate format:cchat "Jeśli tak, możesz zabić dla mnie 5 zombie."
                - wait 5t
                - narrate format:cchat "Czy przyjmiesz to zlecenie?"
                - wait 5t
                # Gracz może wpisać 'tak' lub 'nie' na czacie, LUB kliknąć słowo, aby automatycznie je wypowiedzieć
                - narrate format:instruction_format "Wpisz <&b><&o><element[Tak].click_chat[tak]><&7><&o> lub <&b><&o><element[Nie].click_chat[nie]>"
                # Przejdź (zap) do kroku zawierającego wyzwalacz czatu. Dodaj limit 5 minut, aby NPC nie czekał wiecznie na odpowiedź, jeśli gracz ucieknie.
                - zap accept_question 5m
                - flag player npc_engaged:!
                #- disengage
        # Drugi krok: aktywny tylko podczas oczekiwania na odpowiedź na pytanie z wyzwalacza kliknięcia
        accept_question:
            chat trigger:
                1:
                    # Główny wyzwalacz czatu: jeśli gracz powie tak, zaczyna zadanie
                    trigger: "/Tak/ akceptuję zadanie"
                    script:
                    - if <player.has_flag[npc_engaged]>:
                        - stop
                    - flag player npc_engaged expire:1m
                    #- engage
                    - narrate format:cchat "Świetnie!"
                    - wait 5t
                    - narrate format:instruction_format "Zabij 5 zombie!"
                    # Uruchom licznik we fladze ustawiony na zero.
                    - flag player kill_zombie_quest_count:0
                    # Przejdź do kroku kończącego zadanie i zablokuj go tam (bez limitu czasu).
                    - zap finish_quest
                    - flag player npc_engaged:!
                    #- disengage
                2:
                    # Nawet jeśli nie jest to wymagane, dodaj odpowiedź 'nie', która po prostu cofa do początku
                    trigger: "/Nie/ nie chcę"
                    script:
                    - if <player.has_flag[npc_engaged]>:
                        - stop
                    # Często powtarzające się wiadomości mogą irytować. Dodanie losowości czyni skrypty przyjemniejszymi.
                    - random:
                        - narrate format:cchat "Dobra, spadaj!"
                        - narrate format:cchat "No to nie."
                        - narrate format:cchat "To radź sobie sam!"
                    # Odmówili, więc wróć do domyślnego kroku i czekaj.
                    - zap *
        # Trzeci krok: jedyna dozwolona interakcja to kliknięcie, gracz utknął tu do czasu ukończenia zadania
        finish_quest:
            click trigger:
                script:
                # Ten krok nie posiada opóźnień, więc blokada interaction (engage) nie jest potrzebna.
                # Jeśli zadanie jest oznaczone jako ukończone, daj nagrody.
                - if <player.has_flag[kill_zombie_quest_complete]>:
                    - narrate format:cchat "Dobra robota! Oto Twoja nagroda!"
                    - give diamond
                    # Następnie usuń flagę ukończenia i ustaw czas odnowienia
                    - flag player kill_zombie_quest_complete:!
                    - flag player kill_zombie_quest_cooldown expire:24h
                    # I wróć do domyślnego kroku, aby mogli powtórzyć zadanie po upływie czasu.
                    - zap *
                - else:
                    # W przeciwnym razie wyświetlaj losowe wiadomości o oczekiwaniu.
                    - random:
                        - narrate format:cchat "Zabiłeś już te zombie?"
                        - narrate format:cchat "Wciąż czekam na te zombie."
                        - narrate format:cchat "Zabijesz te zombie czy co?"

killquest_zombie_world:
    type: world
    events:
        # Nasłuchuj zdarzenia zabicia zombie przez gracza
        # Nasłuchuj tylko, gdy flaga licznika jest obecna
        after player kills zombie flagged:kill_zombie_quest_count:
        # Po zabiciu zwiększ licznik gracza
        - flag player kill_zombie_quest_count:++
        # Gdy licznik osiągnie 5, zadanie skończone!
        - if <player.flag[kill_zombie_quest_count]> == 5:
            - narrate format:instruction_format "Zadanie z zombie ukończone: wróć do NPC"
            # Usuń licznik i dodaj flagę informującą o ukończeniu
            - flag player kill_zombie_quest_count:!
            # Alternatywą dla flagi mogłoby być użycie '- zap npc_killquest_interact quest_completed' i nowego kroku.
            # Flaga + if/else została wybrana dla pokazania różnych metodologii.
            - flag player kill_zombie_quest_complete
```
```
