---
title: Indicizzatori per la ricerca per indicizzazione dei dati durante l'importazione
titleSuffix: Azure Cognitive Search
description: Esegui la ricerca per indicizzazione di database SQL di Azure, SQL Istanza gestita, Azure Cosmos DB o archiviazione di Azure per estrarre dati ricercabili e popolare un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f93df91f87f8119a503f2f7c452b61e3af5924f8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208803"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indicizzatori in Ricerca cognitiva di Azure

Un *indicizzatore* in ricerca cognitiva di Azure è un crawler che estrae i dati e i metadati ricercabili da un'origine dati di Azure esterna e popola un indice in base ai mapping da campo a campo tra l'indice e l'origine dati. Questo approccio viene talvolta definito "modello pull" perché il servizio estrae i dati senza che sia necessario scrivere codice che aggiunga dati a un indice.

Gli indicizzatori sono basati su piattaforme o tipi di origini dati, con singoli indicizzatori per SQL Server in Azure, Cosmos DB, archiviazione tabelle di Azure e archiviazione BLOB. Gli indicizzatori di archiviazione BLOB hanno proprietà aggiuntive specifiche per i tipi di contenuto BLOB.

È possibile usare un indicizzatore come unico mezzo per l'inserimento di dati o una combinazione di tecniche, tra cui l'uso di un indicizzatore per caricare solo alcuni dei campi nell'indice.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni cinque minuti. Gli aggiornamenti più frequenti richiedono un modello push che Aggiorna contemporaneamente i dati sia in ricerca cognitiva di Azure che nell'origine dati esterna.

## <a name="approaches-for-creating-and-managing-indexers"></a>Approcci per la creazione e la gestione degli indicizzatori

È possibile creare e gestire gli indicizzatori nei modi seguenti:

