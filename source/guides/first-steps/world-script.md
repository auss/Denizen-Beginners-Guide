Twój pierwszy skrypt świata (World Script)
------------------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Działa samo!

Nauczyłeś się już, [jak używać „/ex” do uruchamiania pojedynczego polecenia Denizen](/guides/first-steps/ex-command) oraz [jak tworzyć skrypty „task”, aby uruchamiać serię poleceń razem](/guides/first-steps/task-script). To bardzo przydatne narzędzia, ale wymagają one od Ciebie siedzenia i wpisywania poleceń, aby coś się stało. Czy sensem silnika skryptowego nie powinno być automatyczne uruchamianie się, gdy jest to potrzebne, aby tworzyć własne rzeczy na serwerze? Byłoby absurdalne siedzieć na serwerze i wpisywać polecenia `/ex` za każdym razem, gdy gracz czegoś potrzebuje!

### Przedstawiamy: Skrypt świata (World Script)

Przyjrzyjmy się głównemu sposobowi tworzenia automatycznie wyzwalanych skryptów: kontenerom skryptów typu `world`!

Skrypt świata zawiera `events` (zdarzenia). Zdarzenie to zasadniczo rzecz, która dzieje się w świecie. Na przykład, gdy gracz niszczy blok, istnieje dla tego zdarzenie. Większość zdarzeń nazywa się dość jasno i prosto – na przykład wspomniane zdarzenie to `player breaks block`. Zauważ, że wszystkie zdarzenia zaczynają się od słowa `on` lub `after`, a po nich następuje zwykle angielska fraza opisująca zdarzenie. Kilka przykładów nazw zdarzeń: `on player places block`, `after entity dies`, `on creeper powered`, `after lightning strikes`... <span class="parens">(różnica między `on` a `after` została wyjaśniona w dalszej części tej strony)</span>.

### Zobaczmy prawdziwy skrypt świata!

```dscript_green
my_world_script:
    type: world
    events:
        after player breaks block:
        - narrate "Łohoho <player.name>, zniszczyłeś blok!"
```

Śmiało, umieść to w pliku skryptu, użyj `/ex reload`, aby go załadować, a następnie zniszcz dowolny blok. Zobaczysz wiadomość na czacie.

![](images/brokeablock.png)

Kilka rzeczy, na które warto zwrócić uwagę w tym przykładzie:
- Typ (`type`) tych skryptów to teraz `world`.
- Zamiast `script:` z poprzednich przykładów, mamy teraz `events:`, który zawiera w sobie właściwą nazwę zdarzenia.
- Samo zdarzenie <span class="parens">(`after player breaks block`)</span> jest wcięte o kolejne 4 spacje za `events:`, co czyni je częścią bloku zdarzeń, a nie kluczem na poziomie podstawowym skryptu.
- Gracz, który zniszczył blok, jest automatycznie przypisanym graczem w tym kontekście. Zatem polecenie narrate wyświetli się temu graczowi, a `<player.name>` będzie jego imieniem.

Zdarzenie zostanie uruchomione za każdym razem, gdy dana rzecz wydarzy się w świecie. Za każdym razem, gdy jakikolwiek gracz zniszczy jakikolwiek blok, ten skrypt się uruchomi. Jeśli 5 graczy zniszczy blok w tym samym czasie, skrypt uruchomi się 5 razy – raz dla każdego gracza, za każdym razem łącząc się z konkretnym graczem, który zniszczył blok. Jeśli jeden gracz weźmie łopatę z wydajnością 5 i zniszczy 10 bloków ziemi w mniej niż sekundę... tak, zdarzenie uruchomi się 10 razy, za każdym razem z tym samym graczem.

### Dobra, ale jaki blok zniszczyli?

Wiesz już, że zniszczyli blok, gdy ten skrypt się uruchamia, ale na pewno zastanawiasz się, jak dowiedzieć się, jaki typ bloku został zniszczony <span class="parens">(w końcu zniszczenie bloku ziemi i bloku rudy diamentu prawdopodobnie nie powinno być obsługiwane w ten sam sposób)</span>. Istnieją na to dwa sposoby.

