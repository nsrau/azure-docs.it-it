
Il codice per tutte le funzioni in un'app per le funzioni specifica si trova in una cartella radice che contiene un file di configurazione host e una o più sottocartelle. Ogni sottocartella contiene il codice di una funzione distinta, come nell'esempio seguente:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Il file host.json, che contiene alcune configurazioni specifiche di runtime, si trova nella cartella radice dell'app per le funzioni. Per informazioni sulle impostazioni disponibili, vedere le [informazioni di riferimento per host.json](../articles/azure-functions/functions-host-json.md).

Ogni funzione ha una cartella che contiene uno o più file di codice, la configurazione function.json e altre dipendenze.