* [Importazione guidata dati > portale](search-import-data-portal.md)
* [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Un nuovo indicizzatore viene inizialmente annunciato come funzionalità di anteprima. Le funzionalità di anteprima vengono introdotte nelle API (REST e .NET) e quindi integrate nel portale dopo il passaggio alla disponibilità generale. Se si sta valutando un nuovo indicizzatore, è consigliabile prevedere la scrittura di codice.

## <a name="permissions"></a>Autorizzazioni

Tutte le operazioni correlate agli indicizzatori, incluse le richieste GET per lo stato o le definizioni, richiedono una [chiave API di amministrazione](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Gli indicizzatori eseguono ricerche per indicizzazione negli archivi dati in Azure.

* [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake storage Gen2](search-howto-index-azure-data-lake-storage.md) (in anteprima)
* [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Database SQL di Azure e Istanza gestita SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server in macchine virtuali di Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Istanza gestita di SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>Fasi dell'indicizzatore

In un'esecuzione iniziale, quando l'indice è vuoto, un indicizzatore verrà letto in tutti i dati forniti nella tabella o nel contenitore. Nelle esecuzioni successive, l'indicizzatore può in genere rilevare e recuperare solo i dati che sono stati modificati. Per i dati BLOB, il rilevamento delle modifiche è automatico. Per altre origini dati come Azure SQL o Cosmos DB, è necessario abilitare il rilevamento delle modifiche.

Per ogni documento che inserisce, un indicizzatore implementa o coordina più passaggi, dal recupero dei documenti a un motore di ricerca finale per l'indicizzazione. Facoltativamente, un indicizzatore è anche strumentale per la guida dell'esecuzione e degli output delle competenze, supponendo che venga definito un valore di competenze.

![Fasi dell'indicizzatore](./media/search-indexer-overview/indexer-stages.png "fasi dell'indicizzatore")

### <a name="stage-1-document-cracking"></a>Fase 1: cracking del documento

Il cracking dei documenti è il processo di apertura di file ed estrazione di contenuto. A seconda del tipo di origine dati, l'indicizzatore proverà a eseguire diverse operazioni per estrarre contenuto potenzialmente indicizzabile.  

Esempi:  

* Quando il documento è un record in un' [origine dati SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), l'indicizzatore estrae ognuno dei campi per il record.
* Quando il documento è un file PDF in un' [origine dati di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md), l'indicizzatore estrae il testo, le immagini e i metadati per il file.
* Quando il documento è un record in un' [origine dati Cosmos DB](search-howto-index-cosmosdb.md), l'indicizzatore estrae i campi e i sottocampi dal documento Cosmos DB.

### <a name="stage-2-field-mappings"></a>Fase 2: mapping dei campi 

Un indicizzatore estrae il testo da un campo di origine e lo invia a un campo di destinazione in un indice o in un archivio informazioni. Quando i tipi e i nomi dei campi coincidono, il percorso è chiaro. Tuttavia, è possibile che si vogliano nomi o tipi diversi nell'output, nel qual caso è necessario indicare all'indicizzatore come eseguire il mapping del campo. Questo passaggio si verifica dopo il cracking del documento, ma prima delle trasformazioni, quando l'indicizzatore legge i documenti di origine. Quando si definisce un [mapping di campi](search-indexer-field-mappings.md), il valore del campo di origine viene inviato così com'è al campo di destinazione senza alcuna modifica. I mapping dei campi sono facoltativi.

### <a name="stage-3-skillset-execution"></a>Fase 3: esecuzione delle competenze

L'esecuzione delle competenze è un passaggio facoltativo che richiama l'elaborazione incorporata o personalizzata di intelligenza artificiale. Potrebbe essere necessario per il riconoscimento ottico dei caratteri (OCR) sotto forma di analisi delle immagini oppure potrebbe essere necessaria la traduzione della lingua. Qualunque sia la trasformazione, l'esecuzione delle competenze è la posizione in cui si verifica l'arricchimento. Se un indicizzatore è una pipeline, è possibile pensare a un [skillt](cognitive-search-defining-skillset.md) come una "pipeline all'interno della pipeline". Un skillt ha una propria sequenza di passaggi chiamati Skills.

### <a name="stage-4-output-field-mappings"></a>Fase 4: mapping dei campi di output

L'output di un skillt è effettivamente un albero di informazioni denominato documento arricchito. I mapping dei campi di output consentono di selezionare le parti di questo albero da mappare ai campi dell'indice. Informazioni su come [definire i mapping dei campi di output](cognitive-search-output-field-mapping.md).

Proprio come i mapping dei campi che associano i valori Verbatim dai campi di origine a quello di destinazione, i mapping dei campi di output indicano all'indicizzatore come associare i valori trasformati nel documento arricchito ai campi di destinazione nell'indice. A differenza dei mapping dei campi, che sono considerati facoltativi, sarà sempre necessario definire un mapping del campo di output per qualsiasi contenuto trasformato che deve risiedere in un indice.

L'immagine successiva mostra una rappresentazione della [sessione di debug](cognitive-search-debug-session.md) dell'indicizzatore di esempio delle fasi dell'indicizzatore: cracking del documento, mapping dei campi, esecuzione di competenze e mapping dei campi di output.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="sessione di debug di esempio" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Procedura di configurazione di base

Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati
Un indicizzatore ottiene una connessione all'origine dati da un oggetto *origine dati* . La definizione dell'origine dati fornisce una stringa di connessione e possibilmente le credenziali. Chiamare l'API REST [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) o la [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) per creare la risorsa.

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice in genere non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. I campi devono corrispondere per nome e tipo di dati. Per altre informazioni sulla strutturazione di un indice, vedere [creare un indice (API REST di Azure ricerca cognitiva) o una](https://docs.microsoft.com/rest/api/searchservice/Create-Index) [classe di indice](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Per informazioni sulle associazioni di campi, vedere [mapping dei campi in Azure ricerca cognitiva Indexers](search-indexer-field-mappings.md).

> [!Tip]
> Anche se gli indicizzatori non possono generare automaticamente un indice, la procedura guidata **Importa dati** nel portale può risultare utile. Nella maggior parte dei casi, la procedura guidata può dedurre uno schema di indice dai metadati esistenti nell'origine, presentando uno schema dell'indice preliminare che è possibile modificare inline mentre la procedura guidata è attiva. Dopo la creazione dell'indice nel servizio, le ulteriori modifiche nel portale sono per lo più limitate all'aggiunta di nuovi campi. Prendere in considerazione la procedura guidata per la creazione, ma non per la revisione di un indice. Per un'esperienza di apprendimento pratico, seguire le indicazioni della [procedura dettagliata per il portale](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passaggio 3: Creare e pianificare l'indicizzatore
La definizione dell'indicizzatore è un costrutto che riunisce tutti gli elementi correlati all'inserimento di dati. Gli elementi necessari includono un'origine dati e un indice. Gli elementi facoltativi includono una pianificazione e mapping dei campi. Il mapping dei campi è facoltativo solo se i campi di origine e di indice corrispondono chiaramente. Per altre informazioni sulla struttura di un indicizzatore, vedere [creare un indicizzatore (API REST di Azure ricerca cognitiva)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Eseguire gli indicizzatori su richiesta

Sebbene sia normale pianificare l'indicizzazione, un indicizzatore può anche essere richiamato su richiesta tramite il [comando Run](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Quando l'API viene eseguita correttamente, la chiamata all'indicizzatore è stata pianificata, ma l'elaborazione effettiva si verifica in modo asincrono. 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite l'API per ottenere lo stato dell'indicizzatore. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Ottenere lo stato dell'indicizzatore

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore tramite il [comando Get Indexer status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

La risposta contiene lo stato globale dell'indicizzatore, la chiamata all'indicizzatore ultimo (o in corso) e la cronologia delle chiamate recenti.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

La cronologia di esecuzione contiene fino alle 50 più recenti esecuzioni completate, in ordine cronologico inverso (in modo che l'esecuzione più recente venga visualizzata per prima nella risposta).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito queste informazioni di base, il passaggio successivo prevede l'analisi dei requisiti e delle attività specifici per ogni tipo di origine dati.

* [Database SQL di Azure, Istanza gestita SQL o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
* [Indicizzazione di BLOB CSV con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-csv-blobs.md)
* [Indicizzazione di BLOB JSON con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-json-blobs.md)
