---
title: Importazione dei dati per l'inserimento dati in un indice di ricerca - Ricerca di Azure
description: Compilare e caricare dati in un indice in Ricerca di Azure da origini dati esterne.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b56a31a58937ddbea08ff22c3d1c0c71942f47f1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445404"
---
# <a name="data-import-overview---azure-search"></a>Panoramica: importazione dati di ricerca di Azure

In Ricerca di Azure vengono eseguite query sul contenuto caricato e salvato in un [indice di ricerca](search-what-is-an-index.md). Questo articolo esamina i due approcci di base per la compilazione in un indice: *eseguire il push* dei dati nell'indice a livello di codice o puntare a un [indicizzatore di Ricerca di Azure](search-indexer-overview.md) in un'origine dati supportata per *eseguire il pull* nei dati.

Con entrambi gli approcci, l'obiettivo consiste nel *caricare i dati* da un'origine dati esterna in un indice di ricerca di Azure. Ricerca di Azure ti permetterà di creare un indice vuoto, ma fino a quando non si push o pull dei dati al suo interno, non è disponibile per query.

## <a name="pushing-data-to-an-index"></a>Push dei dati in un indice
Il modello basato sul push, usato per inviare a livello di codice i dati a Ricerca di Azure, è l'approccio più flessibile. Prima di tutto non ha restrizioni per il tipo di origine dati. È possibile eseguire il push di qualsiasi set di dati costituito da documenti JSON a un indice di Ricerca di Azure, presumendo che ai campi definiti nello schema dell'indice venga eseguito il mapping dei campi di ogni documento del set di dati. In secondo luogo non ha restrizioni per la frequenza di esecuzione. È possibile eseguire il push delle modifiche a un indice ogni volta che si vuole. Per applicazioni con requisiti di latenza molto bassa, ad esempio se è necessario che le operazioni di ricerca siano sincronizzate con i database di inventario dinamici, il modello push è l'unica opzione disponibile.

Questo approccio è più flessibile rispetto al modello pull, perché si possono caricare i documenti individualmente o in batch, fino a 1000 per batch o 16 MB, a seconda del limite che viene raggiunto per primo. Il modello push consente anche di caricare i documenti in Ricerca di Azure indipendentemente dalla posizione dei dati.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Come eseguire il push dei dati a un indice di Ricerca di Azure

Per caricare uno o più documenti in un indice, è possibile usare le API seguenti:

