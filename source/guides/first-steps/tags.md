Twoje pierwsze tagi
---------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Czym jest tag?

Jeśli postępujesz zgodnie z tym przewodnikiem <span class="parens">(a powinieneś!)</span>, to już użyłeś tagu. W [przewodniku po poleceniu ex](/guides/first-steps/ex-command) wypróbowałeś `/ex narrate "cześć <player.name>"`. Ta część `<player.name>` to właśnie **tag** w Denizen. Jest to nieco podobne do tego, co niektóre wtyczki nazywają „placeholderem” (symbolem zastępczym), ale z o wiele większymi możliwościami. Tag „player.name”, jak prawdopodobnie się domyślasz, jest automatycznie zastępowany Twoją nazwą użytkownika.

![](images/playernameexample.png)

Tagi w Denizen są oznaczone nawiasami `<>` wokół opisu tego, jaka wartość powinna zastąpić tag. Tagi mogą dostarczyć Ci niemal każdą potrzebną informację, kiedy tylko jej potrzebujesz.

### Najpopularniejsze tagi

Najczęściej używanymi tagami, bardziej niż jakiekolwiek inne, są tagi kolorów. Tagi kolorów są również jednymi z najprostszych tagów w Denizen. Prawdopodobnie znasz podstawowy system kodów kolorów w Minecraft, taki jak `§7` dla koloru szarego. Prawdopodobnie znasz też wtyczki, które pozwalają wpisać coś w stylu `&7`, aby pokolorować tekst na szaro. Zauważ, że Minecraft wewnętrznie używa znaku sekcji `§`, ale zwykły format wtyczek wykorzystuje ampersand `&` <span class="parens">(wynika to z tego, że `§` jest trudny do wpisania, a `&` jest łatwy na większości klawiatur)</span>. Aby uzyskać szary tekst w Denizen, wpisujesz `<&7>`, co jest wciąż łatwe do wpisania, ale specjalnie sformatowane przy użyciu systemu tagów <span class="parens">(oznacza to, że ma nawiasy `<>`, co wskazuje, że `<&7>` powinno zostać zastąpione przez rzeczywisty wewnętrzny kod koloru jasnoszarego ze znakiem `§`)</span>. Możesz również zamiast tego wpisać `<gray>`, aby uzyskać dokładnie taki sam wynik... to, który z nich wybierzesz, zależy od Twoich preferencji <span class="parens">(czy wolisz krótkie tagi, czy bardziej czytelne? Czy lepiej znasz nazwy kolorów, czy ich wewnętrzne numery ID?)</span>. Możesz też wpisać `<&color[gray]>` lub `<element[mój tekst tutaj].color[gray]>` lub... na wiele innych sposobów zapisać to samo. W miarę postępów w tym przewodniku zauważysz, że posiadanie całego mnóstwa opcji osiągnięcia tego samego celu jest powracającym motywem w Denizen.

Chociaż kolory tekstu są ogólnie najczęściej używanymi tagami, prawie każdy prawdziwy skrypt będzie używał zestawu tagów, które są nieco bardziej skomplikowane niż tylko kody kolorów.

### Jak tagi wyglądają w rzeczywistym użyciu?

Prostym przykładem użycia tagu może być `- teleport <npc> <player.location>`, aby przeteleportować NPC <span class="parens">(pobranego przez tag `<npc>`)</span> do Twojej własnej lokalizacji <span class="parens">(pobranej przez tag `<player.location>`)</span>.

Użycie tagów może stać się o wiele bardziej złożone, jak np. `- teleport <player.location.find_entities[living].within[<[range]>]> <player.cursor_on[<[distance]>].if_null[<player.location.forward[<[distance]>]>]>` ... nie martw się, jeśli to zupełnie nie ma dla Ciebie sensu – to celowo długi i skomplikowany przykład, aby dać Ci przedsmak potęgi, jaką mają tagi w rękach doświadczonego użytkownika <span class="parens">(a to dopiero początek – jedynym ograniczeniem jest wyobraźnia skrypciarza!)</span>. Podstawowe skrypty prawie nigdy nie będą wymagały tak długich tagów.

### Więc jak stworzyć tag?

Jak mogłeś się domyślić, patrząc na powyższy długi przykład – tagi nie ograniczają się tylko do listy wybranych wcześniej tagów, które udostępniliśmy. Tagi są *budowane* przez skrypciarza z wielu części, aby dopasować je do jego potrzeb.

#### Części tagu

Istnieją dwa podstawowe „typy” tagów, które są dostępne dla skrypciarzy jako bloki konstrukcyjne. Te typy to: **tagi bazowe (base tags)** oraz **sub-tagi**.

#### Tagi bazowe

