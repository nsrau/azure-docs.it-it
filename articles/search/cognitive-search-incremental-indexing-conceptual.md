---
title: Arricchimento incrementale (anteprima)Incremental enrichment (preview)
titleSuffix: Azure Cognitive Search
description: Memorizzare nella cache il contenuto intermedio e le modifiche incrementali dalla pipeline di arricchimento dell'ialivello in Archiviazione di Azure per conservare gli investimenti nei documenti elaborati esistenti. Questa funzionalità è attualmente in anteprima pubblica.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024144"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introduzione all'arricchimento incrementale e alla memorizzazione nella cache in Ricerca cognitiva di AzureIntroduction to incremental enrichment and caching in Azure Cognitive Search

> [!IMPORTANT] 
> L'arricchimento incrementale è attualmente in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

L'arricchimento incrementale aggiunge la memorizzazione nella cache e lo stato a una pipeline di arricchimento, preservando l'investimento nell'output esistente, modificando al contempo solo i documenti interessati da particolari modifiche. Questo non solo preserva il vostro investimento monetario nell'elaborazione (in particolare, OCR ed elaborazione delle immagini), ma rende anche per un sistema più efficiente. Quando le strutture e il contenuto vengono memorizzati nella cache, un indicizzatore può determinare quali competenze sono state modificate ed eseguire solo quelle che sono state modificate, nonché eventuali competenze dipendenti a valle. 

## <a name="indexer-cache"></a>Cache dell'indicizzatore

L'arricchimento incrementale aggiunge una cache alla pipeline di arricchimento. L'indicizzatore memorizza nella cache i risultati del cracking del documento più gli output di ogni competenza per ogni documento. Quando un set di competenze viene aggiornato, vengono rieseguite solo le competenze modificate, o a valle. I risultati aggiornati vengono scritti nella cache e il documento viene aggiornato nell'indice di ricerca o nell'archivio informazioni.

Fisicamente, la cache viene archiviata in un contenitore BLOB nell'account di archiviazione di Azure.Physically, the cache is stored in a blob container in your Azure Storage account. La cache utilizza anche l'archiviazione tabelle per un record interno degli aggiornamenti di elaborazione. Tutti gli indici in un servizio di ricerca possono condividere lo stesso account di archiviazione per la cache dell'indicizzatore. A ogni indicizzatore viene assegnato un identificatore di cache univoco e non modificabile per il contenitore utilizzato.

## <a name="cache-configuration"></a>Configurazione della cache

È necessario impostare `cache` la proprietà nell'indicizzatore per iniziare a trarre vantaggio dall'arricchimento incrementale. Nell'esempio seguente viene illustrato un indicizzatore con la memorizzazione nella cache abilitata. Parti specifiche di questa configurazione sono descritte nelle sezioni seguenti. Per ulteriori informazioni, consultate [Impostare l'arricchimento incrementale.](search-howto-incremental-index.md)

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

L'impostazione di questa proprietà su un indicizzatore esistente richiederà la reimpostazione e l'esecuzione di nuovo dell'indicizzatore, che comporterà l'elaborazione di tutti i documenti nell'origine dati. Questo passaggio è necessario per eliminare tutti i documenti arricchiti da versioni precedenti del set di competenze. 

## <a name="cache-management"></a>Gestione della cache

Il ciclo di vita della cache è gestito dall'indicizzatore. Se `cache` la proprietà nell'indicizzatore è impostata su null o la stringa di connessione viene modificata, la cache esistente viene eliminata all'esecuzione successiva dell'indicizzatore. Il ciclo di vita della cache è anche legato al ciclo di vita dell'indicizzatore. Se viene eliminato un indicizzatore, viene eliminata anche la cache associata.

Mentre l'arricchimento incrementale è progettato per rilevare e rispondere alle modifiche senza alcun intervento da parte dell'utente, esistono parametri che è possibile utilizzare per sostituire i comportamenti di default:

+ Assegnare priorità ai nuovi documenti
+ Ignorare i controlli delle competenze
+ Ignora controlli origine dati
+ Valutazione del set di competenze forzata

