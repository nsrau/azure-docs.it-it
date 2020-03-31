---
title: Configurare la cache e l'arricchimento incrementale (anteprima)Configure cache and incremental enrichment (preview)
titleSuffix: Azure Cognitive Search
description: Abilitare la memorizzazione nella cache e conservare lo stato del contenuto arricchito per l'elaborazione controllata in un set di competenze cognitive. Questa funzionalità è attualmente in anteprima pubblica.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989553"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Come configurare la memorizzazione nella cache per l'arricchimento incrementale in Ricerca cognitiva di AzureHow to configure caching for incremental enrichment in Azure Cognitive Search

> [!IMPORTANT] 
> L'arricchimento incrementale è attualmente in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

In questo articolo viene illustrato come aggiungere la memorizzazione nella cache a una pipeline di arricchimento in modo che è possibile modificare in modo incrementale i passaggi senza dover ricompilare ogni volta. Per impostazione predefinita, un set di competenze è senza stato e la modifica di qualsiasi parte della composizione richiede una ripetizione completa dell'indicizzatore. Con l'arricchimento incrementale, l'indicizzatore può determinare quali parti dell'albero del documento devono essere aggiornate in base alle modifiche rilevate nelle definizioni del set di competenze o dell'indicizzatore. L'output elaborato esistente viene conservato e riutilizzato ove possibile. 

Il contenuto memorizzato nella cache viene inserito in Archiviazione di Azure usando le informazioni sull'account fornite. Il contenitore, denominato `ms-az-search-indexercache-<alpha-numerc-string>`, viene creato quando si esegue l'indicizzatore. Deve essere considerato un componente interno gestito dal servizio di ricerca e non deve essere modificato.

