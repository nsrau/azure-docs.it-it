Quando l'host di Funzioni viene eseguito in locale, scrive i log nel percorso seguente:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

In Windows `<DefaultTempDirectory>` è il primo valore trovato delle variabili di ambiente TMP, TEMP, USERPROFILE o la directory di Windows.
In MacOS o Linux `<DefaultTempDirectory>` è la variabile di ambiente TMPDIR.

[!Note]
Quando l'host di Funzioni viene avviato, verrà sovrascritta la struttura di file esistente nella directory.