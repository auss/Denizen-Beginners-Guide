Jak sformatowane są przykładowe skrypty w tym przewodniku
---------------------------------------------------------

Kiedy zobaczysz przykładowy skrypt w dowolnym miejscu tego przewodnika, będzie on zakwalifikowany do jednego z trzech typów oznaczonych kolorami: Dobre, Wymagające zmiany lub Złe.

```eval_rst
.. contents:: Spis treści
    :local:
```

### Dobre skrypty

```dscript_green
my_good_script:
    type: task
    script:
    - narrate "Ten skrypt można po prostu wkleić!"
```

Dobre skrypty mają zieloną obwódkę. Skrypty te nadają się do wstawienia na serwer w obecnej formie i będą działać (choć niekoniecznie będą miały wielkie zastosowanie).

### Skrypty wymagające zmian

```dscript_blue
my_changeable_script:
    type: task
    script:
    - narrate "Będziesz musiał to uzupełnić"
    - wait (Wpisz tutaj swoje opóźnienie)
    - (tutaj wpisz więcej poleceń)
```

Skrypty, które są dobre, ale wymagają zmian do działania, mają niebieską obwódkę. Zazwyczaj prezentują one konkretną składnię lub zawierają tylko fragment skryptu. Jeśli chcesz taki wypróbować, będziesz musiał uzupełnić brakujące elementy przed załadowaniem go.

### Nieidealne skrypty

```dscript_yellow
my_imperfect_script:
    type: task
    script:
    - narrate "twoja flaga to <player.flag[waffle]>, chyba że <player.has_flag[waffle]> to fałsz, ojej"
```

Nieidealne skrypty mają żółtą obwódkę. Są one poprawne jako materiał do nauki, ale nie powinny być używane w takiej formie.

### Złe skrypty

```dscript_red
my_bad_script:
    type: tusk
    scrapt:
    - delete system32
    - narrote 'To nie zadziala""
```

Skrypty, które są złe i nie powinny być używane, mają czerwoną obwódkę. Zazwyczaj prezentują one powszechne błędy lub pułapki i są przedstawiane wyłącznie jako przykłady tego, czego **NIE** robić. Skrypty te mogą zawierać literówki, błędy, złe formatowanie, błędną logikę lub po prostu przestarzałą składnię.
