---
title: Ricompilare un indice di ricerca
titleSuffix: Azure Cognitive Search
description: Aggiungere nuovi elementi, aggiornare gli elementi o i documenti esistenti o eliminare i documenti obsoleti in una ricompilazione completa o indicizzazione parziale per aggiornare un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 10c7d4146d61f5b589b29bc8faad5fa8e60a293a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924028"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Come ricompilare un indice in Azure ricerca cognitiva

Questo articolo illustra come ricompilare un indice di ricerca cognitiva di Azure, le circostanze in cui sono necessarie le ricompilazioni e i consigli per attenuare l'effetto delle ricompilazioni sulle richieste di query in corso.

Una *ricompilazione* consiste nell'eliminare e ricreare le strutture dei dati fisiche associate a un indice, inclusi tutti gli indici invertiti basati sui campi. In ricerca cognitiva di Azure non è possibile eliminare e ricreare i singoli campi. Per ricompilare un indice, è necessario eliminare tutti i dati di archiviazione dei campi, ricreati in base a uno schema di indice esistente o modificato, e quindi ripopolarli con i dati di cui è stato eseguito il push nell'indice o il pull da origini esterne. 

Durante lo sviluppo si ricompilano gli indici durante l'iterazione sulla progettazione degli indici, ma potrebbe essere necessario ricompilare un indice a livello di produzione per supportare le modifiche strutturali, ad esempio l'aggiunta di tipi complessi o l'aggiunta di campi ai suggerimenti.

## <a name="rebuild-versus-refresh"></a>"Ricompila" rispetto a "Aggiorna"

La ricompilazione non deve essere confusa con l'aggiornamento del contenuto di un indice con documenti nuovi, modificati o eliminati. L'aggiornamento di un corpus di ricerca è quasi un dato in ogni app di ricerca, con alcuni scenari che richiedono aggiornamenti aggiornati, ad esempio quando un corpus di ricerca deve riflettere le modifiche dell'inventario in un'app di vendita online.

Fino a quando non si modifica la struttura dell'indice, è possibile aggiornare un indice utilizzando le stesse tecniche utilizzate per caricare l'indice inizialmente:

* Per l'indicizzazione in modalità push, chiamare [Aggiungi, aggiornare o eliminare documenti](/rest/api/searchservice/addupdate-or-delete-documents) per eseguire il push delle modifiche a un indice.

* Per gli indicizzatori, è possibile [pianificare l'esecuzione dell'indicizzatore](search-howto-schedule-indexers.md) e usare il rilevamento delle modifiche o i timestamp per identificare il Delta. Se gli aggiornamenti devono essere riflessi più velocemente rispetto a quello che può essere gestito da un'utilità di pianificazione, è invece possibile usare l'indicizzazione in modalità push.

## <a name="rebuild-conditions"></a>Condizioni di ricompilazione

Eliminare e ricreare un indice se si verifica una delle condizioni seguenti. 

| Condizione | Descrizione |
|-----------|-------------|
| Modificare la definizione di un campo | La modifica di un nome di campo, di un tipo di dati o di [attributi di indice](/rest/api/searchservice/create-index) specifici (searchable, filterable, sortable, facetable) richiede una ricompilazione completa. |
| Assegnare un analizzatore a un campo | Gli [analizzatori](search-analyzers.md) vengono definiti in un indice e quindi assegnati ai campi. È possibile aggiungere una nuova definizione di analizzatore a un indice in qualsiasi momento, ma è possibile *assegnare* un analizzatore solo quando il campo viene creato. Questo vale per entrambe le proprietà **analyzer** e **indexAnalyzer**. La proprietà **searchAnalyzer** è un'eccezione perché è possibile assegnare questa proprietà a un campo esistente. |
| Aggiornare o eliminare una definizione di analizzatore in un indice | Non è possibile eliminare o modificare una configurazione di analizzatore esistente (analizzatore, tokenizer, filtro di token o filtro di caratteri) nell'indice a meno che non si ricompili l'intero indice. |
| Aggiungere un campo a uno strumento suggerimenti | Se esiste già un campo e lo si vuole aggiungere a un costrutto [Suggesters](index-add-suggesters.md), è necessario ricompilare l'indice. |
| Eliminare un campo | Per rimuovere fisicamente tutte le tracce di un campo è necessario ricompilare l'indice. Quando una ricompilazione immediata non è praticabile, è possibile modificare il codice dell'applicazione per disabilitare l'accesso al campo "eliminato" oppure utilizzare il [$Select parametro di query](search-query-odata-select.md) per scegliere i campi che sono rappresentati nel set di risultati. Fisicamente la definizione del campo e i contenuti rimangono nell'indice fino alla successiva ricompilazione, quando si applica uno schema che omette il campo in questione. |
| Cambiare i livelli | Se è necessaria una maggiore capacità, non è disponibile alcun aggiornamento sul posto nel portale di Azure. È necessario creare un nuovo servizio e gli indici devono essere compilati da zero nel nuovo servizio. Per semplificare l'automazione di questo processo, è possibile usare il codice di esempio **index-backup-restore** in questo [repository di esempio di Azure ricerca cognitiva .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Questa app eseguirà il backup dell'indice in una serie di file JSON e quindi ricreerà l'indice in un servizio di ricerca specificato.|

## <a name="update-conditions"></a>Condizioni di aggiornamento

È possibile apportare molte altre modifiche senza influito sulle strutture fisiche esistenti. In particolare, le seguenti modifiche *non* richiedono una ricompilazione dell'indice. Per queste modifiche, è possibile [aggiornare una definizione di indice](/rest/api/searchservice/update-index) con le modifiche apportate.

