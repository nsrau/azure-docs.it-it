---
title: Eseguire l'aggiornamento a Ricerca di Azure .NET SDK versione 9
titleSuffix: Azure Cognitive Search
description: Eseguire la migrazione del codice alla versione 9 di Azure Search .NET SDK dalle versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793009"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Eseguire l'aggiornamento a Ricerca di Azure .NET SDK versione 9

Se si usa la versione 7.0-preview o precedente di [Azure Search .NET SDK,](https://aka.ms/search-sdk)questo articolo consente di aggiornare l'applicazione per l'utilizzo della versione 9.

> [!NOTE]
> Se si desidera utilizzare la versione 8.0-preview per valutare le funzionalità che non sono ancora in genere disponibili, è anche possibile seguire le istruzioni in questo articolo per eseguire l'aggiornamento all'anteprima 8.0 dalle versioni precedenti.

Per una procedura dettagliata più generale relativa all'SDK, inclusi gli esempi, vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

La versione 9 di Azure Search .NET SDK contiene molte modifiche rispetto alle versioni precedenti. Alcune di queste sono modifiche di rilievo, ma devono richiedere solo modifiche relativamente minori al codice. Per le istruzioni su come modificare il codice per usare la nuova versione dell'SDK, vedere [Passaggi per eseguire l'aggiornamento](#UpgradeSteps) .

> [!NOTE]
> Se si usa la versione 4.0-preview o versione precedente, è necessario eseguire prima l'aggiornamento alla versione 5 e quindi eseguire l'aggiornamento alla versione 9. Per istruzioni, vedere [Aggiornamento a Azure Search .NET SDK versione 5.](search-dotnet-sdk-migration-version-5.md)
>
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Novità della versione 9
La versione 9 di Ricerca di Azure .NET SDK è destinata alla versione più recente disponibile in generale dell'API REST di Ricerca di Azure, in particolare 2019-05-06. Questo rende possibile usare le nuove funzionalità di Ricerca di Azure da un'applicazione .NET, incluse le seguenti:

* [L'arricchimento](cognitive-search-concept-intro.md) dell'iA è la possibilità di estrarre testo da immagini, BLOB e altre origini dati non strutturate, arricchendo il contenuto per renderlo più ricercabile in un indice di Ricerca di Azure.AI enrichment is the ability to extract text from images, blobs, and other unstructured data sources - enriching the content to make it more searchable in an Azure Search index.
* Il supporto per [i tipi complessi](search-howto-complex-data-types.md) consente di modellare quasi qualsiasi struttura JSON annidata in un indice di Ricerca di Azure.Support for complex types allows you to model almost any nested JSON structure in an Azure Search index.
* [Il completamento automatico](search-autocomplete-tutorial.md) offre un'alternativa all'API **Suggerisci** per l'implementazione del comportamento di ricerca durante la digitazione. Il completamento automatico "termina" la parola o la frase che un utente sta digitando.
* [La modalità di analisi JsonLines,](search-howto-index-json-blobs.md)parte dell'indicizzazione BLOB di Azure, crea un documento di ricerca per ogni entità JSON separato da una nuova riga.

### <a name="new-preview-features-in-version-80-preview"></a>Nuove funzionalità di anteprima della versione 8.0-anteprima
Versione 8.0-preview di Azure Search .NET SDK targets versione API 2017-11-11-Preview. Questa versione include tutte le stesse caratteristiche della versione 9, più:

* Le chiavi di [crittografia gestite dal cliente](search-security-manage-encryption-keys.md) per la crittografia lato servizio inattivi sono una nuova funzionalità di anteprima. Oltre alla crittografia inattivi integrata gestita da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui si è l'unico proprietario delle chiavi.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A seconda della struttura del codice, la ricompilazione potrebbe essere eseguita correttamente. In questo caso è possibile procedere.

Se la compilazione non riesce, sarà necessario correggere ogni errore di compilazione. Vedere [Modifiche di rilievo nella versione 9](#ListOfChanges) per informazioni dettagliate su come risolvere ogni potenziale errore di compilazione.

È possibile che vengano visualizzati avvisi di compilazione aggiuntivi correlati a proprietà o metodi obsoleti. Gli avvisi indicheranno istruzioni sulle operazioni da eseguire al posto della funzionalità deprecata. Ad esempio, se l'applicazione utilizza la `DataSourceType.DocumentDb` proprietà, si dovrebbe ottenere un avviso che dice "Questo membro è deprecato. Utilizzare invece CosmosDb".

Dopo avere corretto eventuali errori o avvisi di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità dell'SDK sono descritte in dettaglio [in Novità della versione 9.](#WhatsNew)

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Modifiche di rilievo nella versione 9Breaking changes in version 9

Esistono diverse modifiche di rilievo nella versione 9 che possono richiedere modifiche al codice oltre a ricompilare l'applicazione.

> [!NOTE]
> L'elenco delle modifiche riportato di seguito non è esaustivo. Alcune modifiche probabilmente non genereranno errori di compilazione, ma sono tecnicamente interrompendo poiché interrompono la compatibilità binaria con assembly che dipendono da versioni precedenti degli assembly di Azure Search .NET SDK. Tali modifiche non sono elencate di seguito. Ricompilare l'applicazione durante l'aggiornamento alla versione 9 per evitare problemi di compatibilità binaria.

### <a name="immutable-properties"></a>Proprietà non modificabili

Le proprietà pubbliche di diverse classi di modello sono ora non modificabili. Se è necessario creare istanze personalizzate di queste classi per il test, è possibile utilizzare i nuovi costruttori con parametri:If you need to create custom instances of these classes for testing, you can use the new parameterized constructors:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Modifiche al campo

La `Field` classe è stata modificata ora che può anche rappresentare campi complessi.

Le `bool` seguenti proprietà sono ora nullable:The following properties are now nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Ciò è dovuto al `null` fatto che queste proprietà devono ora essere nel caso di campi complessi. Se si dispone di codice che legge queste proprietà, deve essere preparato per gestire `null`. Si noti che `Field` tutte le altre proprietà di sono sempre state `null` e continuano ad essere nullable, e alcune di queste saranno anche nel caso di campi complessi, in particolare i seguenti:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Il costruttore `Field` senza parametri `internal`di è stato creato. D'ora in `Field` poi, ogni richiede un nome esplicito e un tipo di dati al momento della costruzione.

### <a name="simplified-batch-and-results-types"></a>Tipi di risultati e batch semplificati

Nella versione 7.0-preview e versioni precedenti, le varie classi che incapsulano gruppi di documenti erano strutturati in gerarchie di classi parallele:In version 7.0-preview and earlier, the various classes that encapsulate groups of documents were structured into parallel class hierarchies:

  -  `DocumentSearchResult`ed `DocumentSearchResult<T>` ereditato da`DocumentSearchResultBase`
  -  `DocumentSuggestResult`ed `DocumentSuggestResult<T>` ereditato da`DocumentSuggestResultBase`
  -  `IndexAction`ed `IndexAction<T>` ereditato da`IndexActionBase`
  -  `IndexBatch`ed `IndexBatch<T>` ereditato da`IndexBatchBase`
  -  `SearchResult`ed `SearchResult<T>` ereditato da`SearchResultBase`
  -  `SuggestResult`ed `SuggestResult<T>` ereditato da`SuggestResultBase`

I tipi derivati senza un parametro di tipo generico dovevano essere `Document` utilizzati in scenari "tipizzati in modo dinamico" e presunto utilizzo del tipo.

A partire dalla versione 8.0-preview, le classi di base e le classi derivate non generiche sono state tutte rimosse. Per scenari tipizzati in modo `IndexBatch<Document>` `DocumentSearchResult<Document>`dinamico, è possibile utilizzare , e così via.
 
### <a name="removed-extensibleenum"></a>Rimosso ExtensibleEnum

La classe di base `ExtensibleEnum` è stata rimossa. Tutte le classi che derivano da `AnalyzerName`esso `DataType`sono `DataSourceType` ora struct, ad esempio , e . Anche `Create` i loro metodi sono stati rimossi. È sufficiente rimuovere `Create` le chiamate a poiché questi tipi sono convertibili in modo implicito dalle stringhe. Se ciò genera errori del compilatore, è possibile richiamare in modo esplicito l'operatore di conversione tramite il cast per evitare ambiguità sui tipi. Ad esempio, è possibile modificare codice simile al seguente:For example, you can change code like this:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

con questo:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Le proprietà che contengono valori facoltativi di questi tipi sono ora tipizzate in modo esplicito come nullable in modo che continuino a essere facoltative.

### <a name="removed-facetresults-and-hithighlights"></a>Rimossi FacetResults e HitHighlights

Le `FacetResults` `HitHighlights` classi e sono state rimosse. I risultati dei facet `IDictionary<string, IList<FacetResult>>` vengono ora `IDictionary<string, IList<string>>`digitati come e colpiti da evidenziazioni come . Un modo rapido per risolvere gli errori `using` di compilazione introdotti da questa modifica consiste nell'aggiungere alias all'inizio di ogni file che utilizza i tipi rimossi. Ad esempio:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Cambia in SynonymMap 

Il costruttore `SynonymMap` non ha più un parametro `enum` per `SynonymMapFormat`. Tale enum aveva un solo valore ed era quindi ridondante. Se in conseguenza di ciò vengono visualizzati errori di compilazione, è sufficiente rimuovere i riferimenti al parametro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Varie modifiche alle classi del modello

La `AutocompleteMode` proprietà `AutocompleteParameters` di non è più nullable. Se si dispone di codice `null`che assegna questa proprietà a , è sufficiente rimuoverla e la proprietà verrà inizializzata automaticamente sul valore predefinito.

L'ordine dei parametri `IndexAction` per il costruttore è stato modificato ora che questo costruttore viene generato automaticamente. Anziché utilizzare il costruttore , `IndexAction.Upload`è `IndexAction.Merge`consigliabile utilizzare i metodi factory , e così via.

### <a name="removed-preview-features"></a>Funzionalità di anteprima rimosse

Se si esegue l'aggiornamento dalla versione 8.0-preview alla versione 9, tenere presente che la crittografia con chiavi gestite dal cliente è stata rimossa poiché questa funzionalità è ancora in anteprima. In particolare, le `EncryptionKey` proprietà di `Index` e `SynonymMap` sono state rimosse.

Se l'applicazione ha una dipendenza rigida da questa funzionalità, non sarà possibile eseguire l'aggiornamento alla versione 9 di Ricerca di Azure .NET SDK. È possibile continuare a utilizzare la versione 8.0-anteprima. Tenere presente, tuttavia, che **non è consigliabile usare SDK in anteprima nelle applicazioni di produzione**. Le funzionalità di anteprima sono destinate esclusivamente alla valutazione e sono soggette a modifiche.

> [!NOTE]
> Se sono stati creati indici crittografati o mappe dei sinonimi utilizzando l'anteprima 8.0 dell'SDK, sarà comunque possibile utilizzarli e modificarne le definizioni utilizzando la versione 9 dell'SDK senza influire negativamente sul loro stato di crittografia. La versione 9 dell'SDK `encryptionKey` non invierà la proprietà all'API REST e di conseguenza l'API REST non modificherà lo stato di crittografia della risorsa. 

### <a name="behavioral-change-in-data-retrieval"></a>Cambiamento comportamentale nel recupero dei dati

Se si utilizzano le API `Search`"tipizzate in modo dinamico" , `Suggest`o `Get` che restituiscono istanze di tipo `Document`, tenere presente che ora deserializzano le matrici JSON vuote `object[]` anziché . `string[]`

## <a name="conclusion"></a>Conclusioni
Per altri dettagli sull'uso di .NET SDK Ricerca di Azure, vedere le [Procedure .NET](search-howto-dotnet-sdk.md).

I commenti degli utenti sull'SDK saranno molto apprezzati. Se si verificano problemi, sentitevi liberi di chiederci aiuto su [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Verificare di avere anteposto al titolo del problema il prefisso "[Ricerca di Azure]".

Grazie per avere usato Ricerca di Azure.
