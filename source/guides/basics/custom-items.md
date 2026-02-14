Bardziej ozdobne patyki: Tworzenie własnych przedmiotów (TYLKO PLACEHOLDER)
-----------------------------------

**TODO: Opis wprowadzający do kontenerów skryptów przedmiotów oraz ogólne koncepcje tworzenia własnych przedmiotów, z uwzględnieniem receptur, nazw/opisów (lore), mechanizmów, flag przedmiotów itp.**

### Placeholder (Miejsce na treść)

Dopóki ta strona nie zostanie napisana, możesz obejrzeć [stary film instruktażowy tutaj](https://one.denizenscript.com/denizen/vids/Custom%20Items).

### Przykładowy skrypt

Oto szybki przykład nowoczesnego skryptu przedmiotu.

```dscript_green
my_item:
    type: item
    material: stick
    display name: <&[item]>Ozdobny patyk!
    lore:
    - <&[lore]>Taki ozdobny.
```

Możesz otrzymać ten przedmiot w grze, wpisując `/ex give my_item`

Zauważ, że powyższy przykład używa własnych kodów kolorów `item` oraz `lore` zdefiniowanych w Twoim pliku `Denizen/config.yml`.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o własnych przedmiotach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja kontenerów skryptów przedmiotów](https://meta.denizenscript.com/Docs/Languages/item%20script%20containers)
- [Dokumentacja kontenerów skryptów książek](https://meta.denizenscript.com/Docs/Languages/book%20script%20containers)
