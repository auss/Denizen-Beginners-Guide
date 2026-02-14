Co można zrobić za pomocą Denizen?
-----------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Pozwól, że ujmę to prosto

![](images/throughdenizen.gif)

### Co zaawansowany użytkownik może zrobić z Denizen?

**Co można zrobić z Denizen... jeśli jesteś oddany sprawie? Wszystko, co tylko przyjdzie Ci do głowy!**
- Oto różnorodne, wysokiej jakości skrypty opublikowane publicznie [w sekcji zasobów forum Denizen](https://forum.denizenscript.com/resources/categories/denizen-scripts.1/?prefix_id=2)
- (Nieaktualne) Oto menedżer regionów, który konkuruje z WorldGuard, napisany jako skrypt Denizen: [Spigot - dRegions](https://www.spigotmc.org/resources/denizen-dregions.26864/)
- (Nieaktualne) Oto skrypt o długości zaledwie około 20 linii, który sprawia, że pochodnia świeci, gdy trzyma się ją w dłoni: [Forums - Denizen Torch Light](https://forum.denizenscript.com/viewtopic.php?f=13&t=8)

![](images/torchlight.png)

- (Nieaktualne) W przeszłości, zanim ja (mcmonkey) stworzyłem Sentinel, napisałem ten sam podstawowy koncept jako w pełni funkcjonalny skrypt Denizen: [Old Repo Archive - dSentry](https://one.denizenscript.com/denizen/repo/entry/0)

![](images/dsentry.png)

- Lub cokolwiek innego szalonego, co możesz sobie wyobrazić. Oto GUI skrzyni z klawiaturą numeryczną do wpisywania:

![](images/bankkeypad.gif)

- Wciąż mało? Potrzebujesz czegoś jeszcze bardziej dzikiego i niezwykłego? Cóóżż... oto ktoś [używający skryptu Denizen do zamawiania pizzy z wnętrza Minecrafta.](https://www.youtube.com/watch?v=5ml8EKTswu8)

### Co zwykły użytkownik może zrobić z Denizen?

**Co można zrobić z Denizen... jeśli jesteś bardziej przeciętnym użytkownikiem? Wciąż całkiem sporo!**
- Bardzo powszechne jest pisanie różnorodnych podstawowych narzędzi serwerowych w skryptach Denizen, takich jak codzienny automatyczny restart, który składa się zaledwie z kilku linii skryptu. W rzeczywistości, oto dokładnie taki skrypt:
```dscript_green
daily_autorestart:
    type: world
    debug: false
    events:
        on system time 01:00:
        - announce "<gold>Restart serwera za dziesięć minut!"
        - wait 5m
        - announce "<gold>Restart serwera za pięć minut!"
        - wait 2m
        - announce "<gold>Restart serwera za trzy minuty!"
        - wait 1m
        - announce "<gold>Restart serwera za dwie minuty!"
        - wait 1m
        - announce "<gold>Restart serwera za minutę!"
        - wait 30s
        - announce "<gold>Restart serwera za trzydzieści sekund!"
        - wait 30s
        - adjust server restart
```
To takie proste! Większość miejsca zajmują proste skopiowane i wklejone wiadomości.
- Tworzenie zadań (questów) w Denizen jest również bardzo popularne (zazwyczaj opartych na NPC, sprawdź stronę [Co Denizen potrafi zrobić z Citizens?](/guides/background/denizen-citizens), aby uzyskać więcej informacji).
- Systemy własnych przedmiotów są również dość popularne. Możesz pisać skrypty przedmiotów tak prosto, jak w tym przykładzie:
```dscript_green
hero_sword:
    type: item
    debug: false
    material: diamond_sword
    display name: <gold>Miecz Bohatera!
    lore:
    - Miecz nadawany tylko największym bohaterom.
    enchantments:
    - sharpness:5
```
Możesz łatwo użyć `- give hero_sword` w dowolnym momencie i wykorzystać proste zdarzenia świata, aby nadać swoim przedmiotom specjalne właściwości.
