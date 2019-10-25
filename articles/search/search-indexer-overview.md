---
title: Indicizzatori per la ricerca per indicizzazione delle origini dati durante l'indicizzazione
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione di database SQL di Azure, Azure Cosmos DB o archiviazione di Azure per estrarre dati ricercabili e popolare un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5e5d43909dc0e65c12c053515ba534ce5cfa121f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793652"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indicizzatori in Azure ricerca cognitiva

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

## <a name="permissions"></a>autorizzazioni

Tutte le operazioni correlate agli indicizzatori, incluse le richieste GET per lo stato o le definizioni, richiedono una [chiave API di amministrazione](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Gli indicizzatori eseguono ricerche per indicizzazione negli archivi dati in Azure.

* [Archivio BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archivio tabelle di Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server in Macchine virtuali di Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Istanze gestite di SQL in Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

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
La definizione dell'indicizzatore è un costrutto che riunisce tutti gli elementi correlati all'inserimento di dati. Gli elementi necessari includono un'origine dati e un indice. Gli elementi facoltativi includono una pianificazione e mapping dei campi. Il mapping dei campi è facoltativo solo se i campi di origine e di indice corrispondono chiaramente. Un indicizzatore può fare riferimento a un'origine dati di un altro servizio, purché tale origine dati appartenga alla stessa sottoscrizione. Per altre informazioni sulla struttura di un indicizzatore, vedere [creare un indicizzatore (API REST di Azure ricerca cognitiva)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Eseguire gli indicizzatori su richiesta

Sebbene sia normale pianificare l'indicizzazione, un indicizzatore può anche essere richiamato su richiesta tramite il [comando Run](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando l'API viene eseguita correttamente, la chiamata all'indicizzatore è stata pianificata, ma l'elaborazione effettiva si verifica in modo asincrono. 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite l'API per ottenere lo stato dell'indicizzatore. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Ottenere lo stato dell'indicizzatore

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore tramite il [comando Get Indexer status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

La risposta contiene lo stato globale dell'indicizzatore, la chiamata all'indicizzatore ultimo (o in corso) e la cronologia delle chiamate recenti.

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

La cronologia di esecuzione contiene fino alle 50 più recenti esecuzioni completate, in ordine cronologico inverso (in modo che l'esecuzione più recente venga visualizzata per prima nella risposta).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito queste informazioni di base, il passaggio successivo prevede l'analisi dei requisiti e delle attività specifici per ogni tipo di origine dati.

* [Database SQL di Azure o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Archivio BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archivio tabelle di Azure](search-howto-indexing-azure-tables.md)
* [Indicizzazione di BLOB CSV con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-csv-blobs.md)
* [Indicizzazione di BLOB JSON con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-json-blobs.md)
