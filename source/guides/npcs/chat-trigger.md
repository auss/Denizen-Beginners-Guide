Wyzwalacze czatu (Chat Triggers) (CZĘŚCIOWY OPIS)
-----------------------------------------------

**TODO: Opis wyjaśniający wyzwalacze czatu, w tym zarówno standardowe wyzwalacze, jak i dynamiczne wyzwalacze regex (szczególnie catch-all). Przypomnij użytkownikowi o aktywacji wyzwalacza za pomocą `trigger` w `on assignment`.**

### Placeholder (Miejsce na treść)

Dopóki ta strona nie zostanie napisana, możesz obejrzeć [stary film instruktażowy tutaj](https://one.denizenscript.com/denizen/vids/Your%20First%20Interact%20Script%20And%20Chat%20Trigger).

### Przykładowy skrypt

Oto szybki przykład nowoczesnego skryptu interakcji z dwoma podstawowymi wyzwalaczami czatu.

```dscript_green
my_assignment:
    type: assignment
    actions:
        on assignment:
        - trigger name:chat state:true
    interact scripts:
    - my_interact

my_interact:
    type: interact
    steps:
        1:
            chat trigger:
                1:
                    trigger: /Witaj/ przyjazny NPC!
                    script:
                    - chat "Witaj, <player.name>!"
                2:
                    trigger: /Żegnaj/ przyjazny NPC!
                    script:
                    - chat "Bywaj, <player.name>!"
```

Ten skrypt można przypisać do wybranego przez Ciebie NPC za pomocą polecenia `/ex assignment set my_assignment`

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o wyzwalaczach czatu, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka Chat Triggers](https://meta.denizenscript.com/Docs/Languages/chat%20triggers)
