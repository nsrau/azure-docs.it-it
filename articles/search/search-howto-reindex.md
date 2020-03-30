---
title: Ricostruire un indice di ricercaRebuild a search index
titleSuffix: Azure Cognitive Search
description: Aggiungere nuovi elementi, aggiornare elementi o documenti esistenti o eliminare documenti obsoleti in una ricompilazione completa o parziale per aggiornare un indice di Ricerca cognitiva di Azure.Add new elements, update existing elements or documents, or delete obsolete documents in a full rebuild or partial indexing to refresh an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498369"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Come ricostruire un indice in Ricerca cognitiva di AzureHow to rebuild an index in Azure Cognitive Search

In questo articolo viene illustrato come ricostruire un indice di Ricerca cognitiva di Azure, le circostanze in cui sono necessarie ricompilazioni e consigli per attenuare l'impatto delle ricompilazioni sulle richieste di query in corso.

Una *ricompilazione* consiste nell'eliminare e ricreare le strutture dei dati fisiche associate a un indice, inclusi tutti gli indici invertiti basati sui campi. In Ricerca cognitiva di Azure non è possibile eliminare e ricreare singoli campi. Per ricompilare un indice, è necessario eliminare tutti i dati di archiviazione dei campi, ricreati in base a uno schema di indice esistente o modificato, e quindi ripopolarli con i dati di cui è stato eseguito il push nell'indice o il pull da origini esterne. 

Ricompilare gli indici durante lo sviluppo è un'operazione consueta, ma potrebbe presentarsi la necessità di ricompilare un indice anche a livello di produzione in seguito a modifiche strutturali, come l'aggiunta di tipi complessi o l'aggiunta di campi agli strumenti suggerimenti.

## <a name="rebuild-conditions"></a>Condizioni di ricompilazione

Eliminare e ricreare un indice se una delle seguenti condizioni è vera. 

