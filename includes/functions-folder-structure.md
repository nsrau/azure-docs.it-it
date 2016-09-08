
Il codice per tutte le funzioni in una determinata app per le funzioni si trova in una cartella radice contenente un file di configurazione host e una o più sottocartelle, ciascuna delle quali contiene un codice per una funzione separata, come nell'esempio seguente

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

Il file *host.json* contiene alcune configurazioni specifiche del runtime e si trova nella cartella radice dell'app per le funzioni. Per informazioni sulle impostazioni disponibili vedere [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) nella wiki del repository WebJobs.Script.

Ogni funzione ha una cartella che contiene uno o più file di codice, la configurazione function.json e altre dipendenze.

<!---HONumber=AcomDC_0824_2016-->