Tag bazowy to coś w rodzaju `<player>`, który zwraca `PlayerTag` sytuacyjnie odpowiedniego gracza. Istnieją również pozornie wieloczęściowe tagi bazowe, takie jak `<server.motd>` <span class="parens">(chociaż czasem są one interpretowane tak, że `<server>` zwraca „ServerTagBase”, a `<ServerTagBase.motd>` jest jego sub-tagiem)</span>.

Tagi bazowe istnieją samodzielnie i mogą być używane tylko jako pierwsza część tagu. Każdy ukończony tag posiada dokładnie jeden użyty tag bazowy.

#### Sub-tagi

Sub-tag to coś w rodzaju `<PlayerTag.name>`, który zwraca `ElementTag` z imieniem odpowiedniego gracza. Sub-tag w dokumentacji zawsze zaczyna się od `(Coś)Tag`, jak `PlayerTag` lub `ItemTag`. Wszystko, co nie jest w tym formacie, jest tagiem bazowym.

Sub-tagi są dołączane na końcu istniejącego tagu. Kompletny tag może mieć dowolną liczbę sub-tagów – zero <span class="parens">(sama baza)</span>, jeden <span class="parens">(bardzo prosty tag)</span>, pięćset <span class="parens">(niesamowicie długi tag)</span> lub dowolną inną liczbę.

#### Łączenie części

Widzieliśmy powyżej przykład sub-tagu `<PlayerTag.name>`. Kiedy sub-tag zaczyna się od „PlayerTag”, oznacza to zasadniczo „wstaw tutaj gracza”... ponieważ tag bazowy `<player>` zwraca gracza, a `<PlayerTag.name>` potrzebuje gracza jako swojej pierwszej części, możesz je połączyć, tworząc `<player.name>`, który zwraca imię sytuacyjnie odpowiedniego gracza. W przypadku polecenia `/ex`, `<player.name>` zwraca *Twoje* imię.

#### Dłuższe łańcuchy

Tag `<PlayerTag.target>` zwraca encję, na którą patrzy gracz. Jeśli użyjesz go, patrząc na innego gracza, zwróci on `PlayerTag` tego gracza. Możemy więc użyć zasady „wstaw cokolwiek, co zwraca poprawny PlayerTag wszędzie tam, gdzie potrzebny jest PlayerTag”, aby wstawić `<player>` do tagu, tworząc `<player.target>`. Ponieważ w tym przykładzie teoretycznie patrzymy na innego gracza, możemy powtórzyć tę zasadę, wstawiając ten nowy tag na początku `<PlayerTag.name>`. Daje to w rezultacie `<player.target.name>` – funkcjonalnie kompletny tag, który zwraca imię gracza, na którego patrzysz!

![](images/howtagsfittogether.gif)

### PlayerTag to gracz w tagu

Ten wybór słownictwa nie jest trudny do odszyfrowania. PlayerTag to gracz reprezentowany przez tag – kto by pomyślał?! Podobnie NPCTag to NPC w tagu, EntityTag to dowolna encja w tagu, ItemTag to dowolny przedmiot w tagu... moglibyśmy tak wymieniać długo, istnieje mnóstwo typów rzeczy, których możesz użyć w tagu.

Jedno słowo, którego użyliśmy wcześniej, może być nieco mniej jasne: „ElementTag”. ElementTag to najbardziej podstawowy typ wartości: cokolwiek! ElementTag może być liczbą, tekstem, danymi binarnymi, a nawet graczem.

#### Chwila, ale przed chwilą powiedziałeś, że gracze to PlayerTagi

Tak, gracze to PlayerTagi. A także ElementTagi. I również EntityTagi.

W Denizen typy obiektów mogą mieć podtypy. Godnym uwagi przykładem jest to, że PlayerTag i NPCTag mają podtyp EntityTag. Oznacza to, że wszędzie tam, gdzie potrzebny jest `EntityTag` <span class="parens">(na przykład tag `<EntityTag.health>`)</span>, można wstawić PlayerTag lub NPCTag i będzie to działać dokładnie tak, jak oczekiwano <span class="parens">(zatem `<player.health>` zwróci wartość życia gracza)</span>.

ElementTag, jak powiedziano, jest najbardziej podstawowym typem wartości. Każdy pojedynczy typ obiektu w Denizen ma podtyp ElementTag. Oznacza to, że wszędzie tam, gdzie potrzebny jest `ElementTag`, można wstawić *dowolną* wartość. PlayerTag, NPCTag, ItemTag lub cokolwiek innego – wszystkie są poprawnymi danymi wejściowymi dla wszystkiego, co przyjmuje ElementTag.

### Niektóre tagi przyjmują parametry (wejścia)

Niektóre tagi potrzebują wartości wejściowej. Na przykład tag `<util.random.int[<#>].to[<#>]>` przyjmuje dwie proste wartości numeryczne <span class="parens">(„od” i „do”)</span> i zwraca losową liczbę pomiędzy nimi.

