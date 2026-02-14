Wyzwalacze zbliżeniowe (Proximity Triggers) (CZĘŚCIOWY OPIS)
---------------------------------------------------------

**TODO: Opis wyjaśniający wyzwalacze zbliżeniowe, w tym wyzwalacze wejścia (entry), wyjścia (exit) oraz ruchu (move). Przypomnij użytkownikowi o aktywacji wyzwalacza za pomocą `trigger` w `on assignment`.**

### Placeholder (Miejsce na treść)

Dopóki ta strona nie zostanie napisana, możesz obejrzeć [stary film instruktażowy tutaj](https://one.denizenscript.com/denizen/vids/Proximity%20Triggers).

### Przykładowy skrypt

Oto szybki przykład nowoczesnego skryptu interakcji z podstawowymi wyzwalaczami zbliżeniowymi.

```dscript_green
my_assignment:
    type: assignment
    actions:
        on assignment:
        - trigger name:proximity state:true radius:10
    interact scripts:
    - my_interact

my_interact:
    type: interact
    steps:
        1:
            proximity trigger:
                entry:
                    script:
                    - chat "Witaj, <player.name>!"
                exit:
                    script:
                    - chat "Żegnaj, <player.name>!"
                move:
                    script:
                    - ratelimit <player> 10s
                    - chat "Nadal tu krążysz, <player.name>?"
```

Ten skrypt można przypisać do wybranego przez Ciebie NPC za pomocą polecenia `/ex assignment set script:my_assignment`

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o wyzwalaczach zbliżeniowych, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka Proximity Triggers](https://meta.denizenscript.com/Docs/Languages/proximity%20triggers)