| Condizione | Descrizione |
|-----------|-------------|
| Modificare la definizione di un campo | La modifica di un nome di campo, di un tipo di dati o di [attributi di indice](https://docs.microsoft.com/rest/api/searchservice/create-index) specifici (searchable, filterable, sortable, facetable) richiede una ricompilazione completa. |
| Assegnare un analizzatore a un campo | Gli [analizzatori](search-analyzers.md) vengono definiti in un indice e quindi assegnati ai campi. È possibile aggiungere una nuova definizione di analizzatore a un indice in qualsiasi momento, ma è possibile *assegnare* un analizzatore solo quando il campo viene creato. Questo vale per entrambe le proprietà **analyzer** e **indexAnalyzer**. La proprietà **searchAnalyzer** è un'eccezione perché è possibile assegnare questa proprietà a un campo esistente. |
| Aggiornare o eliminare una definizione di analizzatore in un indice | Non è possibile eliminare o modificare una configurazione di analizzatore esistente (analizzatore, tokenizer, filtro di token o filtro di caratteri) nell'indice a meno che non si ricompili l'intero indice. |
| Aggiungere un campo a uno strumento suggerimenti | Se esiste già un campo e lo si vuole aggiungere a un costrutto [Suggesters](index-add-suggesters.md), è necessario ricompilare l'indice. |
| Eliminare un campo | Per rimuovere fisicamente tutte le tracce di un campo è necessario ricompilare l'indice. Quando una ricompilazione immediata non è pratica, è possibile modificare il codice dell'applicazione per disabilitare l'accesso al campo "eliminato" o utilizzare il [parametro](search-query-odata-select.md) di query $select per scegliere i campi rappresentati nel set di risultati. Fisicamente la definizione del campo e i contenuti rimangono nell'indice fino alla successiva ricompilazione, quando si applica uno schema che omette il campo in questione. |
| Cambiare i livelli | Se è necessaria una maggiore capacità, non è disponibile alcun aggiornamento sul posto nel portale di Azure.If you require more capacity, there is no in-place upgrade in the Azure portal. È necessario creare un nuovo servizio e creare indici da zero sul nuovo servizio. Per automatizzare questo processo, è possibile usare il codice di esempio **index-backup-restore** in questo repository di esempio di Ricerca cognitiva di [Azure .NET.](https://github.com/Azure-Samples/azure-search-dotnet-samples) Questa app eseguirà il backup dell'indice in una serie di file JSON e quindi ricreerà l'indice in un servizio di ricerca specificato.|

## <a name="update-conditions"></a>Condizioni di aggiornamento

Molte altre modifiche possono essere apportate senza influire sulle strutture fisiche esistenti. In particolare, le modifiche seguenti *non* richiedono una ricostruzione dell'indice. Per queste modifiche, è possibile aggiornare una [definizione](https://docs.microsoft.com/rest/api/searchservice/update-index) di indice con le modifiche.

+ Aggiungere un nuovo campo
+ Impostare l'attributo **recuperabile** in un campo esistente
+ Impostare **searchAnalyzer** su un campo esistente
+ Aggiungere una nuova definizione di analizzatore in un indice
+ Aggiungere, aggiornare o eliminare i profili di punteggio
+ Aggiungere, aggiornare o eliminare le impostazioni di CORS
+ Aggiungere, aggiornare o eliminare synonymMaps

Quando si aggiunge un nuovo campo, ai documenti indicizzati esistenti viene assegnato un valore Null per il nuovo campo. In un aggiornamento dati futuro, i valori dei dati di origine esterni sostituiscono i valori Null aggiunti da Ricerca cognitiva di Azure.On a future data refresh, values from external source data replace the nulls added by Azure Cognitive Search. Per altre informazioni sull'aggiornamento del contenuto degli indici, vedere [Add, Update or Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare documenti).

## <a name="how-to-rebuild-an-index"></a>Come ricompilare un indice

Durante lo sviluppo, lo schema dell'indice cambia frequentemente. È possibile pianificarlo creando indici che possono essere eliminati, ricreati e ricaricati rapidamente con un piccolo set di dati rappresentativo.

Per le applicazioni già in produzione è consigliabile creare un nuovo indice da eseguire affiancato a un indice esistente per evitare tempi di inattività delle query. Il codice dell'applicazione fornisce il reindirizzamento al nuovo indice.

L'indicizzazione non viene eseguita in background e il servizio bilancia l'indicizzazione aggiuntiva rispetto alle query in corso. Durante l'indicizzazione, è possibile [monitorare le richieste](search-monitor-queries.md) di query nel portale per assicurarsi che le query vengano completate in modo tempestivo.

1. Determinare se è necessaria una ricostruzione. Se si aggiungono solo campi o si modifica una parte dell'indice non correlata ai campi, è possibile aggiornare semplicemente [la definizione](https://docs.microsoft.com/rest/api/searchservice/update-index) senza eliminarla, ricrearla e ricaricarla completamente.

1. [Ottenere una definizione](https://docs.microsoft.com/rest/api/searchservice/get-index) di indice nel caso in cui sia necessario per riferimento futuro.

1. [Eliminare l'indice esistente](https://docs.microsoft.com/rest/api/searchservice/delete-index), presupponendo che non si eseguano indici nuovi e precedenti affiancati. 

   Le eventuali query destinate a tale indice vengono eliminate immediatamente. Tenere presente che l'eliminazione di un indice è irreversibile, distruggendo l'archiviazione fisica per la raccolta di campi e altri costrutti. Pausa per pensare alle implicazioni prima di lasciarlo cadere. 

1. [Creare un indice rivisto,](https://docs.microsoft.com/rest/api/searchservice/create-index)in cui il corpo della richiesta includa definizioni di campo modificate o modificate.

1. [Caricare l'indice con i documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) da un'origine esterna.

Quando si crea l'indice, viene allocato uno spazio di archiviazione fisico per ogni campo dello schema di indice e viene creato un indice invertito per ogni campo ricercabile. I campi non ricercabili possono essere usati in filtri o espressioni, ma non hanno indici invertiti e non supportano la ricerca full-text o fuzzy. Durante una ricompilazione dell'indice, questi indici invertiti vengono eliminati e ricreati in base allo schema di indice fornito.

Quando si carica l'indice, l'indice invertito di ogni campo viene popolato con tutte le parole univoche e tokenizzate di ogni documento, con un mapping agli ID documento corrispondenti. Ad esempio, nel caso dell'indicizzazione di un set di dati di hotel, un indice invertito creato per un campo Città potrebbe contenere termini per Roma, Milano e così via. Per i documenti che includono Roma o Milano nel campo Città, il relativo ID documento viene elencato insieme al termine. Durante un'operazione di [aggiunta, aggiornamento o eliminazione](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), l'elenco dei termini e degli ID documento viene aggiornato di conseguenza.

> [!NOTE]
> Se i requisiti in termini di contratto di servizio sono rigorosi, può essere consigliabile effettuare il provisioning di un nuovo servizio appositamente per questa attività, mentre le operazioni di sviluppo e indicizzazione avvengono in completo isolamento da un indice di produzione. Un servizio separato viene eseguito nel proprio hardware, eliminando qualsiasi possibilità di conflitto di risorse. Al termine dello sviluppo, è necessario lasciare il nuovo indice sul posto, reindirizzare le query al nuovo endpoint e indice oppure eseguire il codice completato per pubblicare un indice rivisto nel servizio Ricerca cognitiva di Azure originale. Attualmente non è disponibile alcun meccanismo per spostare in un altro servizio un indice pronto per l'uso.

## <a name="check-for-updates"></a>Verificare gli aggiornamenti

È possibile iniziare a eseguire query su un indice subito dopo il caricamento del primo documento. Se si conosce l'ID di un documento, l'[API REST di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/lookup-document) restituisce il documento specifico. Per un test su più larga scala, è possibile aspettare che l'indice venga caricato completamente e quindi usare le query per verificare il contesto che ci si aspetta di vedere.

È possibile utilizzare [Search Explorer](search-explorer.md) o uno strumento di test Web come [Postman](search-get-started-postman.md) per verificare la disponibilità di contenuto aggiornato.

Se è stato aggiunto o rinominato un campo, utilizzare [$select](search-query-odata-select.md) per restituire tale campo:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di Archiviazione BLOB di AzureAzure Blob Storage indexer](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di Archiviazione tabelle di AzureAzure Table Storage indexer](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Ricerca cognitiva di AzureSecurity in Azure Cognitive Search](search-security-overview.md)