#### Tagi kontekstowe (Context Tags)

Pierwszym sposobem na dowiedzenie się, jaki typ bloku został zniszczony, są tagi `context`. Tagi kontekstowe, jak sama nazwa wskazuje, dostarczają szczegółów na temat kontekstu uruchomienia skryptu (kiedy/gdzie/dlaczego). W zdarzeniu tagi kontekstowe zawierają wszystko, co musisz wiedzieć o tym, co się stało.

W zdarzeniu `on player breaks block` dostępny jest tag `<context.material>`, który pozwala pobrać materiał. Zwraca on `MaterialTag`, a tag `<MaterialTag.name>` pobiera nazwę materiału. Połączmy je więc, tworząc `<context.material.name>`.

Spróbuj wstawić ten tag do linii z narrate, np. `- narrate "Łohoho <player.name>, zniszczyłeś blok typu <context.material.name>!"`, a następnie przeładuj skrypty i zniszcz kilka bloków. Powinieneś widzieć nazwę każdego typu bloku, który niszczysz.

![](images/brokeastone.png)

Zauważ, że tagi kontekstowe są zawsze tagami bazowymi i nigdy nie są sub-tagami <span class="parens">(chociaż oczywiście możesz dołączyć sub-tag na końcu tagu kontekstowego, kiedy tylko potrzebujesz)</span>.

Zauważ również, że tagi kontekstowe, naturalnie, istnieją tylko w swoim odpowiednim kontekście. Zdarzenie `on entity dies` nie posiada tagu `<context.material>`, ponieważ nie jest on częścią tego zdarzenia. Podobnie skrypt zadania (task script) lub polecenie `/ex` nie będą miały żadnych kontekstów, ponieważ nie są częścią żadnego zdarzenia.

#### Bardziej szczegółowe linie zdarzeń

Nazwy zdarzeń pozwalają na dodanie dodatkowych specyfikacji, takich jak wypełnienie parametru lub przełącznik zdarzenia (event switch).

Zdarzenie niszczenia bloku jest udokumentowane jako: `player breaks block` lub `player breaks <material>`. Ta druga wersja daje nam opcję wpisania konkretnego materiału. W tym kontekście `<>` oznacza „wpisz tutaj swoją konkretną wartość”. Spróbuj więc zmienić linię zdarzenia w swoim skrypcie na `after player breaks stone:`, przeładuj i spróbuj zniszczyć kilka różnych typów bloków, w tym kamień (stone). Zobaczysz, że narracja pojawia się tylko wtedy, gdy zniszczysz kamień. Jeśli zniszczysz ziemię lub cokolwiek innego, nic się nie stanie.

Przełączniki zdarzeń (event switches) to dodatkowe opcje zdarzenia poza podstawowym wejściem nazwy. Zdarzenie niszczenia bloku ma dostępnych kilka przełączników, w tym jeden udokumentowany jako `with:<item>`, służący do przetwarzania zdarzenia tylko wtedy, gdy gracz niszczy blok określonym przedmiotem.

Aby to wykorzystać, możesz napisać np.: `after player breaks stone with:diamond_pickaxe:`, co sprawi, że skrypt uruchomi się tylko wtedy, gdy gracz użyje diamentowego kilofa do zniszczenia kamienia – jeśli spróbujesz zrobić to żelaznym kilofem lub czymkolwiek innym, skrypt po prostu się nie uruchomi.

#### Linie zdarzeń są statyczne

Zauważ, że linie zdarzeń nigdy nie mogą zawierać tagów – muszą zawsze być poprawnym, czystym tekstem. W [dalszej sekcji](/guides/basics/if-command) dowiesz się, jak zmieniać działanie skryptu w oparciu o bardziej dynamiczne sprawdzenia oparte na tagach.