+ Aggiungere un nuovo campo
+ Imposta l'attributo **recuperabile** in un campo esistente
+ Impostare **searchAnalyzer** su un campo esistente
+ Aggiungere una nuova definizione di analizzatore in un indice
+ Aggiungere, aggiornare o eliminare i profili di punteggio
+ Aggiungere, aggiornare o eliminare le impostazioni di CORS
+ Aggiungere, aggiornare o eliminare synonymMaps

Quando si aggiunge un nuovo campo, ai documenti indicizzati esistenti viene assegnato un valore Null per il nuovo campo. In seguito a un aggiornamento dati futuro, i valori dei dati di origine esterni sostituiscono i valori null aggiunti da Azure ricerca cognitiva. Per altre informazioni sull'aggiornamento del contenuto degli indici, vedere [Add, Update or Delete Documents](/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare documenti).

## <a name="how-to-rebuild-an-index"></a>Come ricompilare un indice

Durante lo sviluppo, lo schema dell'indice cambia di frequente. È possibile pianificarlo creando indici che possono essere eliminati, ricreati e ricaricati rapidamente con un set di dati rappresentativo di piccole dimensioni.

Per le applicazioni già in produzione è consigliabile creare un nuovo indice da eseguire affiancato a un indice esistente per evitare tempi di inattività delle query. Il codice dell'applicazione fornisce il reindirizzamento al nuovo indice.

L'indicizzazione non viene eseguita in background e il servizio bilancia l'indicizzazione aggiuntiva rispetto alle query in corso. Durante l'indicizzazione, è possibile [monitorare le richieste di query](search-monitor-queries.md) nel portale per assicurarsi che le query vengano completate in modo tempestivo.

1. Determinare se è necessaria una ricompilazione. Se si aggiungono solo campi o si modifica una parte dell'indice non correlata ai campi, potrebbe essere sufficiente [aggiornare la definizione](/rest/api/searchservice/update-index) senza eliminarla, ricrearla e ricaricarla completamente.

1. [Ottenere una definizione di indice](/rest/api/searchservice/get-index) nel caso in cui sia necessario per riferimento futuro.

1. [Eliminare l'indice esistente](/rest/api/searchservice/delete-index), presupponendo che non vengano eseguiti side-by-side gli indici nuovi e obsoleti. 

   Le eventuali query destinate a tale indice vengono eliminate immediatamente. Tenere presente che l'eliminazione di un indice è irreversibile, eliminando l'archiviazione fisica per la raccolta Fields e altri costrutti. Sospendere per considerare le implicazioni prima di eliminarlo. 

1. [Creare un indice modificato](/rest/api/searchservice/create-index), in cui il corpo della richiesta include le definizioni di campo modificate o modificate.

1. [Caricare l'indice con i documenti](/rest/api/searchservice/addupdate-or-delete-documents) da un'origine esterna.

Quando si crea l'indice, viene allocato uno spazio di archiviazione fisico per ogni campo dello schema di indice e viene creato un indice invertito per ogni campo ricercabile. I campi non ricercabili possono essere usati in filtri o espressioni, ma non hanno indici invertiti e non supportano la ricerca full-text o fuzzy. Durante una ricompilazione dell'indice, questi indici invertiti vengono eliminati e ricreati in base allo schema di indice fornito.

Quando si carica l'indice, l'indice invertito di ogni campo viene popolato con tutte le parole univoche e tokenizzate di ogni documento, con un mapping agli ID documento corrispondenti. Ad esempio, nel caso dell'indicizzazione di un set di dati di hotel, un indice invertito creato per un campo Città potrebbe contenere termini per Roma, Milano e così via. Per i documenti che includono Roma o Milano nel campo Città, il relativo ID documento viene elencato insieme al termine. Durante un'operazione di [aggiunta, aggiornamento o eliminazione](/rest/api/searchservice/addupdate-or-delete-documents), l'elenco dei termini e degli ID documento viene aggiornato di conseguenza.

> [!NOTE]
> Se i requisiti in termini di contratto di servizio sono rigorosi, può essere consigliabile effettuare il provisioning di un nuovo servizio appositamente per questa attività, mentre le operazioni di sviluppo e indicizzazione avvengono in completo isolamento da un indice di produzione. Un servizio separato viene eseguito nel proprio hardware, eliminando qualsiasi possibilità di conflitto di risorse. Al termine dello sviluppo, è possibile lasciare invariato il nuovo indice, reindirizzando le query al nuovo endpoint e all'indice oppure eseguire il codice terminato per pubblicare un indice modificato nel servizio ricerca cognitiva di Azure originale. Attualmente non è disponibile alcun meccanismo per spostare in un altro servizio un indice pronto per l'uso.

## <a name="check-for-updates"></a>Verificare gli aggiornamenti

È possibile iniziare a eseguire query su un indice subito dopo il caricamento del primo documento. Se si conosce l'ID di un documento, l'[API REST di ricerca documenti](/rest/api/searchservice/lookup-document) restituisce il documento specifico. Per un test su più larga scala, è possibile aspettare che l'indice venga caricato completamente e quindi usare le query per verificare il contesto che ci si aspetta di vedere.

È possibile usare [Esplora ricerche](search-explorer.md) o uno strumento di test Web come [post](search-get-started-postman.md) per verificare la presenza di contenuto aggiornato.

Se è stato aggiunto o rinominato un campo, usare [$Select](search-query-odata-select.md) per restituire il campo: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
+ [Indicizzazione nel portale](search-import-data-portal.md)
+ [Indicizzatore del database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indicizzatore di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indicizzatore di archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Sicurezza in Azure ricerca cognitiva](search-security-overview.md)