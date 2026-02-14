Denizen i Discord: dDiscordBot
-------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Wstęp

dDiscordBot to **dodatek (addon)** do Denizen, który udostępnia polecenia, zdarzenia skryptowe i tagi do interakcji z API Discorda. Zasadniczo możesz stworzyć w pełni funkcjonalnego bota Discord bezpośrednio wewnątrz Denizen!

### Co on potrafi?

Istnieje wiele bibliotek API Discorda dla różnych języków programowania. Być może znasz niektóre z nich, jak [Discord.Net](https://github.com/discord-net/Discord.Net) czy [JDA](https://github.com/DV8FromTheWorld/JDA) <span class="parens">(obie te biblioteki są używane w projektach własnych Denizen)</span>. Te biblioteki dążą do pokrycia **wszystkich aspektów** API Discorda.

Denizen jest przeznaczony dla serwerów Minecraft. W związku z tym dDiscordBot zapewnia narzędzia, które są przydatne dla serwera Minecraft w interakcji z Discordem. Niemniej jednak, niektóre z rzeczy, które możesz zrobić, to:

- Wysyłanie, odbieranie i odpowiadanie na wiadomości
  - Załączanie plików, embedów (ramek) i przycisków
- Zarządzanie i odpowiadanie na polecenia ukośnika (slash commands)
- Zarządzanie rolami

...i wiele więcej.

### Do czego można go użyć?

Wiele serwerów Minecraft posiada społeczności na Discordzie. Zapewniają one przestrzeń socjalną poza samą grą. Użytkownicy Discorda mogą chcieć wchodzić w interakcje z graczami online i na odwrót.

Głównymi zastosowaniami mogą być: łącznik kont (account linker), który weryfikuje użytkownika Discorda z jego kontem gracza na serwerze, oraz mostek czatu (chat bridge) między Discordem a Minecraftem. Możesz także przekazywać ogłoszenia, organizować wydarzenia międzyplatformowe lub pozwolić użytkownikom Discorda na odpytywanie serwera o dane.

### Tworzenie bota

Istnieje wystarczająco dużo poradników dotyczących tworzenia konta bota, na przykład [ten tutaj](https://discordpy.readthedocs.io/en/stable/discord.html).

Pamiętaj, aby na stronie bota włączyć opcje `Server Members Intent` oraz `Message Content Intent`.

Generując link do zaproszenia bota na serwer, użyj strony 'OAuth2' w panelu deweloperskim i zaznacz pole `bot`. Prawdopodobnie zechcesz też zaznaczyć `applications.commands`, jeśli planujesz używać poleceń ukośnika.

Alternatywnie, użyj po prostu linku: `https://discord.com/oauth2/authorize?scope=bot%20applications.commands&permissions=0&client_id=1234` i zastąp `1234` na końcu identyfikatorem aplikacji (Application ID) swojego bota.

W większości przypadków nie chcesz przypisywać uprawnień bota na stronie 'OAuth2', ponieważ może to zrobić bałagan w ustawieniach ról na Twoim serwerze.

### Instalacja na serwerze

Kiedy mówimy, że dDiscordBot jest dodatkiem, oznacza to, że nie znajduje się on w głównym pliku jar Denizen – ma swój własny plik jar, który musisz pobrać i umieścić w folderze plugins. [Możesz go pobrać tutaj.](https://ci.citizensnpcs.co/job/dDiscordBot/)

Jeśli instalujesz dDiscordBot po raz pierwszy, musisz zrestartować serwer, aby go załadować. Po restarcie Twój serwer uzyska dostęp do szeregu poleceń, tagów i zdarzeń związanych z Discordem.

### Logowanie

Gdy masz już bota utworzonego na stronie aplikacji Discorda, dodanego do swojej grupy na Discordzie oraz zainstalowany dDiscordBot na serwerze Minecraft, kolejnym krokiem jest zalogowanie się do instancji bota z poziomu serwera.

Aby zalogować serwer jako bota, użyj [polecenia `discordconnect`](https://meta.denizenscript.com/Docs/Commands/discordconnect).

Ze względów bezpieczeństwa będziesz musiał użyć tagu SecretTag dla tokena, umieszczając swój token w pliku `plugins/Denizen/secrets.secret`. Po prostu dodaj klucz typu `discord_bot_token: 123.abc` <span class="parens">(i zamień `123.abc` na token ze strony aplikacji Discorda)</span>.

Polecenie `discordconnect` przyjmuje argument `id`. Może to być cokolwiek, wybierz po prostu etykietę, która jest łatwa do zapamiętania i pasuje do celu <span class="parens">(np. `mojbot`, `pomocbot` lub `relay`)</span>. Służy ona do unikalnej identyfikacji bota w przypadku, gdy używasz wielu botów naraz na jednym serwerze. Prawie wszystkie polecenia Discord wymagają tego argumentu i musisz upewnić się, że za każdym razem używasz tej samej wybranej etykiety.

Pamiętaj, że choć możesz łączyć się i rozłączać w dowolnym momencie, zazwyczaj wystarczy połączyć się raz – najlepiej tuż po starcie serwera przy użyciu [zdarzenia `server start`](https://meta.denizenscript.com/Docs/Events/server%20start). Dodatkowo pamiętaj, aby używać [operatora czekania (~)](https://meta.denizenscript.com/Docs/Languages/waitable) przy tym poleceniu, podobnie jak przy innych poleceniach Discorda.

```dscript_green
connect_to_discord:
    type: world
    events:
        after server start:
        - ~discordconnect id:mojbot token:<secret[discord_bot_token]>
```

### Wysyłanie wiadomości

Skoro już się zalogowałeś, możesz wysłać swoją pierwszą wiadomość. [Polecenie `discordmessage`](https://meta.denizenscript.com/Docs/Commands/discordmessage) posiada argument `channel`, który wskazuje kanał Discorda, na który bot ma wysłać wiadomość. Bot musi oczywiście posiadać uprawnienia do wyświetlania i wysyłania wiadomości na tym kanale. Możesz użyć właściwego [obiektu kanału](https://meta.denizenscript.com/Docs/ObjectTypes/DiscordChannelTag) wszędzie tam, gdzie masz do niego odniesienie, lub po prostu użyć wewnętrznego identyfikatora ID kanału na Discordzie; jeśli nie wiesz, jak sprawdzić ID na Discordzie, [zobacz tutaj](https://support.discord.com/hc/en-us/articles/206346498-Where-can-I-find-my-User-Server-Message-ID-).

Zauważ, że jeśli zamierzasz często używać ID kanału lub serwera, powinieneś zapisać je w skrypcie danych lub fladze. Możesz to zrobić w grze, na przykład wpisując polecenie `/ex flag server discord_botspam:<discord[mojbot].group[Denizen].channel[bot-spam]>` i wpisując odpowiednie nazwy – oczywiście sprawdź wyjście debugowania, aby upewnić się, że tag wypełnił się poprawnie. Możesz spróbować `/ex narrate <server.flag[discord_botspam].name>`, aby upewnić się, że kanał jest poprawnie zapisany.

Oto jak wysłanie wiadomości na kanał wyglądałoby w prostym skrypcie zadania:

```dscript_green
send_a_message:
    type: task
    script:
    - ~discordmessage id:mojbot channel:<server.flag[discord_botspam]> "Witaj, świecie!"
```

#### Bardziej ozdobne wiadomości: Embedy

Jeśli kiedykolwiek wchodziłeś w interakcję z botem na Discordzie <span class="parens">(miejmy nadzieję, że tak, skoro tworzysz własnego!)</span>, wiesz, że często nie odpowiadają one czystym tekstem, lecz specjalnie sformatowanymi ramkami – nazywają się one „embedami”.

![](images/discord_embed.png)

W Denizen obsługuje się to za pomocą [obiektów DiscordEmbedTag](https://meta.denizenscript.com/Docs/ObjectTypes/DiscordEmbedTag). Wysyła się je tym samym poleceniem `discordmessage`, zastępując tekst wiadomości obiektem embed.

`DiscordEmbedTag` to zasadniczo opakowanie wokół obiektu MapTag z danymi. W najprostszym przypadku możesz go skonstruować za pomocą [tagu `discord_embed`](https://meta.denizenscript.com/Docs/Tags/discord_embed) i wypełnić go danymi za pomocą [`DiscordEmbedTag.with[key].as[value]`](https://meta.denizenscript.com/Docs/Tags/discordembedtag.with.as) lub [`DiscordEmbedTag.with_map`](https://meta.denizenscript.com/Docs/Tags/discordembedtag.with_map).

Istnieje długa lista kluczy wspieranych przez `DiscordEmbedTag`, więc sprawdź dokumentację tagów tego obiektu po szczegóły. Najczęściej używane są tytuły (`title`) i opisy (`description`).

Bardzo prosty embed można stworzyć całkowicie w jednej linii, używając `with_map` i składni mapy. Bardziej skomplikowane dane, zwłaszcza przy użyciu tagów, lepiej wprowadzać przy użyciu wariantu tagu `with[...].as[...]` (aby zapobiec błędom przetwarzania) lub [polecenia `definemap`](https://meta.denizenscript.com/Docs/Commands/definemap).

Przykład prostego użycia w linii:

```dscript_green
send_an_embed:
    type: task
    script:
    - define embed <discord_embed.with_map[title=Przykładowy Bot;description=Wow! Ten bot to naprawdę bot;timestamp=<util.time_now>;color=#00FFFF]>
    - ~discordmessage id:mojbot channel:<server.flag[discord_botspam]> <[embed]>
```

Zauważ również, że możesz użyć tagu `<n>`, aby uzyskać znak nowej linii w dowolnej wiadomości lub embedzie.

### Automatyzacja wiadomości

Zróbmy to w sposób zautomatyzowany! Korzystając ze [zdarzenia `discord message received`](https://meta.denizenscript.com/Docs/Events/discord%20message%20received), możesz wykryć, kiedy użytkownik Discorda wyśle wiadomość i uruchomić polecenia na podstawie jej treści. Możesz także odpowiedzieć na tym samym kanale, na którym wiadomość została wysłana! Na przykład, jeśli chcesz odpowiedzieć użytkownikowi, gdy jego wiadomość zawiera słowo „ping”:

```dscript_green
ping_pong:
    type: world
    events:
        after discord message received:
        - if <context.new_message.text.contains_text[ping]>:
            - ~discordmessage id:mojbot reply:<context.new_message> Pong!
            # lub: - ~discordmessage id:mojbot channel:<context.new_message.channel> Pong!
```

![](images/acikek_pingpong.png)

#### Zastosowanie: Mostek czatu (Chat Bridge)

Masz już podstawowe narzędzia do stworzenia mostka czatu. Podstawowy mostek działa w dwóch prostych krokach:

- Gdy gracz w Minecraft pisze na czacie, wyślij wiadomość na kanał Discorda.
- Gdy użytkownik Discorda wyśle wiadomość na kanał, rozgłoś ją graczom online w Minecraft.

Zajmijmy się pierwszą częścią. Możesz do tego użyć [zdarzenia `player chats`](https://meta.denizenscript.com/Docs/Events/player%20chats). Następnie wystarczy użyć polecenia `discordmessage`, jak w pierwszym przykładzie. Powinieneś także dołączyć imię gracza z Minecrafta, w przeciwnym razie użytkownicy Discorda nie będą wiedzieli, od kogo pochodzi wiadomość.

Pamiętaj, że formatowanie tekstu na Discordzie używa [Markdowna](https://support.discord.com/hc/en-us/articles/210298617-Markdown-Text-101-Chat-Formatting-Bold-Italic-Underline-) – tagi formatowania czatu Minecrafta z Denizen, takie jak `<bold>`, tutaj nie zadziałają.

```dscript_green
chat_bridge:
    type: world
    events:
        after player chats:
        - define message "**<player.name>**: <context.message>"
        - ~discordmessage id:mojbot channel:<server.flag[discord_chatrelay]> <[message]>
```

Świetnie! Teraz kolejny krok. Możesz użyć zdarzenia `discord message received` jak we wcześniejszym przykładzie, ale chcemy przesyłać wiadomość tylko wtedy, gdy pochodzi ona z konkretnego kanału. Na szczęście to zdarzenie jest wyposażone w przełącznik `channel`, którego potrzebujemy. Tym razem, ponieważ wiadomość zostanie wyświetlona w Minecraft, powinniśmy użyć tagów formatowania czatu Denizen zamiast Discord Markdown.

Zauważ, że otrzymany [obiekt `DiscordMessageTag`](https://meta.denizenscript.com/Docs/ObjectTypes/DiscordMessageTag) nie reprezentuje tylko samej treści wiadomości: możesz pobrać autora, kanał, ID i wiele więcej.

```dscript_blue
chat_bridge:
    type: world
    events:
        # Pamiętaj, że '12345' należy zastąpić skopiowanym, surowym ID kanału.
        # Niestety tagi typu server.flag obecnie nie działają w liniach etykiet zdarzeń.
        after discord message received channel:12345:
        # np.: [Discord] <acikek> Cześć!
        - announce "[<blue>Discord<&r>] <&lt><context.new_message.author.name><&gt> <context.new_message.text>"
```

### Polecenia ukośnika (Slash Commands)

Polecenia ukośnika to nowy sposób interakcji z botem na żądanie na Discordzie. Są wbudowane w klienta, co oznacza, że pomoc do polecenia można zobaczyć bez zewnętrznych zasobów. Pojawiają się one również na liście, gdy zaczniesz pisać wiadomość od `/` – śmiało, wypróbuj!

![](images/discord_slashcommands.png)

Dzięki dDiscordBot możesz tworzyć własne polecenia ukośnika. Należą one do zestawu funkcji znanych jako Interakcje (Interactions), obok przycisków i menu wyboru.

**Polecenie ukośnika wystarczy utworzyć tylko raz.** Utworzenie polecenia o tej samej nazwie aktualizuje to już istniejące.

Gdy użytkownik użyje polecenia ukośnika, musisz odpowiedzieć w ciągu zaledwie 5 sekund. Nie oznacza to jednak konieczności natychmiastowego wysłania wiadomości; zamiast tego, jeśli potrzebujesz więcej czasu, możesz *odroczyć* (defer) – czyli potwierdzić – żądanie, na które odpowiesz później.

Więcej o ograniczeniach poleceń ukośnika możesz przeczytać [tutaj](https://gist.github.com/MinnDevelopment/b883b078fdb69d0e568249cc8bf37fe9).

#### Zastosowanie: Polecenie ostatniego logowania

Zróbmy polecenie ukośnika sprawdzające czas ostatniego logowania gracza. Jeśli gracz jest online, powinniśmy zamiast tego wyświetlić taką informację. Najpierw musisz utworzyć polecenie. Możesz to zrobić za pomocą [polecenia `discordcommand`](https://meta.denizenscript.com/Docs/Commands/discordcommand) z argumentem `create`. Chcemy pobrać od użytkownika imię gracza; nazywa się to opcją i musi zostać dołączone do polecenia przy jego tworzeniu.

Tworząc polecenie ukośnika, możesz określić serwer (grupę), na którym ma być ono dostępne, za pomocą argumentu `group`. Jest to bardzo przydatne do testów, nawet jeśli w przyszłości chcesz, by polecenie było dostępne globalnie. **Rejestracja polecenia globalnego może zająć do godziny!**

Argument `options` to mapa map, gdzie wartości pasują do określonego formatu. Możesz go zobaczyć na [stronie meta](https://meta.denizenscript.com/Docs/Commands/discordcommand). Do tego celu zaleca się użycie polecenia `definemap`.

Argument `name` jest wymagany, a `description` opcjonalny, ale przydatny dla użytkowników. Wrzućmy wszystko do skryptu zadania:

```dscript_blue
create_lastlogin:
    type: task
    script:
    - definemap options:
        1:
            type: string
            name: player
            description: Imię gracza Minecraft
            required: true

    - ~discordcommand id:mojbot create name:lastlogin "description:Wyświetla czas ostatniego logowania gracza." "group:<discord[mojbot].group[Moj serwer]>" options:<[options]>
```

Gdy polecenie zostanie utworzone, możesz użyć [zdarzenia `discord slash command`](https://meta.denizenscript.com/Docs/Events/discord%20slash%20command), aby nasłuchiwać jego użyć. Pamiętaj, aby użyć przełącznika `name` dla nazwy polecenia.

Używanie poleceń ukośnika, przycisków i menu wyboru nazywa się interakcją i to są rzeczy, które musimy potwierdzić (acknowledge), jak wspomniano wcześniej. Możesz to obsłużyć [poleceniem `discordinteraction`](https://meta.denizenscript.com/Docs/Commands/discordinteraction), które wymaga argumentu `interaction`. Zauważ, że to polecenie **nie potrzebuje** argumentu `id`. Wszystkie trzy odpowiadające zdarzenia posiadają tag `<context.interaction>`, który zawiera już w sobie odniesienie do bota.

Dobrą praktyką jest odroczenie odpowiedzi (defer), nawet jeśli odpowiedź na interakcję nie zajmie dużo czasu. Użyj instrukcji `defer`, aby potwierdzić otrzymanie, oraz `reply`, aby odpowiedzieć wiadomością. Na razie przetestuj działanie prostym „Hello World”:

```dscript_green
lastlogin:
    type: world
    events:
        on discord slash command name:lastlogin:
        - ~discordinteraction defer interaction:<context.interaction>
        - ~discordinteraction reply interaction:<context.interaction> "Witaj, świecie!"
```

![](images/lastlogin_helloworld.png)

To polecenie polega na obsłudze przekazanej opcji. Skoro ustawiliśmy opcję `player` jako `required` (wymaganą), standardowy klient Discorda nie pozwoli użytkownikowi na użycie polecenia bez podania tego tekstu.

Tag `<context.options>` dla zdarzenia `discord slash command` zwraca MapTag z nazwami opcji i ich podanymi wartościami.

Możemy pobrać wartość imienia gracza przez `<context.options.get[player]>` i wstawić ją do `<server.match_offline_player[...]>`, aby pobrać właściwy obiekt gracza odpowiadający temu imieniu.

Jak uczymy na stronie [Częste błędy: Nie ufaj graczom](/guides/troubleshooting/common-mistakes#don-t-trust-players), nigdy nie ufaj danym od użytkownika. Nawet przy tej prostej interakcji użytkownik Discorda może: podać imię gracza, który nie istnieje, podać coś, co w ogóle nie jest imieniem gracza lub jest sformatowane jak niepowiązany obiekt, podać pustą wartość lub użyć błędu, by pominąć wymaganą listę opcji i nie podać nic. Dlatego upewnij się, że starannie weryfikujesz każdą informację i obsługujesz przypadki błędów prostym komunikatem odmownym.

Następnie zechcesz sprawdzić, czy gracz jest online czy offline, co możesz zrobić za pomocą [tagu `PlayerTag.is_online`](https://meta.denizenscript.com/Docs/Tags/playertag.is_online). Jeśli jest, możesz po prostu to napisać. Jeśli nie, możesz użyć [tagu `PlayerTag.last_played_time`](https://meta.denizenscript.com/Docs/Tags/playertag.last_played_time) i sformatować zwrócony [obiekt `TimeTag`](https://meta.denizenscript.com/Docs/ObjectTypes/timetag). Możemy użyć [tagu `TimeTag.format_discord`](https://meta.denizenscript.com/Docs/Tags/timetag.format_discord), aby wyświetlić ten czas czytelnie przy użyciu automatycznego formatowania Discorda – alternatywnie możesz użyć [`TimeTag.format[...]`](https://meta.denizenscript.com/Docs/Tags/timetag.format), by stworzyć własny format.

Pamiętaj o dołączeniu imienia gracza do wiadomości! `<server.match_offline_player[...]>` zwraca *najlepsze dopasowanie* do wejścia, co oznacza, że wynikowy gracz nie zawsze będzie tym, o którym myślał użytkownik, jeśli błędnie wpisał imię.

Oto ostateczny skrypt:

```dscript_green
lastlogin:
    type: world
    events:
        on discord slash command name:lastlogin:
        - ~discordinteraction defer interaction:<context.interaction>

        # Zauważ, że puste wejście do match_offline_player na pewno zwróci null.
        - define player <server.match_offline_player[<context.options.get[player].if_null[<empty>]>].if_null[null]>

        - if <[player]> == null:
            - ~discordinteraction reply interaction:<context.interaction> "To imię jest nieprawidłowe lub ten gracz nigdy nie dołączył do serwera!"
            - stop

        - if <[player].is_online>:
            - ~discordinteraction reply interaction:<context.interaction> "**<[player].name>** jest obecnie na serwerze!"
        - else:
            - define message "**<[player].name>** był ostatnio widziany: <[player].last_played_time.format_discord>"
            - ~discordinteraction reply interaction:<context.interaction> <[message]>
```

![](images/acikek_lastlogin.gif)

<span class="parens">(Format użyty na tym gifie to `<[player].last_played_time.format[LLLL dd, yyyy 'o' hh:mm a]>` zamiast tagu format_discord)</span>

### Klikalne cosie: Komponenty

Reszta zestawu funkcji interakcji obejmuje przyciski i menu wyboru, które znajdują się w osobnej kategorii: *komponenty*. Komponenty można dołączać zarówno do odpowiedzi na interakcje, jak i do zwykłych wiadomości (za pomocą argumentu `rows`), a po użyciu zwracają one interakcję.

Możesz skonstruować obiekt [`DiscordButtonTag`](https://meta.denizenscript.com/Docs/ObjectTypes/DiscordButtonTag) za pomocą [`<discord_button>`](https://meta.denizenscript.com/Docs/tags/discord_button). Następnie możesz dołączać właściwości przy użyciu tagów w stylu `with`, dokładnie tak jak przy `DiscordEmbedTag`. [`DiscordSelectionTag`](https://meta.denizenscript.com/Docs/ObjectTypes/DiscordSelectionTag) również działa w ten sposób.

Wspierane właściwości dla przycisku możesz zobaczyć [tutaj](https://meta.denizenscript.com/Docs/Tags/discordbuttontag.with.as), a dla menu wyboru [tutaj](https://meta.denizenscript.com/Docs/Tags/discordselectiontag.with.as).

Argument `rows` to lista list. Główne listy reprezentują oddzielne rzędy, podczas gdy listy wewnątrz reprezentują komponenty w każdym rzędzie (podobnie do kolumn w siatce). Możesz użyć `definemap`, aby łatwiej przedstawić taką warstwową listę. Dla pojedynczego przycisku możesz go po prostu podać bezpośrednio bez owijania w listę. Argument ten znajduje się zarówno w `discordinteraction`, jak i `discordmessage`. Dodatkowo możesz dowolnie mieszać komponenty w rzędach. Wiadomość jest jednak wciąż wymagana!

#### Przyciski

Na przykład wysłanie wiadomości z pojedynczym przyciskiem wyglądałoby tak:

```dscript_green
buttons:
    type: task
    script:
    - define click_me <discord_button.with[id].as[click_me].with[label].as[Kliknij mnie!].with[style].as[success]>
    - ~discordmessage id:mojbot channel:<server.flag[discord_botspam]> rows:<[click_me]> Przyciski!
```

Przykład z kilkoma przyciskami:

```dscript_green
buttons:
    type: task
    script:
    - definemap buttons:
        1:
            1: <discord_button.with[id].as[first].with[label].as[Kliknij mnie!].with[style].as[primary]>
            2: <discord_button.with[id].as[second].with[label].as[Nie, mnie kliknij!].with[style].as[secondary]>
        2:
            1: <discord_button.with[id].as[secondrow_first].with[label].as[Ten rząd jest lepszy!].with[style].as[danger]>
            2: <discord_button.with[id].as[https://denizenscript.com].with[label].as[Ta kolumna rządzi!].with[style].as[link]>
    - ~discordmessage id:mojbot channel:<server.flag[discord_botspam]> rows:<[buttons]> Przyciski!
```

ID powinno być unikalne dla przycisku: dzięki niemu odróżnisz jeden przycisk od drugiego. Zobaczysz to przy użyciu zdarzenia `discord button clicked`, które posiada przełącznik `id` (podobnie jak przełącznik `name` w zdarzeniu polecenia ukośnika). Wyjątkiem jest sytuacja, gdy Twój przycisk ma być po prostu linkiem – w takim przypadku ustaw ID na adres URL do otwarcia po kliknięciu.

Przykład odpowiedzi na naciśnięcie przycisku (dla przykładu z jednym przyciskiem):

```dscript_green
click_me:
    type: world
    events:
        on discord button clicked id:click_me:
        - ~discordinteraction defer interaction:<context.interaction>
        - ~discordinteraction reply interaction:<context.interaction> "Witaj, <context.interaction.user.name>!"
```

![](images/button_reply.png)

#### Menu wyboru (Selection Menus)

Menu wyboru działają jak połączenie przycisków i opcji poleceń.

Wejście opcji to mapa-lista map, gdzie każda mapa wewnętrzna może posiadać klucze `label`, `value`, `description` oraz `emoji`.

Emoji jest opcjonalne, a jeśli chcesz je dołączyć, najprostszym sposobem jest wysłanie emoji na Discordzie ze znakiem `\` przed nim, np. `\:smile:`, aby uzyskać surową formę emoji, a następnie użycie jej w skrypcie.

Sposób działania menu wyboru najlepiej pokazać na przykładzie:

```dscript_green
selection_menu:
    type: task
    script:
    # Podobnie jak opcje polecenia
    - definemap options:
        1:
            label: Dobrze
            value: mood_good
            description: Mam się nieźle.
            emoji: 🙂
        2:
            label: Lepiej
            value: mood_better
            description: Czuję się wspaniale!
            emoji: 😃
        3:
            label: Najlepiej
            value: mood_best
            description: Jestem na szczycie świata!
            emoji: 🤩

    - define menu <discord_selection.with[id].as[mood_menu].with[options].as[<[options]>]>
    - ~discordmessage id:mojbot channel:<server.flag[discord_botspam]> rows:<[menu]> "Jak się dzisiaj czujesz?"

mood_menu:
    type: world
    events:
        on discord selection used id:mood_menu:
        - ~discordinteraction defer interaction:<context.interaction>

        - choose <context.option.get[value]>:
            - case mood_good:
                - define message "Cieszę się."
            - case mood_better:
                - define message "Wow, to świetnie!"
            - case mood_best:
                - define message "Po prostu niesamowicie!"

        - ~discordinteraction reply interaction:<context.interaction> <[message]>
```

![](images/selection_menu.gif)

### Rzeczy do wypróbowania

Ta strona przewodnika obejmuje dwa małe projekty oraz podstawy komponentów wiadomości. Aby uzyskać więcej pomysłów, wróć do sekcji [Do czego można go użyć?](#do-czego-mozna-go-uzyc). Jeśli szukasz wyzwania, możesz spróbować:

- Mieszania różnych komponentów w wiadomościach
- Odpowiadania na interakcje prywatnie (podpowiedź: sprawdź dokumentację!)
- Stworzenia polecenia „informacje o graczu” (player info)

Użyj wyobraźni! Discord to kolejna platforma pełna unikalnych koncepcji i możliwości dla Twojej kreatywności.

### Częste problemy: Gdy coś poszło nie tak

Jeśli podczas testowania bota coś nie działa, oto kilka typowych problemów i sposobów ich rozwiązania:

- Przede wszystkim upewnij się dwukrotnie, że włączyłeś `Server Members Intent` na stronie bota Discorda. Wiele rzeczy się zepsuje, jeśli ta opcja jest wyłączona.
- Upewnij się, że Twój bot ma uprawnienia do tego, co próbujesz zrobić! Bot musi móc widzieć kanał, którego używasz, czytać na nim wiadomości i móc je tam wysyłać. Podczas pierwszych testów bota im więcej uprawnień, tym lepiej. Kiedy już wdrożysz bota do rzeczywistego użytku, ogranicz mu uprawnienia tylko do tych niezbędnych.
- Jeśli nie możesz zarejestrować poleceń ukośnika (z błędem `50001: Missing Access` w konsoli), być może zapomniałeś włączyć zakres `applications.commands` podczas dodawania bota na serwer.
- Podobnie jak przy wszystkich problemach z Denizen, monitoruj swoją konsolę – wyjście debugowania zawiera wiele pomocnych informacji pozwalających zdiagnozować, co dokładnie poszło nie tak.
- Jeśli utkniesz lub się zgubisz, poproś o pomoc na Discordzie. Zobacz też ogólną stronę Denizen dotyczącą [rozwiązywania napotkanych problemów](/guides/first-steps/problem-solving).

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się więcej o dDiscordBot, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

- [Polecenia dDiscordBot](https://meta.denizenscript.com/Docs/Commands/discor)
- [Tagi dDiscordBot](https://meta.denizenscript.com/Docs/Tags/discor)
- [Zdarzenia dDiscordBot](https://meta.denizenscript.com/Docs/Events/discord)
- [Strona dDiscordBot na GitHubie](https://github.com/DenizenScript/dDiscordBot)
- [Wewnętrzna dokumentacja referencyjna Discorda](https://discord.com/developers/docs/reference)
