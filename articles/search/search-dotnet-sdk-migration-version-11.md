---
title: Eseguire l'aggiornamento a .NET SDK versione 11
titleSuffix: Azure Cognitive Search
description: Eseguire la migrazione del codice ad Azure ricerca cognitiva .NET SDK versione 11 da versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 03d40dcaeaefe01fecbc201cf28dc20c8634af9d
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926672"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Eseguire l'aggiornamento ad Azure ricerca cognitiva .NET SDK versione 11

Se si usa la versione 10,0 o precedente di [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search), questo articolo consentirà di eseguire l'aggiornamento alla versione 11.

La versione 11 è una libreria client completamente riprogettata, rilasciata dal team di sviluppo di Azure SDK (le versioni precedenti sono state prodotte dal team di sviluppo di Azure ricerca cognitiva). La libreria è stata riprogettata per una maggiore coerenza con altre librerie client di Azure, prendendo una dipendenza da [Azure. Core](https://docs.microsoft.com/dotnet/api/azure.core) e [System.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json)e implementando approcci noti per le attività comuni.

Di seguito sono riportate alcune differenze principali che si noterà nella nuova versione:

+ Un pacchetto e una libreria anziché più
+ Nuovo nome del pacchetto: `Azure.Search.Documents` anziché `Microsoft.Azure.Search` .
+ Tre client invece di due: `SearchClient` , `SearchIndexClient` ,`SearchIndexerClient`
+ Denominare le differenze in una gamma di API e piccole differenze strutturali che semplificano alcune attività

## <a name="package-and-library-consolidation"></a>Consolidamento di pacchetti e librerie

La versione 11 consolida più pacchetti e librerie in uno. Dopo la migrazione, sarà possibile gestire un minor numero di librerie.

+ [Azure.Search.Docpacchetto uments](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Informazioni di riferimento sulle API per la libreria client](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Differenze tra client

Se applicabile, nella tabella seguente viene eseguito il mapping delle librerie client tra le due versioni.

| Ambito delle operazioni | Microsoft. Azure. search &nbsp; (V10) | Azure.Search.Documents &nbsp; (V11) |
|---------------------|------------------------------|------------------------------|
| Client utilizzato per le query e per popolare un indice. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Client usato per indici, analizzatori, mappe sinonimi | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Client usato per gli indicizzatori, le origini dati, skillsets | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**nuovo**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient`esiste in entrambe le versioni, ma supporta elementi diversi. Nella versione 10 `SearchIndexClient` creare indici e altri oggetti. Nella versione 11, `SearchIndexClient` funziona con gli indici esistenti. Per evitare confusione durante l'aggiornamento del codice, tenere presente l'ordine in cui i riferimenti client vengono aggiornati. Per attenuare eventuali problemi di sostituzione delle stringhe, seguire la sequenza [descritta nella procedura di aggiornamento](#UpgradeSteps) .

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Denominazione e altre differenze di API

Oltre alle differenze dei client (annotate in precedenza e quindi omesse qui), più API sono state rinominate e in alcuni casi riprogettate. Di seguito sono riepilogate le differenze tra i nomi di classe. Questo elenco non è esaustivo, ma esegue modifiche all'API di gruppo per attività, che può essere utile per le revisioni su blocchi di codice specifici. Per un elenco dettagliato degli aggiornamenti dell'API, vedere il [log delle modifiche](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) per `Azure.Search.Documents` su GitHub.

### <a name="authentication-and-encryption"></a>Autenticazione e crittografia

| Versione 10 | Equivalente versione 11 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey`(esistente nell' [SDK di anteprima](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) come funzionalità disponibile a livello generale) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indici, analizzatori, mappe sinonimi

| Versione 10 | Equivalente versione 11 |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Campo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analizzatore](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (anche `AnalyzerName` a `LexicalAnalyzerName` ) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (anche `StandardTokenizerV2` a `LuceneStandardTokenizerV2` ) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (anche `TokenizerName` a `LexicalTokenizerName` ) |
| [SynonymMap. Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | No. Rimuovere i riferimenti a `Format` . |

Le definizioni dei campi sono semplificate: [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) sono nuove API per la creazione di definizioni di campo.

### <a name="indexers-datasources-skillsets"></a>Indicizzatori, origini dati, skillsets

| Versione 10 | Equivalente versione 11 |
|------------|-----------------------|
| [Indicizzatore](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Abilità](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Set di competenze](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importazione dei dati

| Versione 10 | Equivalente versione 11 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Definizioni di query e risultati

| Versione 10 | Equivalente versione 11 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) o [searchresults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1), a seconda che il risultato sia un singolo documento o multiplo. |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions hanno](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Novità della versione 11

Ogni versione di una libreria client di Azure ricerca cognitiva è destinata a una versione corrispondente dell'API REST. L'API REST è considerata fondamentale per il servizio, con singoli SDK che racchiudono una versione dell'API REST. Gli sviluppatori .NET possono essere utili per esaminare la documentazione dell' [API REST](https://docs.microsoft.com/rest/api/searchservice/) se si desiderano maggiori informazioni su oggetti o operazioni specifiche.

La versione 11 è destinata al [servizio di ricerca 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Poiché la versione 11 è anche una nuova libreria client compilata da zero, la maggior parte degli sforzi di sviluppo si è concentrata sull'equivalenza con la versione 10, con il supporto di alcune funzionalità API REST ancora in sospeso.

La versione 11 supporta completamente gli oggetti e le operazioni seguenti:

+ Creazione e gestione di indici
+ Creazione e gestione della mappa sinonimi
+ Tutti i tipi di query e la sintassi (eccetto i filtri spaziali)
+ Oggetti e operazioni dell'indicizzatore per l'indicizzazione di origini dati di Azure, tra cui origini dati e skillsets

### <a name="pending-features"></a>Funzionalità in sospeso

Le seguenti funzionalità della versione 10 non sono ancora disponibili nella versione 11. Se si usano queste funzionalità, disattivare la migrazione fino a quando non sono supportate.

+ tipi geospaziali
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (anche se è possibile usare [questa soluzione alternativa](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)).
+ [Knowledge store](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento

I passaggi seguenti consentono di iniziare a eseguire una migrazione del codice esaminando il primo set di attività necessarie, in particolare per quanto riguarda i riferimenti ai client.

1. Installare il [pacchetto diAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) facendo clic con il pulsante destro del mouse sui riferimenti del progetto e scegliendo "Gestisci pacchetti NuGet". in Visual Studio.

1. Sostituire le direttive using per Microsoft. Azure. search con quanto segue:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Sostituire [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) con [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential).

1. Aggiornare i riferimenti client per gli oggetti correlati all'indicizzatore. Se si usano gli indicizzatori, le origini dati o skillsets, modificare i riferimenti client in [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient). Questo client è una novità della versione 11 e non ha attività precedenti.

1. Aggiornare i riferimenti client per le query e l'importazione di dati. Le istanze di [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) devono essere modificate in [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient). Per evitare confusione dei nomi, assicurarsi di intercettare tutte le istanze prima di procedere al passaggio successivo.

1. Aggiornare i riferimenti client per gli oggetti index, indexer, mapping sinonimo e Analyzer. Le istanze di [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) devono essere modificate in [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient). 

1. Per quanto possibile, aggiornare le classi, i metodi e le proprietà per usare le API della nuova libreria. La sezione relativa alle [differenze di denominazione](#naming-differences) è un punto di partenza, ma è anche possibile esaminare il [log delle modifiche](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   In caso di problemi durante la ricerca di API equivalenti, è consigliabile registrare un problema in in [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) modo che sia possibile migliorare la documentazione o esaminare il problema.

1. Ricompilare la soluzione. Dopo aver corretto gli errori o gli avvisi di compilazione, è possibile apportare ulteriori modifiche all'applicazione per sfruttare i vantaggi offerti dalle [nuove funzionalità](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Modifiche di rilievo nella versione 11

Date le modifiche apportate alle librerie e alle API, un aggiornamento alla versione 11 non è semplice e costituisce una modifica sostanziale nel senso che il codice non sarà più compatibile con le versioni precedenti. Per una revisione approfondita delle differenze, vedere il [log delle modifiche](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) per `Azure.Search.Documents` .

In termini di versioni del servizio, il passaggio da 10 a 11 introduce le modifiche di comportamento seguenti: 

+ L' [algoritmo di classificazione BM25](index-ranking-similarity.md) sostituisce l'algoritmo di classificazione precedente con la tecnologia più recente. I nuovi servizi utilizzeranno questo algoritmo automaticamente. Per i servizi esistenti, è necessario impostare i parametri per l'utilizzo del nuovo algoritmo.

+ I [risultati ordinati](search-query-odata-orderby.md) per i valori null sono stati modificati in questa versione, con i valori Null visualizzati per primi se l'ordinamento è `asc` e l'ultimo se l'ordinamento è `desc` . Se è stato scritto codice per gestire il modo in cui vengono ordinati i valori null, è consigliabile esaminare e potenzialmente rimuovere tale codice se non è più necessario.

## <a name="next-steps"></a>Passaggi successivi

+ [Azure.Search.Docpacchetto uments](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Esempi in GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Informazioni di riferimento sull'APIAzure.Search.Document](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)