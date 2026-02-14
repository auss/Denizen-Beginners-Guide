Rozwiązywanie napotkanych problemów
------------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

### Gdy Twój skrypt nie działa i nie wiesz dlaczego

Napisałeś skrypt, załadowałeś go i... nic! Albo w ogóle nie działa, albo robi coś innego, niż zamierzałeś. Co teraz?

### Konsola debugowania

Jedną z najważniejszych i najbardziej cenionych funkcji Denizen jest *pełne, automatyczne debugowanie*! Wszystko, co robią Twoje skrypty, trafia do logów debugowania <span class="parens">(dopóki nie wyłączysz debugowania dla danego skryptu za pomocą klucza `debug: false`)</span>. To magiczne narzędzie, które pomoże Ci znaleźć problem znacznie szybciej, niż kiedykolwiek udałoby Ci się to bez niego.

![](images/debugerror.png)

### Jak czytać debug?

Konsola debugowania wyświetli każde uruchomione polecenie w kolejności ich wykonywania. Pokaże treść polecenia, sposób interpretacji argumentów, a czasem także wynik polecenia <span class="parens">(jeśli dotyczy)</span>. Wszelkie przetworzone tagi zostaną również wyświetlone obok poleceń, z których pochodzą. Zestawy poleceń działających razem są identyfikowane przez ich kolejkę (queue) <span class="parens">(która ma unikalną nazwę i wzór kolorów, aby wizualnie ją odróżnić)</span>.

#### Trudniejszy sposób

W niektórych przypadkach, gdy sprawy nie idą dokładnie tak, jak się spodziewałeś, możesz zostać zmuszony do czytania poleceń linia po linii, próbując znaleźć moment, w którym coś odbiegło od Twoich oczekiwań <span class="parens">(na przykład: możesz odkryć, że tag zwrócił „zombie_pigman”, podczas gdy myślałeś, że zwróci „pigzombie”, przez co logika zależna od tej nazwy nie zadziałała i wszystko dalej się posypało)</span>.

#### Błędy

W wielu przypadkach będziesz mieć szczęście i problem zostanie wychwycony przez system – w takim punkcie zobaczysz wyraźny czerwony tekst krzyczący **„ERROR!”** i wyjaśniający, co zostało wykryte.

W niektórych przypadkach komunikat o błędzie powie Ci dokładnie, co poszło nie tak. Na przykład, jeśli kiedykolwiek przez pomyłkę wpiszesz notację z dokumentacji zamiast prawdziwego tagu, np. `<PlayerTag.name>`, podczas gdy potrzebowałeś `<player.name>`, komunikat będzie dość jasny: `Notacja 'ObjectTag' służy do celów dokumentacyjnych i nie należy jej używać dosłownie. Należy zamiast niej wstawić rzeczywisty obiekt.`

W innych sytuacjach komunikat o błędzie zidentyfikuje ogólny problem, ale niekoniecznie powie dokładnie, gdzie popełniłeś błąd lub co musisz zrobić. Na przykład, jeśli zapomniałeś cudzysłowu wokół wielowyrazowego argumentu polecenia, komunikat o błędzie może brzmieć po prostu kilka razy z rzędu: `(jakieś słowo) is an unknown argument!`. Powodem tego jest fakt, że silnik skryptowy nie potrafi odgadnąć Twoich *intencji*, a jedynie stwierdzić, co jest nie tak z danymi wejściowymi, które *podałeś* <span class="parens">(zauważ jednak, że silnik często przynajmniej *próbuje* zgadywać w przypadku typowych błędów, jak ten w przykładzie – i w rzeczywistości w nowoczesnych wersjach Denizen ten konkretny komunikat zapyta, czy może zapomniałeś o cudzysłowie)</span>. W takich przypadkach od Ciebie zależy ustalenie, co dokładnie musisz zmienić. Ogólnie rzecz biorąc, błąd niemal zawsze znajduje się bardzo blisko <span class="parens">(pod względem numerów linii)</span> komunikatu, który pojawił się w logach jako **pierwszy**, i prawdopodobnie jest ściśle powiązany z tym, co ten komunikat wskazuje. Pamiętaj również, że to, co zostało oddebugowane bezpośrednio *przed* komunikatem o błędzie, zazwyczaj jest tym, co wywołało błąd, a nie to, co jest *po nim*. Ale co, jeśli po prostu nie potrafisz tego rozgryźć?

