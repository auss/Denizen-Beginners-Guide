Struktura projektu Denizen
---------------------------

```eval_rst
.. contents:: Spis treści
    :local:
```

**Denizen** to wtyczka dla serwerów Minecraft, która analizuje i interpretuje język skryptowy zwany **DenizenScript**. Najpopularniejszą implementacją Denizen jest ta dla Spigota – to właśnie ten plik jar prawdopodobnie wielokrotnie pobierałeś. W momencie pisania tego tekstu jest to *jedyna* utrzymywana implementacja.

Każda implementacja Denizen zależy od **DenizenCore**, czyli głównego zestawu narzędzi, który napędza każdą funkcję języka. Zawiera on całą zawartość, która nie jest specyficzna dla konkretnej implementacji, taką jak podstawowe operacje na ElementTag czy kontenery skryptów danych.

Wsparcie dla innych wtyczek w skryptach Denizen zapewnia **Depenizen**. Wykorzystuje on narzędzia dostarczane zarówno przez DenizenCore, jak i Denizen, do interakcji z zewnętrznymi API. Podobnie działa **dDiscordBot**, choć skupia się on na jednym konkretnym API <span class="parens">(Discord)</span>.

O ile nie planujesz pracy nad podstawowymi funkcjami języka, większość Twojego kodu będzie wyglądać podobnie do istniejących już rozwiązań – we wspomnianych projektach znajdziesz mnóstwo przykładów dla każdego typu wkładu.