W najprostszym przypadku wejścia te można po prostu wypełnić dowolnym typem wartości, jakiego oczekują. Możesz więc wpisać w grze `/ex narrate <util.random.int[1].to[10]>`, a system – zgodnie z oczekiwaniami – wyświetli losową liczbę od 1 do 10. Jeśli powtórzysz to polecenie kilka razy, za każdym razem otrzymasz inną losową liczbę <span class="parens">(chyba że się powtórzy, co prawdziwa losowość czasem robi)</span>.

![](images/utilrandomexample.png)

#### Tag jako parametr innego tagu?!

Proste wartości wejściowe są dobre do testów, ale w rzeczywistym użyciu często znajdziesz powód, by wstawić dynamiczną wartość jako parametr tagu. Oto zabawny przykładowy skrypt:

```dscript_green
slowly_dying:
    type: task
    script:
    - narrate "Myślisz, że jesteś taki wyjątkowy, bo zostało Ci <player.health> życia?!"
    - hurt <player> <util.random.decimal[0].to[<player.health.div[2]>]>
    - narrate "Ha! Masz za swoje, <player.name>! Teraz zostało Ci tylko <player.health> życia!"
```

Za każdym razem, gdy ten przykładowy skrypt zostanie uruchomiony, gracz straci losową ilość życia, maksymalnie do połowy jego aktualnego stanu, zazwyczaj mniej. Skrypt powie mu również, ile życia miał przed i po zadaniu obrażeń.

Jak widać w przykładzie, tag `<player.health.div[2]>` został użyty jako drugi parametr dla tagu `<util.random.decimal[<#.#>].to[<#.#>]>`.

Ten wewnętrzny tag składa się z `<player>`, aby pobrać połączonego gracza, `<PlayerTag.health>`, aby pobrać wartość życia gracza, oraz `<ElementTag.div[<#.#>]>`, aby podzielić tę wartość <span class="parens">(w tym przypadku przez 2)</span>.

### Uwaga na przyszłość

W przyszłości, pisząc prawdziwe skrypty, oczywiście nie będziesz musiał po prostu zapamiętywać wszystkich dostępnych tagów. W [dalszej sekcji](/guides/basics/finding-tools) zobaczymy sposoby na znalezienie tagów potrzebnych do dowolnego pomysłu na skrypt. Dopóki nie dotrzemy do tej sekcji, ten przewodnik będzie pokazywał Ci wszelkie tagi potrzebne do ćwiczenia pomysłów przedstawionych na danej stronie.

### Wyjaśnienie pojęć

Na tej stronie użyto kilku słów specyficznych dla Denizen. W większości przypadków kontekst ich użycia powinien wystarczyć, aby dać Ci wyobrażenie o tym, co oznaczają... Ale na wszelki wypadek oto kilka wyjaśnień, aby upewnić się, że dobrze rozumiesz użyte słowa:

- **„Zwraca” (Returns)** – omawiając tagi, słowo to jest zazwyczaj używane w zdaniach typu „Tag `<PlayerTag.name>` zwraca imię gracza”, co oznacza, że tag ten po użyciu zostaje zastąpiony imieniem gracza. Zatem `- narrate "cześć <player.name>"` wyświetli `"cześć bob"`. Słowo „zwraca” w dowolnym zdaniu można zastąpić frazą „zostaje zastąpiony przez”, np. „Tag `<PlayerTag.name>` zostaje zastąpiony przez imię gracza”.
- **„Obiekt” (Object)** – to ogólny termin odnoszący się do pewnej wartości, zwykle używany w kontekście omawiania różnych *typów obiektów*. PlayerTag to jeden typ obiektu, NPCTag to inny. Podczas pracy nad skryptem każdy gracz jest obiektem, a obiekt ten jest typu PlayerTag. Każdy typ nazywa się „(coś)Tag”, aby wskazać, że jest to (coś) do użytku wewnątrz tagu. To sformułowanie jest używane nawet wtedy, gdy nie omawia się konkretnie tagów. Szerszy zestaw wszystkich obiektów w kontekście skryptowania jest czasami nazywany ObjectTags.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o tagach, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka Fallbacks](https://meta.denizenscript.com/Docs/Languages/tag%20fallbacks)
- [Dokumentacja języka ObjectTags](https://meta.denizenscript.com/Docs/Languages/ObjectTags)
- [Dokumentacja języka Unique vs generic objects](https://meta.denizenscript.com/Docs/Languages/Unique%20Objects%20vs%20Generic%20Objects)
- [Dokumentacja języka powiązana z ElementTag](https://meta.denizenscript.com/Docs/Languages/ElementTag)
- [Lista wszystkich typów obiektów](https://meta.denizenscript.com/Docs/ObjectTypes)
- [Lista wszystkich tagów](https://meta.denizenscript.com/Docs/Tags/)