### Słoik pełen oczu

Czasami problem jest zbyt mylący, by go zrozumieć samodzielnie, innym razem po prostu potrzebujesz innej pary oczu, która spojrzy na Twój skrypt i dostrzeże coś oczywistego, co Tobie umknęło.

W takich przypadkach najlepszym miejscem jest [nasz Discord](https://discord.gg/Q6pZGSR). Na Discordzie znajduje się otwarty kanał wsparcia (`#denizen`), gdzie możesz poprosić o pomoc innych użytkowników Denizen. Znajdziesz tam również kilka botów, które potrafią przeszukiwać dokumentację (sprawdź `!help`), i wiele więcej na kanale do testowania botów (`#bot-spam`).

[![](images/thediscord.png)](https://discord.gg/Q6pZGSR)

Prosząc o pomoc ze skryptem, najlepiej jest podać link do swojego skryptu za pomocą [serwisu do udostępniania skryptów (paste service)](https://paste.denizenscript.com/) oraz kopię logu debugowania. Log możesz nagrać, wpisując w grze `/denizen debug -r`, następnie uruchamiając skrypt i wpisując `/denizen submit`. Wygeneruje to link do nagranej kopii logów debugowania dla Twojego skryptu, który możesz zamieścić obok linku do skryptu, aby pomocnicy mogli go przeanalizować.

Dołączając do Discorda, zanim cokolwiek napiszesz, koniecznie przeczytaj zasady (kanał `#rules`), aby nie zacząć znajomości od złej strony. Discord jest ogólnie dość spokojnym i przyjaznym miejscem, ale zasady będą egzekwowane <span class="parens">(oczywiście z umiarem – zwykły wypadek nie spowoduje bana ani niczego takiego, nie martw się. Większość przypadkowych naruszeń zasad skończy się po prostu ostrzeżeniem... nic więcej się nie wydarzy, chyba że będziesz wielokrotnie łamać te same zasady)</span>.

Bądź jednak przygotowany na potencjalnie „szorstkie” wsparcie przy naprawianiu szczególnie niechlujnych skryptów. Jeśli opublikujesz skrypt pełen błędów, prawdopodobnie zobaczysz odpowiedź pomocnika z długą, groźnie wyglądającą listą każdego problemu i instrukcją, jak go naprawić. Nie przywiązuj zbyt dużej wagi do tonu wypowiedzi <span class="parens">(pamiętaj, że rozmowy tekstowe często tracą zamierzony ton i mają tendencję do brzmienia bardziej negatywnie, niż było to zamierzone)</span> – tworzymy te długie analizy, ponieważ chcemy, abyś się uczył i tworzył najlepsze możliwe skrypty. Gdybyśmy byli na Ciebie naprawdę zli, prawdopodobnie w ogóle byśmy nie odpowiedzieli.

### Powiązana dokumentacja techniczna

Jeśli chcesz dowiedzieć się znacznie więcej o rozwiązywaniu problemów, oto kilka przewodników technicznych, które możesz wziąć pod uwagę...

Uwaga: większość użytkowników, zwłaszcza tych uczących się Denizen po raz pierwszy, powinna po prostu przejść do następnej strony przewodnika. Referencje te mogą być interesujące do późniejszego powrotu, gdy już nauczysz się Denizen w stopniu, jaki przewiduje ten przewodnik.

- [Dokumentacja języka polecenia debug](https://meta.denizenscript.com/Docs/Languages/denizen%20debug%20command)
- [Dokumentacja języka polecenia submit](https://meta.denizenscript.com/Docs/Languages/denizen%20submit%20command)