Istnieje jednak pewna ograniczona dynamika. Rozważmy przełącznik `with:diamond_pickaxe`, którego użyliśmy powyżej. Co jeśli chcemy, aby liczył się *dowolny* kilof? Możemy do tego użyć `with:*_pickaxe`. Symbol `*` oznacza „cokolwiek w tym miejscu”. Podobnie, jeśli chcemy tylko diamentowy lub żelazny, możemy użyć `with:diamond_pickaxe|iron_pickaxe`. Symbol `|` oznacza „którykolwiek z nich się liczy”. Możesz to również zastosować do części wejściowych linii zdarzenia. Na przykład, jeśli chcesz reagować na graczy niszczących kłody drewna, ale nie chcesz pisać osobnego zdarzenia dla każdego typu drewna, możesz po prostu użyć `after player breaks *_log:`.

### Dobra, ale przestańcie niszczyć moje bloki, proszę

Możesz więc uruchomić skrypt, gdy wystąpi zdarzenie. Możesz również sprawdzić szczegóły zdarzenia. Ale jak *coś zrobić* ze zdarzeniem? Czy musimy tylko pisać `- narrate "*wygraża pięścią* przestań niszczyć moje bloki!"`?

Tutaj wchodzi polecenie `determine`. Polecenie determine służy do *określania wyniku* zdarzenia (determination). Jeśli go nie użyjesz, zdarzenie po prostu dzieje się tak, jak zwykle. Kiedy użyjesz determine, możesz zmienić dowolną rzecz dotyczącą zdarzenia <span class="parens">(w rozsądnych granicach modyfikacji zdarzenia – jeśli chcesz, aby w odpowiedzi na zdarzenie nastąpiła niepowiązana zmiana w świecie, użyj odpowiedniego polecenia, które tę zmianę wywołuje)</span>.

Najczęściej dostępną determinacją, którą wspierają prawie wszystkie zdarzenia, jest `- determine cancelled`. To *anuluje* zdarzenie – oznacza to, że zdarzenie albo w ogóle się nie odbędzie, albo natychmiast zostanie cofnięte. W przypadku zdarzenia `breaks block` sprawi to, że blok nigdy nie zostanie zniszczony <span class="parens">(obserwujący gracze zobaczą, że nic się nie dzieje, jednak gracz, który próbował zniszczyć blok, zobaczy, jak na chwilę znika i natychmiast powraca – jest to konsekwencja przewidywania po stronie klienta i niestety jest nieuniknione bez modyfikacji klienta)</span>.

Wypróbujmy to! Po linii narrate w naszym skrypcie testowym dodaj determine. Jeśli wprowadziłeś wszystkie sugerowane dotychczas zmiany, Twój skrypt powinien teraz wyglądać tak:

```dscript_red
my_world_script:
    type: world
    events:
        after player breaks stone with:diamond_pickaxe:
        - narrate "Łohoho <player.name>, zniszczyłeś blok typu <context.material.name>!"
        - determine cancelled
```

Zaraz... to nie zadziałało. Co poszło nie tak?

#### Mamy zdarzenia „teraz” i „wtedy”

Tutaj właśnie ujawnia się różnica między `after` a `on`. `after` oznacza uruchomienie skryptu *po tym*, jak zdarzenie już się wydarzyło i zakończyło. `on` oznacza uruchomienie skryptu *zanim* zdarzenie się wydarzy i kiedy można je jeszcze zmienić. Skutecznie, gdy używamy `after`, zdarzenie jest wydarzeniem *przeszłym*, a gdy używamy `on`, jest wydarzeniem *przyszłym*. Możesz użyć `determine` tylko wewnątrz `on`, a nie `after`, z bardzo prostego powodu: nie możesz zmienić przeszłości!

Zmień więc `after` w naszym przykładowym skrypcie na `on`.

```dscript_green
my_world_script:
    type: world
    events:
        on player breaks stone with:diamond_pickaxe:
        - narrate "Łohoho <player.name>, zniszczyłeś blok typu <context.material.name>!"
        - determine cancelled
```