+ [Aggiungere, aggiornare o eliminare documenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) o [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Non sono attualmente supportati strumenti per il push dei dati tramite il portale.

Per un'introduzione a ogni metodologia, vedere [Guida introduttiva: Creare un indice di ricerca di Azure con PowerShell](search-create-index-rest-api.md) oppure [ C# avvio rapido: Creare un indice di ricerca di Azure con .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Azioni di indicizzazione: upload, merge, mergeOrUpload, eliminazione

È possibile controllare il tipo di azione di indicizzazione in base al documento, che specifica se il documento deve essere caricato completo, unito con contenuto del documento esistente o eliminato.

Nell'API REST di inviare richieste HTTP POST con corpi delle richieste JSON all'URL dell'endpoint dell'indice di ricerca di Azure. Ogni oggetto JSON nella matrice "value" contiene la chiave del documento e specifica se un'azione di indicizzazione aggiunge, aggiorna o Elimina il contenuto del documento. Per un esempio di codice, vedere [caricare i documenti](search-get-started-dotnet.md#load-documents).

In .NET SDK, creare un pacchetto di dati in un `IndexBatch` oggetto. Un' `IndexBatch` incapsula una raccolta di `IndexAction` oggetti, ognuno dei quali contiene un documento e una proprietà che indica l'azione da eseguire sul documento di ricerca di Azure. Per un esempio di codice, vedere la [ C# Guida introduttiva](search-get-started-dotnet.md).


| @search.action | Descrizione | Campi necessari per ogni documento | Note |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |L'azione `upload` è simile a "upsert", in cui il documento viene inserito se è nuovo e aggiornato o sostituito se esiste già. |chiave, oltre a tutti gli altri campi da definire |Quando si aggiorna o si sostituisce un documento esistente, qualsiasi campo non specificato nella richiesta avrà il campo impostato su `null`. Ciò si verifica anche quando il campo è stato precedentemente impostato su un valore diverso da null. |
| `merge` |Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice, l'unione non riuscirà. |chiave, oltre a tutti gli altri campi da definire |I campi specificati in un'azione di unione sostituiscono i campi esistenti nel documento. In .NET SDK, questa categoria include campi di tipo `DataType.Collection(DataType.String)`. Nell'API REST, questa categoria include campi di tipo `Collection(Edm.String)`. Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue un'unione con valore `["economy", "pool"]` per `tags`, il valore finale del campo `tags` sarà `["economy", "pool"]` e non `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Questa azione si comporta come `merge` se nell'indice esiste già un documento con la chiave specificata. Se il documento non esiste, si comporta come `upload` con un nuovo documento. |chiave, oltre a tutti gli altri campi da definire |- |
| `delete` |Rimuove il documento specificato dall'indice. |solo campo chiave |Tutti i campi diversi dal campo chiave specificati verranno ignorati. Se si vuole rimuovere un singolo campo da un documento, usare invece `merge` e impostare il campo su Null in modo esplicito. |

## <a name="decide-which-indexing-action-to-use"></a>Decidere quale azione di indicizzazione usare
Importare i dati con il .NET SDK (caricamento, unione, eliminazione e mergeOrUpload). A seconda delle azioni scelte tra le seguenti, per ogni documento devono essere inclusi solo campi specifici:


### <a name="formulate-your-query"></a>Formulare la query
Esistono due modi per [eseguire una ricerca nell'indice usando l'API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uno consiste nell'inviare una richiesta HTTP POST in cui i parametri di query vengono definiti in un oggetto JSON nel corpo della richiesta. L'altro consiste nell'inviare una richiesta HTTP GET in cui i parametri di query vengono definiti nell'URL della richiesta. POST ha [limiti più ridotti](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) per quanto riguarda le dimensioni dei parametri di query rispetto a GET. Per questo motivo, è consigliabile usare POST, a meno di non avere circostanze particolari in cui l'uso di GET potrebbe essere più conveniente.

Sia per POST che per GET è necessario fornire il *nome del servizio*, il *nome dell'indice* e la *versione dell'API* corretta nell'URL della richiesta. Al momento della pubblicazione di questo documento la versione dell'API corrente è `2019-05-06`. Per GET i parametri di query devono essere forniti nella *stringa di query* alla fine dell'URL. Per il formato dell'URL, vedere di seguito:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Il formato per POST è lo stesso, ma con solo la versione dell'API nei parametri della stringa di query.


## <a name="pulling-data-into-an-index"></a>Pull dei dati in un indice
Il modello pull effettua una ricerca per indicizzazione su un'origine dati supportata e carica automaticamente i dati nell'indice. In Ricerca di Azure questa funzionalità viene implementata tramite gli *indicizzatori*, attualmente disponibili per queste piattaforme:

+ [Archiviazione BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Archiviazione tabelle](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Database SQL di Azure e SQL Server in VM di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Gli indicizzatori connettono un indice a un'origine dati, in genere una tabella, una vista o una struttura equivalente, e mappano i campi di origine ai campi equivalenti nell'indice. Durante l'esecuzione il set di righe viene automaticamente trasformato in JSON e caricato nell'indice specificato. Tutti gli indicizzatori supportano la pianificazione, in modo da consentire di specificare la frequenza dell'aggiornamento dei dati. La maggior parte degli indicizzatori fornisce il rilevamento delle modifiche se l'origine dati lo supporta. Tramite il rilevamento di modifiche ed eliminazioni nei documenti esistenti, oltre al riconoscimento di nuovi documenti, gli indicizzatori eliminano la necessità di gestire attivamente i dati nell'indice. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Come eseguire il pull dei dati in un indice di Ricerca di Azure

La funzionalità di indicizzatore viene esposta nel [portale di Azure](search-import-data-portal.md), nell'[API REST](/rest/api/searchservice/Indexer-operations) e in [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Un vantaggio dell'uso del portale consiste nel fatto che Ricerca di Azure può solitamente generare automaticamente uno schema di indice predefinito leggendo i metadati del set di dati di origine. È possibile modificare l'indice generato fino al termine dell'elaborazione dell'indice. In seguito, sono consentite solo le modifiche che non richiedono la reindicizzazione. Se le modifiche da apportare influiscono direttamente sullo schema, è necessario ricompilare l'indice. 

## <a name="verify-data-import-with-search-explorer"></a>Verificare l'importazione dei dati con Esplora ricerche

Un modo rapido per eseguire un controllo preliminare sul caricamento del documento consiste nell'usare **Esplora ricerche** nel portale. Esplora ricerche consente di eseguire query su un indice senza che sia necessario scrivere codice. L'esperienza di ricerca è basata sulle impostazioni predefinite, ad esempio la [sintassi semplice](/rest/api/searchservice/simple-query-syntax-in-azure-search) e il [parametro di query searchMode](/rest/api/searchservice/search-documents) predefinito. I risultati vengono restituiti in JSON, in modo che sia possibile esaminare l'intero documento.

> [!TIP]
> Numerosi [esempi di codice di Ricerca di Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) includono set di dati incorporati o immediatamente disponibili, che consentono di iniziare con facilità. Il portale offre anche un indicizzatore e un'origine dati di esempio, costituita da un piccolo set di dati immobiliari, denominato "realestate-us-sample". Quando si esegue l'indicizzatore preconfigurato nell'origine dati di esempio, viene creato un indice che viene caricato con i documenti su cui possono essere eseguite query in Esplora ricerche o tramite codice scritto dall'utente.

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Procedura dettagliata per il portale: creare, caricare ed eseguire query su un indice](search-get-started-portal.md)
