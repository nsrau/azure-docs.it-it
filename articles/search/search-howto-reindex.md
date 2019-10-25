---
title: Ricompilare un indice di ricerca cognitiva di Azure
titleSuffix: Azure Cognitive Search
description: Aggiungere nuovi elementi, aggiornare gli elementi o i documenti esistenti o eliminare i documenti obsoleti in una ricompilazione completa o indicizzazione incrementale parziale per aggiornare un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 26a751924985f94a7d7d12a382d4e6654f36ea48
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793716"
---
# <a name="how-to-rebuild-an-azure-cognitive-search-index"></a>Come ricompilare un indice di Azure ricerca cognitiva

Questo articolo illustra come ricompilare un indice di ricerca cognitiva di Azure, le circostanze in cui sono necessarie le ricompilazioni e i consigli per attenuare l'effetto delle ricompilazioni sulle richieste di query in corso.

Una *ricompilazione* consiste nell'eliminare e ricreare le strutture dei dati fisiche associate a un indice, inclusi tutti gli indici invertiti basati sui campi. In ricerca cognitiva di Azure non è possibile eliminare e ricreare i singoli campi. Per ricompilare un indice, è necessario eliminare tutti i dati di archiviazione dei campi, ricreati in base a uno schema di indice esistente o modificato, e quindi ripopolarli con i dati di cui è stato eseguito il push nell'indice o il pull da origini esterne. Ricompilare gli indici durante lo sviluppo è un'operazione consueta, ma potrebbe presentarsi la necessità di ricompilare un indice anche a livello di produzione in seguito a modifiche strutturali, come l'aggiunta di tipi complessi o l'aggiunta di campi agli strumenti suggerimenti.

A differenza delle ricompilazioni che prevedono che l'indice venga portato offline, l'*aggiornamento dei dati* è un'attività che viene eseguita in background. È possibile aggiungere, rimuovere e sostituire documenti con una minima interruzione dei carichi di lavoro delle query, sebbene il completamento delle query richieda in genere più tempo. Per altre informazioni sull'aggiornamento del contenuto degli indici, vedere [Add, Update or Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare documenti).

## <a name="rebuild-conditions"></a>Condizioni di ricompilazione

