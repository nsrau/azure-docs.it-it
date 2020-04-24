---
title: Configurare la cache e l'arricchimento incrementale (anteprima)
titleSuffix: Azure Cognitive Search
description: Abilitare la memorizzazione nella cache e mantenere lo stato del contenuto arricchito per l'elaborazione controllata in un Skills cognitivo. Questa funzionalità è attualmente in anteprima pubblica.
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
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Come configurare la memorizzazione nella cache per l'arricchimento incrementale in Azure ricerca cognitiva

> [!IMPORTANT] 
> L'arricchimento incrementale è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

Questo articolo illustra come aggiungere la memorizzazione nella cache a una pipeline di arricchimento per poter modificare in modo incrementale i passaggi senza dover ricompilare ogni volta. Per impostazione predefinita, un insieme di competenze è senza stato e la modifica di qualsiasi parte della relativa composizione richiede una ripetizione completa dell'indicizzatore. Con l'arricchimento incrementale, l'indicizzatore è in grado di determinare quali parti dell'albero del documento devono essere aggiornate in base alle modifiche rilevate nelle definizioni di skillt o indicizzatore. L'output elaborato esistente viene mantenuto e riutilizzato laddove possibile. 

Il contenuto memorizzato nella cache viene inserito in archiviazione di Azure usando le informazioni dell'account fornite. Il contenitore, denominato `ms-az-search-indexercache-<alpha-numerc-string>`, viene creato quando si esegue l'indicizzatore. Deve essere considerato un componente interno gestito dal servizio di ricerca e non deve essere modificato.

Se non si ha familiarità con la configurazione degli indicizzatori, iniziare con l' [indicizzatore Panoramica](search-indexer-overview.md) e quindi continuare con [skillsets](cognitive-search-working-with-skillsets.md) per informazioni sulle pipeline di arricchimento. Per ulteriori informazioni sui concetti chiave, vedere [arricchimento incrementale](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Abilitare la memorizzazione nella cache su un indicizzatore esistente

Se si dispone di un indicizzatore esistente che dispone già di un livello di competenze, attenersi alla procedura descritta in questa sezione per aggiungere la memorizzazione nella cache. Come operazione monouso, sarà necessario reimpostare e rieseguire l'indicizzatore in modo completo prima che l'elaborazione incrementale possa essere applicata.

> [!TIP]
> Come modello di prova, è possibile eseguire la [Guida introduttiva](cognitive-search-quickstart-blob.md) di questo portale per creare gli oggetti necessari e quindi usare il post o il portale per effettuare gli aggiornamenti. Potrebbe essere necessario alleghi una risorsa Servizi cognitivi fatturabile. Quando si esegue l'indicizzatore più volte, l'allocazione giornaliera gratuita viene esaurita prima di poter completare tutti i passaggi.

### <a name="step-1-get-the-indexer-definition"></a>Passaggio 1: ottenere la definizione dell'indicizzatore

Iniziare con un indicizzatore esistente valido con i componenti seguenti: origine dati, competenze, indice. L'indicizzatore deve essere eseguibile. 

Usando un client API, creare una [richiesta Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer) per ottenere la configurazione corrente dell'indicizzatore. Quando si usa la versione dell'API di anteprima per ottenere l'indicizzatore, `cache` viene aggiunta una proprietà impostata su null alle definizioni.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Copiare la definizione dell'indicizzatore dalla risposta.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Passaggio 2: modificare la proprietà cache nella definizione dell'indicizzatore

Per impostazione predefinita `cache` , la proprietà è null. Usare un client API per impostare la configurazione della cache (il portale non supporta questo aggiornamento del particolato). 

Modificare l'oggetto cache per includere le proprietà obbligatorie e facoltative seguenti: 

+ `storageConnectionString` È obbligatorio e deve essere impostato su una stringa di connessione di archiviazione di Azure. 
+ La `enableReprocessing` proprietà booleana è facoltativa (`true` per impostazione predefinita) e indica che l'arricchimento incrementale è abilitato. Quando necessario, è possibile impostarlo su `false` per sospendere l'elaborazione incrementale mentre altre operazioni che richiedono un utilizzo intensivo di risorse, ad esempio l'indicizzazione di nuovi `true` documenti, sono in corso e quindi ne viene eseguito il capovolgimento a un momento successivo.

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

### <a name="step-3-reset-the-indexer"></a>Passaggio 3: reimpostare l'indicizzatore

Quando si configura l'arricchimento incrementale per gli indicizzatori esistenti, è necessario reimpostare l'indicizzatore per assicurarsi che tutti i documenti siano in uno stato coerente. Per questa attività è possibile usare il portale o un client API e l' [API REST di reimpostazione dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) .

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Passaggio 4: salvare la definizione aggiornata