Ten skrypt teoretycznie oznacza, że jeśli zniszczysz kamień diamentowym kilofem, zostaniesz powstrzymany. Jeśli zniszczysz inny blok lub nie użyjesz diamentowego kilofa, wszystko zadziała normalnie.

![](images/breakingthemstones.gif)

Zauważ, że polecenie determine jest domyślnie uważane za koniec skryptu. Jeśli chcesz użyć polecenia determine, ale potem kontynuować wykonywanie poleceń w skrypcie <span class="parens">(na przykład, aby zastosować kilka różnych determinacji do zmiany wielu części zdarzenia)</span>, musisz użyć argumentu `passively`, np. `- determine passively cancelled`.

#### Zmienianie świata na lepsze

Jeśli chcesz zmienić zdarzenie, zamiast po prostu je anulować, większość zdarzeń ma dostępne specyficzne opcje determinacji. Niszczenie bloku ma opcję determinacji `nothing`, aby żadne przedmioty nie wypadły po zniszczeniu bloku w trybie przetrwania, oraz opcję podania przedmiotu lub listy przedmiotów, aby blok po zniszczeniu upuścił właśnie te wymienione rzeczy.

Jako przykład tego, jak możesz to wykorzystać: możesz użyć tagu `<MaterialTag.item>`, aby pobrać przedmiot z dowolnego materiału, i w połączeniu z dostępnym tagiem kontekstowym `<context.material>` stworzyć linię: `- determine <context.material.item>`. Właśnie zyskałeś darmową moc „jedwabnego dotyku” (silk touch)! Każdy blok upuszcza teraz swoją dokładnie surową formę – więc jeśli na przykład wykopiesz blok rudy diamentu, otrzymasz blok rudy diamentu zamiast samych diamentów <span class="parens">(pamiętaj, że będziesz musiał zmienić linię zdarzenia z powrotem na `block` zamiast `stone`, albo określić `diamond_ore` lub cokolwiek podobnego)</span>.

#### Całe mnóstwo zdarzeń

Dodatkowa uwaga, o której warto wiedzieć: jeden skrypt świata może zawierać kilka zdarzeń. Może to wyglądać mniej więcej tak:

```dscript_green
my_world_script:
    type: world
    events:
        after player breaks block:
        - narrate "Łohoho <player.name>, zniszczyłeś blok typu <context.material.name>!"
        on player breaks stone with:diamond_pickaxe:
        - determine cancelled
        after player places stone:
        - narrate "Dlaczego kładziesz tam ten kamień? Lepiej nie niszcz go diamentowym kilofem!"
```

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o zdarzeniach świata, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka Switches](https://meta.denizenscript.com/Docs/Languages/script%20event%20switches)
- [Dokumentacja języka Player switches](https://meta.denizenscript.com/Docs/Languages/player%20event%20switches)
- [Dokumentacja języka Advanced matching](https://meta.denizenscript.com/Docs/Languages/advanced%20script%20event%20matching)
- [Dokumentacja języka Cancellation](https://meta.denizenscript.com/Docs/Languages/script%20event%20cancellation)
- [Dokumentacja języka Special contexts](https://meta.denizenscript.com/Docs/Languages/script%20event%20special%20contexts)
- [Dokumentacja języka After vs On](https://meta.denizenscript.com/Docs/Languages/script%20event%20after%20vs%20on)
- [Dokumentacja języka Safety in events](https://meta.denizenscript.com/Docs/Languages/safety%20in%20events)
- [Dokumentacja języka Priority](https://meta.denizenscript.com/Docs/Languages/script%20event%20priority)
- [Dokumentacja języka Bukkit priority](https://meta.denizenscript.com/Docs/Languages/bukkit%20event%20priority)
- [Dokumentacja kontenerów skryptów świata](https://meta.denizenscript.com/Docs/Languages/world%20script%20containers)
- [Lista wszystkich zdarzeń świata](https://meta.denizenscript.com/Docs/Events/)