| Condizione | Description |
|-----------|-------------|
| Modificare la definizione di un campo | La modifica di un nome di campo, di un tipo di dati o di [attributi di indice](https://docs.microsoft.com/rest/api/searchservice/create-index) specifici (searchable, filterable, sortable, facetable) richiede una ricompilazione completa. |
| Assegnare un analizzatore a un campo | Gli [analizzatori](search-analyzers.md) vengono definiti in un indice e quindi assegnati ai campi. È possibile aggiungere una nuova definizione di analizzatore a un indice in qualsiasi momento, ma è possibile *assegnare* un analizzatore solo quando il campo viene creato. Questo vale per entrambe le proprietà **analyzer** e **indexAnalyzer**. La proprietà **searchAnalyzer** è un'eccezione perché è possibile assegnare questa proprietà a un campo esistente. |
| Aggiornare o eliminare una definizione di analizzatore in un indice | Non è possibile eliminare o modificare una configurazione di analizzatore esistente (analizzatore, tokenizer, filtro di token o filtro di caratteri) nell'indice a meno che non si ricompili l'intero indice. |
| Aggiungere un campo a uno strumento suggerimenti | Se esiste già un campo e lo si vuole aggiungere a un costrutto [Suggesters](index-add-suggesters.md), è necessario ricompilare l'indice. |
| Eliminare un campo | Per rimuovere fisicamente tutte le tracce di un campo è necessario ricompilare l'indice. Se la ricompilazione immediata non è una soluzione pratica, è possibile modificare il codice dell'applicazione per disabilitare l'accesso al campo "eliminato". Fisicamente la definizione del campo e i contenuti rimangono nell'indice fino alla successiva ricompilazione, quando si applica uno schema che omette il campo in questione. |
| Cambiare i livelli | Se è necessaria una maggiore capacità, non è disponibile alcun aggiornamento sul posto nel portale di Azure. È necessario creare un nuovo servizio e gli indici devono essere compilati da zero nel nuovo servizio. Per semplificare l'automazione di questo processo, è possibile usare il codice di esempio **index-backup-restore** in questo [repository di esempio di Azure ricerca cognitiva .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Questa app eseguirà il backup dell'indice in una serie di file JSON e quindi ricreerà l'indice in un servizio di ricerca specificato.|

Qualsiasi altra modifica può essere eseguita senza alcun impatto sulle strutture fisiche esistenti. Nello specifico, le modifiche seguenti *non* richiedono una ricompilazione dell'indice:

+ Aggiungere un nuovo campo
+ Impostare l'attributo **retrievable** su un campo esistente
+ Impostare **searchAnalyzer** su un campo esistente
+ Aggiungere una nuova definizione di analizzatore in un indice
+ Aggiungere, aggiornare o eliminare i profili di punteggio
+ Aggiungere, aggiornare o eliminare le impostazioni di CORS
+ Aggiungere, aggiornare o eliminare synonymMaps

Quando si aggiunge un nuovo campo, ai documenti indicizzati esistenti viene assegnato un valore Null per il nuovo campo. In seguito a un aggiornamento dati futuro, i valori dei dati di origine esterni sostituiscono i valori null aggiunti da Azure ricerca cognitiva. Per altre informazioni sull'aggiornamento del contenuto degli indici, vedere [Add, Update or Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare documenti).

## <a name="partial-or-incremental-indexing"></a>Indicizzazione parziale o incrementale

In ricerca cognitiva di Azure non è possibile controllare l'indicizzazione per ogni singolo campo, scegliendo di eliminare o ricreare campi specifici. Non esiste nemmeno un meccanismo predefinito per l'[indicizzazione dei documenti in base a criteri](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Se si ha l'esigenza di eseguire l'indicizzazione in base a determinati criteri occorre usare codice personalizzato.

Un'attività che invece può essere eseguita con facilità è l'*aggiornamento dei documenti* in un indice. Per molte soluzioni di ricerca i dati di origine esterni sono volatili e la sincronizzazione tra i dati di origine e l'indice di ricerca rappresenta una prassi comune. Nel codice chiamare l'operazione [Add, Update or Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungi, aggiorna o elimina documenti) o l'[operazione equivalente di .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) per aggiornare il contenuto dell'indice o per aggiungere valori per un nuovo campo.

## <a name="partial-indexing-with-indexers"></a>Indicizzazione parziale con gli indicizzatori

Gli [indicizzatori](search-indexer-overview.md) semplificano l'attività di aggiornamento dei dati. Un indicizzatore può indicizzare solo una tabella o una vista nell'origine dati esterna. Per indicizzare più tabelle, il metodo più semplice consiste nel creare una vista che unisca le tabelle e proietti le colonne da indicizzare. 

Quando si usano indicizzatori che eseguono una ricerca per indicizzazione sulle origini dati esterne, verificare la presenza di una colonna di "limite massimo" nei dati di origine. Se ne esiste una, è possibile usarla per il rilevamento delle modifiche incrementali selezionando solo le righe con contenuto nuovo o modificato. Per l'[Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) viene usato un campo `lastModified`. Nell'[archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection) `timestamp` svolge lo stesso ruolo. Analogamente, sia l'[indicizzatore di database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) che l'[indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) contengono campi per contrassegnare gli aggiornamenti di riga. 

Per altre informazioni sugli indicizzatori, vedere [Indicizzatori in Ricerca di Azure](search-indexer-overview.md) e [Reset Indexer (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) (Reimposta indicizzatore (API REST del servizio Ricerca di Azure)).

## <a name="how-to-rebuild-an-index"></a>Come ricompilare un indice

Pianificare ricompilazioni complete e frequenti durante la fase di sviluppo attivo, quando gli schemi dell'indice sono in stato di flusso. Per le applicazioni già in produzione è consigliabile creare un nuovo indice da eseguire affiancato a un indice esistente per evitare tempi di inattività delle query.

Le autorizzazioni di lettura e scrittura a livello di servizio sono necessarie per gli aggiornamenti dell'indice. 

Non è possibile ricompilare un indice nel portale. A livello di codice, è possibile chiamare l'[API REST di aggiornamento dell'indice](https://docs.microsoft.com/rest/api/searchservice/update-index) o le [API .NET equivalenti](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) per una ricompilazione completa. La richiesta di aggiornamento di un indice è identica all'[API REST di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index), ma ha un contesto diverso.

Il flusso di lavoro seguente è orientato verso l'API REST, ma si applica ugualmente a .NET SDK.

1. Quando si riutilizza il nome di un indice, [eliminare l'indice esistente](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Le eventuali query destinate a tale indice vengono eliminate immediatamente. L'eliminazione di un indice è un'operazione irreversibile, che elimina definitivamente l'archiviazione fisica della raccolta di campi e altri costrutti. Prima di procedere con l'eliminazione di un indice, accertarsi di conoscere le implicazioni di questa operazione. 

2. Formulare una richiesta di [aggiornamento di un indice](https://docs.microsoft.com/rest/api/searchservice/update-index) con l'endpoint del servizio, la chiave API e una [chiave amministratore](https://docs.microsoft.com/azure/search/search-security-api-keys). Una chiave amministratore è necessaria per le operazioni di scrittura.

3. Nel corpo della richiesta fornire uno schema di indice con le definizioni dei campi modificati. Il corpo della richiesta contiene lo schema dell'indice, oltre ai costrutti per profili di punteggio, analizzatori, strumenti suggerimenti e opzioni CORS. Per i requisiti relativi agli schemi, vedere la documentazione sull'[API REST di creazione indice](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Inviare una richiesta di [aggiornamento dell'indice](https://docs.microsoft.com/rest/api/searchservice/update-index) per ricompilare l'espressione fisica dell'indice in ricerca cognitiva di Azure. 

5. [Caricare l'indice con i documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) da un'origine esterna.

Quando si crea l'indice, viene allocato uno spazio di archiviazione fisico per ogni campo dello schema di indice e viene creato un indice invertito per ogni campo ricercabile. I campi non ricercabili possono essere usati in filtri o espressioni, ma non hanno indici invertiti e non supportano la ricerca full-text o fuzzy. Durante una ricompilazione dell'indice, questi indici invertiti vengono eliminati e ricreati in base allo schema di indice fornito.

Quando si carica l'indice, l'indice invertito di ogni campo viene popolato con tutte le parole univoche e tokenizzate di ogni documento, con un mapping agli ID documento corrispondenti. Ad esempio, nel caso dell'indicizzazione di un set di dati di hotel, un indice invertito creato per un campo Città potrebbe contenere termini per Roma, Milano e così via. Per i documenti che includono Roma o Milano nel campo Città, il relativo ID documento viene elencato insieme al termine. Durante un'operazione di [aggiunta, aggiornamento o eliminazione](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), l'elenco dei termini e degli ID documento viene aggiornato di conseguenza.

> [!NOTE]
> Se i requisiti in termini di contratto di servizio sono rigorosi, può essere consigliabile effettuare il provisioning di un nuovo servizio appositamente per questa attività, mentre le operazioni di sviluppo e indicizzazione avvengono in completo isolamento da un indice di produzione. Un servizio separato viene eseguito nel proprio hardware, eliminando qualsiasi possibilità di conflitto di risorse. Al termine dello sviluppo, è possibile lasciare invariato il nuovo indice, reindirizzando le query al nuovo endpoint e all'indice oppure eseguire il codice terminato per pubblicare un indice modificato nel servizio ricerca cognitiva di Azure originale. Attualmente non è disponibile alcun meccanismo per spostare in un altro servizio un indice pronto per l'uso.

## <a name="view-updates"></a>Visualizzare gli aggiornamenti

È possibile iniziare a eseguire query su un indice subito dopo il caricamento del primo documento. Se si conosce l'ID di un documento, l'[API REST di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/lookup-document) restituisce il documento specifico. Per un test su più larga scala, è possibile aspettare che l'indice venga caricato completamente e quindi usare le query per verificare il contesto che ci si aspetta di vedere.

## <a name="see-also"></a>Vedi anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Azure ricerca cognitiva](search-security-overview.md)