[Aggiornare l'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) con una richiesta PUT, il corpo della richiesta deve contenere la definizione di indicizzatore aggiornata con la proprietà cache. Se si ottiene un 400, controllare la definizione dell'indicizzatore per verificare che tutti i requisiti siano soddisfatti (origine dati, competenze, indice).

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

Se si emette un'altra richiesta GET nell'indicizzatore, la risposta dal servizio includerà una `ID` proprietà nell'oggetto cache. La stringa alfanumerica viene aggiunta al nome del contenitore contenente tutti i risultati memorizzati nella cache e lo stato intermedio di ogni documento elaborato da questo indicizzatore. L'ID verrà usato per assegnare un nome univoco alla cache nell'archivio BLOB.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Passaggio 5: eseguire l'indicizzatore

Per eseguire l'indicizzatore, è possibile usare il portale o l'API. Nell'elenco indicizzatori del portale selezionare l'indicizzatore e fare clic su **Esegui**. Un vantaggio dell'uso del portale è che è possibile monitorare lo stato dell'indicizzatore, annotare la durata del processo e il numero di documenti elaborati. Le pagine del portale vengono aggiornate ogni pochi minuti.

In alternativa, è possibile usare REST per [eseguire l'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Dopo l'esecuzione dell'indicizzatore, è possibile trovare la cache nell'archivio BLOB di Azure. Il nome del contenitore è nel formato seguente:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Una reimpostazione e una riesecuzione dell'indicizzatore comportano una ricompilazione completa, in modo che il contenuto possa essere memorizzato nella cache. Tutte le arricchimenti cognitivi verranno rieseguite in tutti i documenti.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Passaggio 6: modificare un valore di competenze e confermare l'arricchimento incrementale

Per modificare un valore di competenze, è possibile usare il portale o l'API. Se, ad esempio, si utilizza la traduzione del testo, una semplice modifica inline `es` da a o da `en` un'altra lingua è sufficiente per il test del modello di prova di arricchimento incrementale.

Eseguire di nuovo l'indicizzatore. Vengono aggiornate solo le parti di un albero di documenti arricchito. Se è stata usata la [Guida introduttiva del portale](cognitive-search-quickstart-blob.md) come proof-of-Concept, modificando l'esperienza di traduzione del testo in "es", si noterà che vengono aggiornati solo 8 documenti anziché i 14 originali. I file di immagine non interessati dal processo di conversione vengono riutilizzati dalla cache.

## <a name="enable-caching-on-new-indexers"></a>Abilitare la memorizzazione nella cache per i nuovi indicizzatori

Per configurare l'arricchimento incrementale per un nuovo indicizzatore, è sufficiente includere la `cache` proprietà nel payload della definizione dell'indicizzatore quando si chiama [Crea indicizzatore (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer). 


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

## <a name="checking-for-cached-output"></a>Verifica dell'output memorizzato nella cache

La cache viene creata, utilizzata e gestita dall'indicizzatore e il relativo contenuto non viene rappresentato in un formato leggibile. Il modo migliore per determinare se la cache viene utilizzata consiste nell'eseguire l'indicizzatore e confrontare le metriche before-and-after per i conteggi del tempo di esecuzione e dei documenti. 

Si supponga, ad esempio, un insieme di competenze che inizia con l'analisi dell'immagine e il riconoscimento ottico dei caratteri (OCR) dei documenti analizzati, seguiti dall'analisi downstream del testo risultante. Se si modifica una competenza del testo downstream, l'indicizzatore può recuperare tutte le immagini elaborate in precedenza e il contenuto OCR dalla cache, aggiornando ed elaborando solo le modifiche relative al testo indicate dalle modifiche. È possibile prevedere un minor numero di documenti nel conteggio dei documenti (ad esempio, 8/8 rispetto a 14/14 nell'esecuzione originale), tempi di esecuzione più brevi e minori costi per la fattura.

## <a name="working-with-the-cache"></a>Uso della cache

Quando la cache è operativa, gli indicizzatori controllano la cache ogni volta che viene chiamato l' [indicizzatore](https://docs.microsoft.com/rest/api/searchservice/run-indexer) , per vedere quali parti dell'output esistente possono essere utilizzate. 

La tabella seguente riepiloga il modo in cui le varie API sono correlate alla cache:

| API           | Effetto della cache     |
|---------------|------------------|
| [Crea indicizzatore (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Crea ed esegue un indicizzatore al primo utilizzo, inclusa la creazione di una cache se la definizione dell'indicizzatore lo specifica. |
| [esecuzione di un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Esegue una pipeline di arricchimento su richiesta. Questa API legge dalla cache se esiste o crea una cache se si aggiunge la memorizzazione nella cache a una definizione di indicizzatore aggiornata. Quando si esegue un indicizzatore in cui è abilitata la memorizzazione nella cache, l'indicizzatore omette i passaggi se è possibile usare l'output memorizzato nella cache. È possibile usare la versione dell'API disponibile a livello generale o in anteprima.|
| [Reimpostare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Cancella l'indicizzatore delle informazioni di indicizzazione incrementale. La successiva esecuzione dell'indicizzatore (su richiesta o pianificazione) prevede una rielaborazione completa da zero, inclusa la ripetizione dell'esecuzione di tutte le competenze e la ricompilazione della cache. Dal punto di vista funzionale, equivale a eliminare l'indicizzatore e a ricrearlo. È possibile usare la versione dell'API disponibile a livello generale o in anteprima.|
| [Reimposta le competenze (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Specifica le competenze da rieseguire durante la successiva esecuzione dell'indicizzatore, anche se non sono state modificate competenze. La cache viene aggiornata di conseguenza. Gli output, ad esempio un archivio informazioni o un indice di ricerca, vengono aggiornati usando i dati riutilizzabili della cache e il nuovo contenuto in base alle competenze aggiornate. |

Per ulteriori informazioni sul controllo di ciò che accade alla cache, vedere [gestione della cache](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Passaggi successivi

L'arricchimento incrementale è applicabile agli indicizzatori che contengono skillsets. Come passaggio successivo, visitare la documentazione relativa alle competenze per comprendere i concetti e la composizione. 

Inoltre, una volta abilitata la cache, è opportuno conoscere i parametri e le API che determinano la memorizzazione nella cache, inclusa la modalità di sostituzione o di forzare comportamenti specifici. Per altre informazioni, vedere i collegamenti seguenti:

+ [Concetti e composizione di competenze](cognitive-search-working-with-skillsets.md)
+ [Come creare un oggetto di competenze](cognitive-search-defining-skillset.md)
+ [Introduzione all'arricchimento e alla memorizzazione nella cache incrementali](cognitive-search-incremental-indexing-conceptual.md)