### <a name="prioritize-new-documents"></a>Assegnare priorità ai nuovi documenti

Impostare `enableReprocessing` la proprietà per controllare l'elaborazione dei documenti in ingresso già rappresentati nella cache. Quando `true` (impostazione predefinita), i documenti già presenti nella cache vengono rielaborati quando si esegue nuovamente l'indicizzatore, presupponendo che l'aggiornamento della competenza influisca su tale documento. 

Quando `false`, i documenti esistenti non vengono rielaborati, assegnando in modo efficace la priorità al nuovo contenuto in ingresso rispetto al contenuto esistente. È consigliabile `enableReprocessing` `false` impostare su solo su base temporanea. Per garantire la coerenza `enableReprocessing` tra `true` il corpo, deve essere la maggior parte del tempo, assicurando che tutti i documenti, sia nuovi che esistenti, siano validi in base alla definizione di competenze corrente.

### <a name="bypass-skillset-evaluation"></a>Ignorare la valutazione del set di competenze

La modifica di un set di competenze e il ritrattamento di tale set di competenze vanno in genere di pari passo. Tuttavia, alcune modifiche a un set di competenze non devono comportare la rielaborazione (ad esempio, la distribuzione di una competenza personalizzata in una nuova posizione o con una nuova chiave di accesso). Molto probabilmente, si tratta di modifiche periferiche che non hanno alcun impatto reale sulla sostanza del set di competenze stesso. 

Se si sa che una modifica al set di competenze è `disableCacheReprocessingChangeDetection` effettivamente superficiale, è necessario ignorare la valutazione del set di competenze impostando il parametro su `true`:

1. Chiamare Update Skillset e modificare la definizione del set di competenze.
1. Aggiungere `disableCacheReprocessingChangeDetection=true` il parametro alla richiesta.
1. Inviare la modifica.

L'impostazione di questo parametro garantisce il commit solo degli aggiornamenti alla definizione del set di competenze e che la modifica non venga valutata per gli effetti sul corpo esistente.

L'esempio seguente mostra una richiesta Update Skillset con il parametro :

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Ignorare i controlli di convalida dell'origine dati

La maggior parte delle modifiche apportate a una definizione dell'origine dati invaliderà la cache. Tuttavia, per gli scenari in cui si sa che una modifica non deve invalidare la cache, ad esempio la modifica di una stringa di connessione o la rotazione della chiave nell'account di archiviazione, aggiungere il`ignoreResetRequirement` parametro nell'aggiornamento dell'origine dati. L'impostazione `true` di questo parametro su consente l'attraversamento del commit, senza attivare una condizione di ripristino che comporterebbe la ricompilazione e la compilazione di tutti gli oggetti da zero.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Valutazione del set di competenze forzata

Lo scopo della cache è evitare elaborazioni non necessarie, ma si supponga di apportare una modifica a una competenza che l'indicizzatore non rileva (ad esempio, la modifica di un elemento nel codice esterno, ad esempio una competenza personalizzata).

In questo caso, è possibile utilizzare [reimposta competenze](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) per forzare il ritrattamento di una particolare competenza, incluse eventuali competenze a valle che dipendono dall'output di tale competenza. Questa API accetta una richiesta POST con un elenco di competenze che devono essere invalidate e contrassegnate per la rielaborazione. Dopo Reset Skills, eseguire l'indicizzatore per richiamare la pipeline.

## <a name="change-detection"></a>Rilevamento delle modifiche

Dopo aver abilitato una cache, l'indicizzatore valuta le modifiche nella composizione della pipeline per determinare quale contenuto può essere riutilizzato e quale deve essere rielaborato. In questa sezione vengono enumerate le modifiche che invalidano la cache, seguite dalle modifiche che attivano l'elaborazione incrementale. 

### <a name="changes-that-invalidate-the-cache"></a>Modifiche che invalidano la cacheChanges that invalidate the cache

Una modifica invalidante è una modifica che fa sì che l'intera cache non sia più valida. Un esempio di modifica invalidante è quello in cui l'origine dati viene aggiornata. Ecco l'elenco completo delle modifiche che, se apportate, invaliderebbero la cache:

* Modifica del tipo di origine dati
* Modifica del contenitore dell'origine dati
* Credenziali dell'origine dati
* Criteri di rilevamento delle modifiche all'origine dati
* Criteri di rilevamento delle eliminazioni di origini dati
* Mapping dei campi dell'indicizzatore
* Parametri dell'indicizzatore
    * Modalità di analisi
    * Estensioni escluse
    * Estensioni indicizzate
    * Solo metadati di archiviazione dell'indice per i documenti di dimensioni eccessive
    * Intestazioni di testo delimitate
    * Delimitatore di testo delimitato
    * Radice documento
    * Azione per immagini (modifiche alla modalità di estrazione delle immagini)

### <a name="changes-that-trigger-incremental-processing"></a>Modifiche che attivano l'elaborazione incrementaleChanges that trigger incremental processing

L'elaborazione incrementale valuta la definizione del set di competenze e determina le competenze da rieseguire, aggiornando in modo selettivo le parti interessate dell'albero del documento. Di seguito è riportato l'elenco completo delle modifiche risultanti nell'arricchimento incrementale:

* Competenza nel set di competenze di tipo diverso Tipo OData della competenza aggiornato
* Parametri specifici delle competenze aggiornati, ad esempio l'URL, i valori predefiniti o altri parametri
* Modifiche agli output delle competenze, la competenza restituisce output aggiuntivi o diversi
* Aggiornamenti delle competenze che producono una cronologia diversa, il concatenamento delle competenze è cambiato, ad esempio gli input delle competenze
* Invalidamento di qualsiasi competenza a monte, se una competenza che fornisce un input a questa competenza viene aggiornata
* Aggiornamenti alla posizione di proiezione dell'archivio conoscenze, risultanti nella ripetizione della proiezione dei documenti
* Modifiche alle proiezioni dell'archivio conoscenze, risultanti nella ripetizione della proiezione dei documenti
* Modifiche ai mapping dei campi di output in un indicizzatore risultanti nella ripetizione della proiezione dei documenti nell'indice

## <a name="api-reference"></a>API (riferimento)

La versione `2019-05-06-Preview` dell'API REST fornisce l'arricchimento incrementale tramite proprietà aggiuntive su indicizzatori, set di competenze e origini dati. Oltre alla documentazione di riferimento, vedere Configurare la [memorizzazione nella cache per l'arricchimento incrementale](search-howto-incremental-index.md) per informazioni dettagliate su come chiamare le API.

+ [Creare un indicizzatore (api-version-2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indicizzatore di aggiornamento (api-version-2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Aggiornamento del set di competenze (api-version-2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (nuovo parametro URI nella richiesta)

+ [Reimpostazione competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indicizzatori di database (Azure SQL, Cosmos DB). Alcuni indicizzatori recuperano i dati tramite query. Per le query che recuperano dati, [Aggiorna origine dati](https://docs.microsoft.com/rest/api/searchservice/update-data-source) supporta un nuovo parametro in una richiesta **ignoreResetRequirement**, che deve essere impostato `true` su quando l'azione di aggiornamento non deve invalidare la cache. 

  Usare **ignoreResetRequirement** con parsimonia in quanto potrebbe causare incoerenze indesiderate nei dati che non verranno rilevati facilmente.

## <a name="next-steps"></a>Passaggi successivi

L'arricchimento incrementale è una potente funzionalità che estende il rilevamento delle modifiche ai set di competenze e all'arricchimento dell'IA. L'arricchimento AIncremental consente il riutilizzo del contenuto elaborato esistente durante l'iterazione sulla progettazione delle competenze.

Come passaggio successivo, abilitare la memorizzazione nella cache in un indicizzatore esistente o aggiungere una cache quando si definisce un nuovo indicizzatore.

> [!div class="nextstepaction"]
> [Configurare la memorizzazione nella cache per l'arricchimento incrementaleConfigure caching for incremental enrichment](search-howto-incremental-index.md)
