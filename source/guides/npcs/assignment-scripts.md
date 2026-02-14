Kim jestem: Skrypty przypisania (Assignment Scripts) (CZĘŚCIOWY OPIS)
----------------------------------------------------------------------

**TODO: Opis wyjaśniający podstawy skryptów przypisania, w tym akcję `on assignment`.**

### Placeholder (Miejsce na treść)

Dopóki ta strona nie zostanie napisana, możesz obejrzeć [stary film instruktażowy tutaj](https://one.denizenscript.com/denizen/vids/NPCs%20And%20You:%20Your%20First%20Assignment%20Script).

### Przykładowy skrypt

Oto szybki przykład nowoczesnego skryptu przypisania.

```dscript_green
my_assignment:
    type: assignment
    actions:
        on assignment:
        - trigger name:click state:true
        on click:
        - chat "Witaj <player.name>!"
```

Ten skrypt można przypisać do wybranego przez Ciebie NPC za pomocą polecenia `/ex assignment set script:my_assignment`

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o skryptach przypisania, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja kontenerów skryptów przypisania](https://meta.denizenscript.com/Docs/Languages/assignment%20script%20containers)
- [Dokumentacja akcji On Assignment](https://meta.denizenscript.com/Docs/Actions/assignment)
- [Lista wszystkich akcji skryptów przypisania](https://meta.denizenscript.com/Docs/Actions)
