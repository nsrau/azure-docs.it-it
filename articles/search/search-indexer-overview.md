---
title: Indicizzatori per la ricerca per indicizzazione dei dati durante l'importazione
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione del database SQL di Azure, del database di Azure Cosmos o dell'archiviazione di Azure per estrarre i dati ricercabili e popolare un indice di Ricerca cognitiva di Azure.Crawl Azure SQL database, Azure Cosmos DB, or Azure storage to extract searchable data and populate an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282991"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indicizzatori in Ricerca cognitiva di Azure

Un *indicizzatore* in Ricerca cognitiva di Azure è un crawler che estrae i dati e i metadati ricercabili da un'origine dati di Azure esterna e popola un indice in base ai mapping da campo a campo tra l'indice e l'origine dati. Questo approccio viene talvolta definito "modello pull" perché il servizio esegue il pull dei dati senza dover scrivere codice che aggiunge dati a un indice.

Gli indicizzatori sono basati su tipi o piattaforme di origine dati, con singoli indicizzatori per SQL Server in Azure, Cosmos DB, Archiviazione tabelle di Azure e Archiviazione BLOB. Gli indicizzatori di archiviazione BLOB hanno proprietà aggiuntive specifiche per i tipi di contenuto BLOB.

È possibile usare un indicizzatore come unico mezzo per l'inserimento di dati o una combinazione di tecniche, tra cui l'uso di un indicizzatore per caricare solo alcuni dei campi nell'indice.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni cinque minuti. Gli aggiornamenti più frequenti richiedono un modello push che aggiorna contemporaneamente i dati sia in Ricerca cognitiva di Azure che nell'origine dati esterna.

## <a name="approaches-for-creating-and-managing-indexers"></a>Approcci per la creazione e la gestione degli indicizzatori

È possibile creare e gestire gli indicizzatori nei modi seguenti:

* [Importazione guidata dati > portale](search-import-data-portal.md)
* [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Un nuovo indicizzatore viene inizialmente annunciato come funzionalità di anteprima. Le funzionalità di anteprima vengono introdotte nelle API (REST e .NET) e quindi integrate nel portale dopo il passaggio alla disponibilità generale. Se si sta valutando un nuovo indicizzatore, è consigliabile prevedere la scrittura di codice.

## <a name="permissions"></a>Autorizzazioni

Tutte le operazioni correlate agli indicizzatori, incluse le richieste GET per lo stato o le definizioni, richiedono una [chiave API di amministrazione.](search-security-api-keys.md) 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Gli indicizzatori eseguono ricerche per indicizzazione negli archivi dati in Azure.

* [Archiviazione BLOB di AzureAzure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in anteprima)Azure Data Lake Storage Gen2 (in preview)
* [Archiviazione tabelle di AzureAzure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server in Macchine virtuali di Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL Managed instances on Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Procedura di configurazione di base
Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati
Un indicizzatore ottiene la connessione all'origine dati da un oggetto *origine dati.* La definizione dell'origine dati fornisce una stringa di connessione ed eventualmente le credenziali. Chiamare l'API REST [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) o la [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) per creare la risorsa.

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice in genere non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. I campi devono corrispondere in base al nome e al tipo di dati. Per altre informazioni sulla strutturazione di un indice, vedere [Creare un indice (API REST di Ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/Create-Index) di Azure) o Classe Index.For more information about structuring an index, see Create an Index (Azure Cognitive Search REST API) or [Index class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Per informazioni sull'associazione dei campi, vedere [Mapping dei campi negli indicizzatori di Ricerca cognitiva](search-indexer-field-mappings.md)di Azure.

> [!Tip]
> Anche se gli indicizzatori non possono generare automaticamente un indice, la procedura guidata **Importa dati** nel portale può risultare utile. Nella maggior parte dei casi, la procedura guidata può dedurre uno schema di indice dai metadati esistenti nell'origine, presentando uno schema dell'indice preliminare che è possibile modificare inline mentre la procedura guidata è attiva. Dopo la creazione dell'indice nel servizio, le ulteriori modifiche nel portale sono per lo più limitate all'aggiunta di nuovi campi. Prendere in considerazione la procedura guidata per la creazione, ma non per la revisione di un indice. Per un'esperienza di apprendimento pratico, seguire le indicazioni della [procedura dettagliata per il portale](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passaggio 3: Creare e pianificare l'indicizzatore
La definizione dell'indicizzatore è un costrutto che riunisce tutti gli elementi correlati all'inserimento di dati. Gli elementi obbligatori includono un'origine dati e un indice. Gli elementi facoltativi includono un abaco e mapping dei campi. Il mapping dei campi è facoltativo solo se i campi di origine e i campi indice corrispondono chiaramente. Per altre informazioni sulla strutturazione di un indicizzatore, vedere [Creare un indicizzatore (API REST di Ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)di Azure) .

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Eseguire gli indicizzatori su richiestaRun indexers on-demand

Sebbene sia comune pianificare l'indicizzazione, un indicizzatore può essere richiamato anche su richiesta utilizzando il [comando Esegui:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando l'API viene eseguita correttamente, la chiamata all'indicizzatore è stata pianificata, ma l'elaborazione effettiva si verifica in modo asincrono. 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite l'API Get Indexer Status. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Ottenere lo stato dell'indicizzatoreGet indexer status

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore tramite il [comando Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


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
* [Archiviazione BLOB di AzureAzure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Archiviazione tabelle di AzureAzure Table Storage](search-howto-indexing-azure-tables.md)
* [Indicizzazione di BLOB CSV tramite l'indicizzatore BLOB di Ricerca cognitiva di AzureIndexing CSV blobs using the Azure Cognitive Search Blob indexer](search-howto-index-csv-blobs.md)
* [Indicizzazione di BLOB JSON con l'indicizzatore BLOB di Ricerca cognitiva di AzureIndexing JSON blobs with Azure Cognitive Search Blob indexer](search-howto-index-json-blobs.md)
