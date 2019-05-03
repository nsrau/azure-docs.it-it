---
title: Indicizzatori per la ricerca per indicizzazione delle origini dati durante l'indicizzazione - Ricerca di Azure
description: Effettuare una ricerca per indicizzazione in un database SQL di Azure, in Azure Cosmos DB o in Archiviazione di Azure per estrarre dati ricercabili e popolare un indice di Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e35573eea836fc8a88c7515409c070ec63aa3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024885"
---
# <a name="indexers-in-azure-search"></a>Indicizzatori in Ricerca di Azure

Un *indicizzatore* in Ricerca di Azure è un crawler che estrae dati e metadati ricercabili da un'origine dati Azure esterna e popola un indice in base a mapping dei campi tra l'indice e l'origine dati dell'utente. Questo approccio viene talvolta definito come un modello di pull' ' perché il servizio effettua il pull dei dati senza dover scrivere alcun codice che consente di aggiungere dati a un indice.

Gli indicizzatori sono basati su tipi di origini dati o piattaforme, con singoli indicizzatori per SQL Server in Azure, Cosmos DB, archiviazione tabelle di Azure e archiviazione Blob. Gli indicizzatori di archiviazione BLOB sono proprietà aggiuntive specifiche per i tipi di contenuto di blob.

È possibile usare un indicizzatore come unico mezzo per l'inserimento di dati o una combinazione di tecniche, tra cui l'uso di un indicizzatore per caricare solo alcuni dei campi nell'indice.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni quindici minuti. Aggiornamenti più frequenti richiedono un modello push che aggiorna contemporaneamente i dati sia in Ricerca di Azure che nell'origine dati esterna.

## <a name="approaches-for-creating-and-managing-indexers"></a>Approcci per la creazione e la gestione degli indicizzatori

È possibile creare e gestire gli indicizzatori nei modi seguenti:

* [Portale > Importazione guidata dati](search-import-data-portal.md)
* [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Un nuovo indicizzatore viene inizialmente annunciato come funzionalità di anteprima. Le funzionalità di anteprima vengono introdotte nelle API (REST e .NET) e quindi integrate nel portale dopo il passaggio alla disponibilità generale. Se si sta valutando un nuovo indicizzatore, è consigliabile prevedere la scrittura di codice.

## <a name="permissions"></a>Autorizzazioni

Tutte le operazioni correlate agli indicizzatori, incluse le richieste GET per lo stato o le definizioni, richiedono un [chiave api amministratore](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Gli indicizzatori eseguono ricerche per indicizzazione negli archivi dati in Azure.

* [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md) 

> [!Note]
> Archiviazione tabelle di Azure non è supportata per [ricerca cognitiva](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Procedura di configurazione di base
Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati
Un indicizzatore recupera connessione all'origine dati da un *zdroj dat* oggetto. La definizione dell'origine dati fornisce una stringa di connessione ed eventualmente credenziali. Chiamare l'API REST [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) o la [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) per creare la risorsa.

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice in genere non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. I campi devono corrispondere per nome e tipo di dati. Per altre informazioni su come strutturare un indice, vedere [Creare l'indice (API REST di Ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) o [classe Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Per una guida alle associazioni dei campi, vedere [Mapping dei campi negli indicizzatori di Ricerca di Azure](search-indexer-field-mappings.md).

> [!Tip]
> Anche se gli indicizzatori non possono generare automaticamente un indice, la procedura guidata **Importa dati** nel portale può risultare utile. Nella maggior parte dei casi, la procedura guidata può dedurre uno schema di indice dai metadati esistenti nell'origine, presentando uno schema dell'indice preliminare che è possibile modificare inline mentre la procedura guidata è attiva. Dopo la creazione dell'indice nel servizio, le ulteriori modifiche nel portale sono per lo più limitate all'aggiunta di nuovi campi. Prendere in considerazione la procedura guidata per la creazione, ma non per la revisione di un indice. Per un'esperienza di apprendimento pratico, seguire le indicazioni della [procedura dettagliata per il portale](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passaggio 3: Creare e pianificare l'indicizzatore
La definizione dell'indicizzatore è un costrutto che riunisce tutti gli elementi correlati all'inserimento dei dati. Gli elementi necessari includono un'origine dati e indice. Elementi facoltativi includono un mapping di pianificazione e campi. Mapping di campo solo sono facoltativi se corrispondono in modo chiaro i campi di origine e i campi dell'indice. Un indicizzatore può fare riferimento a un'origine dati di un altro servizio, purché tale origine dati appartenga alla stessa sottoscrizione. Per altre informazioni su come strutturare un indicizzatore, vedere [Creare un indicizzatore (API REST di Ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Eseguire gli indicizzatori su richiesta

Anche se è comune per pianificare l'indicizzazione, un indicizzatore può anche essere richiamato su richiesta usando il [Esegui comando](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando l'API viene eseguita correttamente, la chiamata all'indicizzatore è stata pianificata, ma l'elaborazione effettiva si verifica in modo asincrono. 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite API Get Indexer Status. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Ottenere lo stato dell'indicizzatore

È possibile recuperare la cronologia di esecuzione e lo stato di un indicizzatore tramite il [comandi Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


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
* [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
* [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
* [Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-json-blobs.md)
