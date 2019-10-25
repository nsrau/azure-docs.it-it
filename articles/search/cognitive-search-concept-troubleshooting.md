---
title: Suggerimenti per la risoluzione dei problemi relativi all'arricchimento di intelligenza artificiale
titleSuffix: Azure Cognitive Search
description: Suggerimenti e risoluzione dei problemi per la configurazione di pipeline di arricchimento AI in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 485dd47e035f03a8e20ded4c8a424f1658f5246a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787709"
---
# <a name="troubleshooting-tips-for-ai-enrichment-in-azure-cognitive-search"></a>Suggerimenti per la risoluzione dei problemi relativi all'arricchimento di intelligenza artificiale in Azure ricerca cognitiva

Questo articolo contiene un elenco di suggerimenti e consigli per proseguire con l'introduzione delle funzionalità di arricchimento di intelligenza artificiale in Azure ricerca cognitiva. 

Se non è già stato fatto, eseguire l' [esercitazione: informazioni su come chiamare le API di arricchimento di intelligenza artificiale](cognitive-search-quickstart-blob.md) per la pratica nell'applicazione di arricchimenti di intelligenza artificiale a un'origine dati BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Suggerimento 1: avviare un set di dati di piccole dimensioni
Il modo migliore per rilevare rapidamente i problemi è aumentarne la velocità di risoluzione. Il modo migliore per ridurre il tempo di indicizzazione è diminuire il numero di documenti da indicizzare. 

Iniziare creando un'origine dati con una vasta gamma di documenti/record. L'esempio di documento deve essere una buona rappresentazione della varietà di documenti che verranno indicizzati. 

Analizzare l'esempio di documento tramite la pipeline end-to-end e verificare che i risultati soddisfino le esigenze. Quando si è soddisfatti dei risultati, è possibile aggiungere più file all'origine dati.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Suggerimento 2: verificare che le credenziali dell'origine dati siano corrette
La connessione all'origine dati non viene convalidata fino a quando non viene definito un indicizzatore che la utilizza. Se vengono visualizzati errori che specificano che l'indicizzatore non ha accesso ai dati, assicurarsi che:
- La stringa di connessione sia corretta. In particolare quando si creano token SAS, assicurarsi di usare il formato previsto da Azure ricerca cognitiva. Vedere [Come specificare la sezione delle credenziali](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) per informazioni sui diversi formati supportati.
- Il nome del contenitore nell'indicizzatore sia corretto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Suggerimento 3: vedere quali aspetti funzionano anche se sono presenti errori
In alcuni casi un errore di piccole dimensioni arresta un indicizzatore nelle proprie tracce. Che sia appropriato prevedere di risolvere i problemi uno alla volta. Tuttavia, è possibile ignorare un determinato tipo di errore, consentendo all'indicizzatore di procedere in modo da vedere i flussi attualmente attivi.

In tal caso, è possibile indicare all'indicizzatore di ignorare gli errori. Eseguire questa operazione impostando *maxFailedItems* e *maxFailedItemsPerBatch* su -1 come parte della definizione dell'indicizzatore.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Suggerimento 4: analizzare i modo approfondito gli aspetti non evidenti dei documenti 
I documenti arricchiti sono strutture temporanee create durante l'arricchimento e quindi eliminate al termine dell’elaborazione.

Per acquisire uno snapshot del documento arricchito creato durante l'indicizzazione, aggiungere un campo denominato ```enriched``` all'indice. L'indicizzatore esegue automaticamente il dump nel campo di una rappresentazione stringa di tutti gli arricchimenti per il documento.

Il campo ```enriched``` conterrà una stringa che è una rappresentazione logica del documento in memoria arricchito in JSON.  Il valore del campo è tuttavia un documento JSON valido. Per le virgolette vengono usati caratteri di escape, quindi sarà necessario sostituire `\"` con `"` per visualizzare il documento in formato JSON. 

Il campo arricchito è destinato esclusivamente al debugging, per comprendere meglio la forma logica del contenuto in base alla quale vengono valutate le espressioni. Ai fini dell’indicizzazione, non dipendere da questo campo.

Aggiungere un campo ```enriched``` come parte della definizione di indice per scopi di debug:

#### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Suggerimento 5: impossibile visualizzare il contenuto previsto

Il contenuto mancante potrebbe essere il risultato di documenti eliminati durante l'indicizzazione. I livelli gratuiti e di base hanno un limite basso per quanto riguarda le dimensioni del documento. Qualsiasi file che superi il limite viene eliminato durante l'indicizzazione. È possibile controllare i documenti eliminati nel portale di Azure. Nel dashboard del servizio di ricerca, fare doppio clic sul riquadro degli indicizzatori. Esaminare la percentuale di esiti positivi dei documenti indicizzati. Se non è al 100%, è possibile selezionare la percentuale per ottenere maggiori dettagli. 

