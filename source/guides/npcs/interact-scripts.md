Co mam robić: Skrypty interakcji (Interact Scripts) (CZĘŚCIOWY OPIS)
----------------------------------------------------------------------

**TODO: Opis wyjaśniający podstawy skryptów interakcji, w tym listę 4 podstawowych wyzwalaczy (triggers), koncepcję kroków (steps) i polecenia zap, oraz sposób aktywacji skryptu interakcji w odpowiednim skrypcie przypisania (assignment script). Zademonstruj wyzwalacz kliknięcia (click trigger).**

### Placeholder (Miejsce na treść)

Dopóki ta strona nie zostanie napisana, możesz obejrzeć [stary film instruktażowy tutaj](https://one.denizenscript.com/denizen/vids/Your%20First%20Interact%20Script%20And%20Chat%20Trigger).

### Przykładowy skrypt

Oto szybki przykład nowoczesnego skryptu interakcji z dwoma podstawowymi krokami.

```dscript_green
my_assignment:
    type: assignment
    actions:
        on assignment:
        - trigger name:click state:true
    interact scripts:
    - my_interact

my_interact:
    type: interact
    steps:
        1:
            click trigger:
                script:
                - chat "Witaj, <player.name>!"
                - zap 2
        2:
            click trigger:
                script:
                - chat "Witaj część 2, <player.name>!"
                - zap *
```

Ten skrypt można przypisać do wybranego przez Ciebie NPC za pomocą polecenia `/ex assignment set script:my_assignment`

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o skryptach interakcji, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja kontenerów skryptów interakcji](https://meta.denizenscript.com/Docs/Languages/interact%20script%20containers)
- [Dokumentacja polecenia Trigger](https://meta.denizenscript.com/Docs/Commands/trigger)
- [Dokumentacja języka Interact Script Triggers](https://meta.denizenscript.com/Docs/Languages/Interact%20Script%20Triggers)
