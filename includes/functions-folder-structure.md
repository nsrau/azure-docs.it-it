
Il codice per tutte le funzioni in un'app di funzione specifica si trova in una cartella radice che contiene un file di configurazione di host e nelle sottocartelle di uno o più. Ogni sottocartella contiene il codice per una funzione separata, come nell'esempio seguente:

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

Il file host.json contiene alcune configurazioni specifiche di runtime e si trova nella cartella radice dell'applicazione (funzione). Per informazioni sulle impostazioni disponibili, vedere il [host.json riferimento](../articles/azure-functions/functions-host-json.md).

Ogni funzione presenta una cartella che contiene uno o più file di codice, la configurazione di function.json e altre dipendenze.