Se non si ha familiarità con la configurazione degli indicizzatori, iniziare con la [panoramica dell'indicizzatore](search-indexer-overview.md) e quindi continuare con i set di [competenze](cognitive-search-working-with-skillsets.md) per informazioni sulle pipeline di arricchimento. Per ulteriori informazioni sui concetti chiave, vedere [Arricchimento incrementale](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Abilitare la memorizzazione nella cache in un indicizzatore esistenteEnable caching on an existing indexer

Se si dispone di un indicizzatore esistente che dispone già di un set di competenze, seguire i passaggi in questa sezione per aggiungere la memorizzazione nella cache. Come operazione una tantera, sarà necessario reimpostare ed eseguire nuovamente l'indicizzatore per intero prima che l'elaborazione incrementale possa avere effetto.

> [!TIP]
> Come proof-of-concept, è possibile eseguire questa [guida introduttiva](cognitive-search-quickstart-blob.md) del portale per creare gli oggetti necessari e quindi utilizzare Postman o il portale per effettuare gli aggiornamenti. È possibile collegare una risorsa servizi cognitivi fatturabile. L'esecuzione dell'indicizzatore più volte esaurirà l'allocazione giornaliera gratuita prima di poter completare tutti i passaggi.

### <a name="step-1-get-the-indexer-definition"></a>Passaggio 1: Ottenere la definizione dell'indicizzatoreStep 1: Get the indexer definition

Iniziare con un indicizzatore valido esistente con i componenti seguenti: origine dati, set di competenze, indice. L'indicizzatore deve essere eseguibile. 

Utilizzando un client API, costruire una [richiesta dell'indicizzatore GET](https://docs.microsoft.com/rest/api/searchservice/get-indexer) per ottenere la configurazione corrente dell'indicizzatore. Quando si utilizza la versione dell'API di `cache` anteprima per l'indicizzatore GET, una proprietà impostata su null viene aggiunta alle definizioni.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Copiare la definizione dell'indicizzatore dalla risposta.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Passaggio 2: Modificare la proprietà della cache nella definizione dell'indicizzatoreStep 2: Modify the cache property in the indexer definition

Per impostazione predefinita, la `cache` proprietà è null. Utilizzare un client API per impostare la configurazione della cache (il portale non supporta questo aggiornamento del particolato). 

Modificare l'oggetto cache per includere le seguenti proprietà obbligatorie e facoltative: 

+ L'operazione `storageConnectionString` è obbligatoria e deve essere impostata su una stringa di connessione di Archiviazione di Azure.The is required, and it must be set to an Azure storage connection string. 
+ La `enableReprocessing` proprietà booleana`true` è facoltativa ( per impostazione predefinita) e indica che l'arricchimento incrementale è abilitato. Quando necessario, è possibile `false` impostarlo su di sospendere l'elaborazione incrementale mentre sono in corso `true` altre operazioni che richiedono un uso intensivo delle risorse, ad esempio l'indicizzazione di nuovi documenti, e quindi capovolgerla di nuovo in un secondo momento.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Passaggio 3: Reimpostare l'indicizzatoreStep 3: Reset the indexer

Quando si imposta l'arricchimento incrementale per gli indicizzatori esistenti per garantire che tutti i documenti siano in uno stato coerente, è necessario reimpostare l'indicizzatore. È possibile usare il portale o un client API e [l'API REST Reimposta indicizzatore](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) per questa attività.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Passaggio 4: Salvare la definizione aggiornataStep 4: Save the updated definition

[Aggiornare l'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) con una richiesta PUT, il corpo della richiesta deve contenere la definizione dell'indicizzatore aggiornata con la proprietà cache. Se si ottiene un 400, controllare la definizione dell'indicizzatore per assicurarsi che tutti i requisiti siano soddisfatti (origine dati, set di competenze, indice).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Se ora si invia un'altra richiesta GET all'indicizzatore, la risposta dal servizio includerà una `ID` proprietà nell'oggetto cache. La stringa alfanumerica viene aggiunta al nome del contenitore contenente tutti i risultati memorizzati nella cache e lo stato intermedio di ogni documento elaborato da questo indicizzatore. L'ID verrà usato per denominare in modo univoco la cache nell'archivio BLOB.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Passaggio 5: Eseguire l'indicizzatoreStep 5: Run the indexer

Per eseguire l'indicizzatore, è possibile usare il portale o l'API. Nell'elenco indicizzatori del portale selezionare l'indicizzatore e fare clic su **Esegui**. Uno dei vantaggi dell'utilizzo del portale è la possibile monitoraggio dello stato dell'indicizzatore, prendere nota della durata del processo e del numero di documenti elaborati. Le pagine del portale vengono aggiornate ogni pochi minuti.

In alternativa, è possibile usare REST per [eseguire l'indicizzatore:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Dopo l'esecuzione dell'indicizzatore, è possibile trovare la cache nell'archiviazione BLOB di Azure.After the indexer runs, you can find the cache in Azure Blob storage. Il nome del contenitore è nel seguente formato:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Una reimpostazione e la riesecuzione dell'indicizzatore comporta una ricompilazione completa in modo che il contenuto possa essere memorizzato nella cache. Tutti gli arricchimenti cognitivi saranno rieseguiti su tutti i documenti.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Passaggio 6: Modificare un set di competenze e confermare l'arricchimento incrementaleStep 6: Modify a skillset and confirm incremental enrichment

Per modificare un set di competenze, è possibile utilizzare il portale o l'API. Ad esempio, se si utilizza la traduzione `en` di `es` testo, una semplice modifica in linea da a o un'altra lingua è sufficiente per il test proof-of-concept dell'arricchimento incrementale.

Eseguire nuovamente l'indicizzatore. Vengono aggiornate solo le parti di un albero di documenti arricchito. Se hai usato la [guida introduttiva](cognitive-search-quickstart-blob.md) del portale come proof-of-concept, modificando la competenza di traduzione del testo in "es", noterai che solo 8 documenti vengono aggiornati invece dei 14 originali. I file di immagine non interessati dal processo di traduzione vengono riutilizzati dalla cache.

## <a name="enable-caching-on-new-indexers"></a>Abilitare la memorizzazione nella cache nei nuovi indicizzatoriEnable caching on new indexers

Per impostare l'arricchimento incrementale per un nuovo `cache` indicizzatore, è necessario includere la proprietà nel payload della definizione dell'indicizzatore quando si chiama [Crea indicizzatore (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Controllo dell'output memorizzato nella cache

La cache viene creata, utilizzata e gestita dall'indicizzatore e il relativo contenuto non è rappresentato in un formato leggibile. Il modo migliore per determinare se la cache viene utilizzata consiste nell'eseguire l'indicizzatore e confrontare le metriche prima e dopo per il tempo di esecuzione e il conteggio dei documenti. 

Si supponga, ad esempio, di un set di competenze che inizia con l'analisi delle immagini e il riconoscimento ottico dei caratteri (OCR) dei documenti digitalizzati, seguito dall'analisi a valle del testo risultante. Se si modifica una competenza di testo a valle, l'indicizzatore può recuperare tutto l'immagine elaborata in precedenza e il contenuto OCR dalla cache, aggiornando ed elaborando solo le modifiche relative al testo indicate dalle modifiche. È possibile prevedere un numero inferiore di documenti nel conteggio dei documenti (ad esempio 8/8 rispetto a 14/14 nell'esecuzione originale), tempi di esecuzione più brevi e meno addebiti sulla fattura.

## <a name="working-with-the-cache"></a>Utilizzo della cache

Una volta che la cache è operativa, gli indicizzatori controllano la cache ogni volta che viene chiamato [l'indicizzatore di](https://docs.microsoft.com/rest/api/searchservice/run-indexer) esecuzione, per vedere quali parti dell'output esistente possono essere utilizzate. 

Nella tabella seguente viene riepilogato il modo in cui le varie API sono correlate alla cache:

| API           | Impatto della cache     |
|---------------|------------------|
| [Crea indicizzatore (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Crea ed esegue un indicizzatore al primo utilizzo, inclusa la creazione di una cache se la definizione dell'indicizzatore la specifica. |
| [esecuzione di un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Esegue una pipeline di arricchimento su richiesta. Questa API legge dalla cache se esiste o crea una cache se è stata aggiunta la memorizzazione nella cache a una definizione aggiornata dell'indicizzatore. Quando si esegue un indicizzatore con la memorizzazione nella cache abilitata, l'indicizzatore omette i passaggi se è possibile usare l'output memorizzato nella cache. È possibile usare la versione API disponibile o di anteprima di questa API.|
| [Reimpostare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Cancella l'indicizzatore di tutte le informazioni di indicizzazione incrementali. L'esecuzione successiva dell'indicizzatore (su richiesta o pianificazione) è la rielaborazione completa da zero, inclusa la riesecuzione di tutte le competenze e la ricostruzione della cache. È funzionalmente equivalente all'eliminazione dell'indicizzatore e alla sua ricreazione. È possibile usare la versione API disponibile o di anteprima di questa API.|
| [Reimposta competenze (2019-05-06-Anteprima)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Specifica le competenze da eseguire nuovamente nella corsa dell'indicizzatore successivo, anche se non sono state modificate le competenze. La cache viene aggiornata di conseguenza. Gli output, ad esempio un archivio informazioni o un indice di ricerca, vengono aggiornati utilizzando dati riutilizzabili dalla cache più nuovo contenuto per ogni competenza aggiornata. |

Per ulteriori informazioni sul controllo di ciò che accade alla cache, vedere [Gestione della cache](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Passaggi successivi

L'arricchimento incrementale è applicabile agli indicizzatori che contengono set di competenze. Come passaggio successivo, consulta la documentazione del set di competenze per comprendere i concetti e la composizione. 

Inoltre, una volta abilitata la cache, è consigliabile conoscere i parametri e le API che determinano la memorizzazione nella cache, inclusa la modalità di override o forzare particolari comportamenti. Per altre informazioni, vedere i collegamenti seguenti:

+ [Concetti e composizione delle competenze](cognitive-search-working-with-skillsets.md)
+ [Come creare un set di competenze](cognitive-search-defining-skillset.md)
+ [Introduzione all'arricchimento incrementale e alla memorizzazione nella cache](cognitive-search-incremental-indexing-conceptual.md)