Se il problema è correlato alle dimensioni del file, è possibile che venga visualizzato un errore simile al seguente: "il BLOB \<nome file >" ha le dimensioni di \<file > byte, che supera le dimensioni massime per l'estrazione del documento per il livello di servizio corrente. " Per ulteriori informazioni sui limiti degli indicizzatori, vedere i [Limiti del servizio](search-limits-quotas-capacity.md).

Una secondo motivo per cui il contenuto non viene visualizzato potrebbe risiedere negli errori di mapping di input/output correlati. Ad esempio, il nome di destinazione di output è "Persone" ma il nome del campo indice è in minuscolo, "persone". Il sistema potrebbe restituire messaggi di operazione riuscita 201 per l'intera pipeline, pertanto si potrebbe ritenere che l'indicizzazione ha avuto esito positivo, quando in realtà un campo è vuoto. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Suggerimento 6: estendere l'elaborazione oltre il tempo di esecuzione massimo (finestra di 24 ore)

L’analisi delle immagini è complessa a livello computazionale anche per i casi semplici, pertanto, quando le immagini hanno dimensioni particolarmente grandi o sono particolarmente complesse, i tempi di elaborazione possono superare il tempo massimo consentito. 

Il tempo di esecuzione massimo varia in base al livello: alcuni minuti per il livello gratuito, indicizzazione di 24 ore per i livelli fatturabili. Se l'elaborazione non viene completata entro un periodo di 24 ore per l'elaborazione a richiesta, passare a una pianificazione per consentire all'indicizzatore di riprendere l'elaborazione dove era stata interrotta. 

Per gli indicizzatori pianificati, l'indicizzazione dell'ultimo documento valido noto viene ripresa nei termini previsti. Tramite una pianificazione ricorrente, l'indicizzatore può concentrarsi sul backlog immagine per svariate ore o giorni, fino a quando è completa l’elaborazione di tutte le immagini. Per altre informazioni sulla sintassi Schedule, vedere [Step 3: Create-an-Indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) o vedere [How to Schedule Indexers for Azure ricerca cognitiva](search-howto-schedule-indexers.md).

> [!NOTE]
> Se un indicizzatore è impostato su una determinata pianificazione ma ripetutamente ha esito negativo sullo stesso documento più volte ogni volta che viene eseguito, l'indicizzatore inizierà a essere eseguito in un intervallo meno frequente (fino al massimo almeno una volta ogni 24 ore) finché non apporterà correttamente lo stato di avanzamento in.  Se si ritiene di aver risolto il problema che causava il blocco dell'indicizzatore in un determinato momento, è possibile eseguire un'esecuzione su richiesta dell'indicizzatore e, se l'operazione ha esito positivo, l'indicizzatore tornerà nuovamente all'intervallo di pianificazione impostato.

Per l’indicizzazione basata sul portale (come descritto nella Guida introduttiva), selezionare l’opzione dell'indicizzatore "Esegui una volta" comporta la limitazione dell’elaborazione a 1 ora (`"maxRunTime": "PT1H"`). È possibile estendere la finestra di elaborazione a un valore maggiore.

## <a name="tip-7-increase-indexing-throughput"></a>Suggerimento 7: aumentare la velocità effettiva di indicizzazione

Per [indicizzazione parallela](search-howto-large-index.md), inserire i dati in più contenitori o più cartelle virtuali all'interno dello stesso contenitore. Creare quindi più coppie di origine dati e di indicizzatori. Tutti gli indicizzatori possono utilizzare lo stesso insieme di competenze e scrivere nello stesso indice di ricerca di destinazione, in modo che l'app per la ricerca non debba necessariamente essere a conoscenza di tale partizionamento.
Per altre informazioni, vedere [Indicizzazione di set di dati di grandi dimensioni](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Vedi anche
+ [Guida introduttiva: creare una pipeline di arricchimento di intelligenza artificiale nel portale](cognitive-search-quickstart-blob.md)
+ [Esercitazione: informazioni sulle API REST per l'arricchimento di intelligenza artificiale](cognitive-search-tutorial-blob.md)
+ [Specificare le credenziali dell'origine dati](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indicizzazione di set di dati di grandi dimensioni](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Come eseguire la mappatura dei campi arricchiti a un indice](cognitive-search-output-field-mapping.md)
