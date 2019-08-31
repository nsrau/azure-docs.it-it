---
title: Eseguire l'aggiornamento ad Azure search .NET SDK versione 9-ricerca di Azure
description: Eseguire la migrazione del codice in ricerca di Azure .NET SDK versione 9 da versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 32908ab209cbe05a0acf9da896e1e1fb11e6f5dd
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183235"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>Eseguire l'aggiornamento a .NET SDK di ricerca di Azure versione 9

Se si usa la versione 7,0-Preview o precedente di [Azure search .NET SDK](https://aka.ms/search-sdk), questo articolo consentirà di aggiornare l'applicazione per l'uso della versione 9.

> [!NOTE]
> Se si vuole usare la versione 8,0-Preview per valutare le funzionalità che non sono ancora disponibili a livello generale, è anche possibile seguire le istruzioni riportate in questo articolo per eseguire l'aggiornamento a 8,0-Preview da versioni precedenti.

Per una procedura dettagliata più generale relativa all'SDK, inclusi gli esempi, vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

La versione 9 di Azure search .NET SDK contiene molte modifiche rispetto alle versioni precedenti. Alcune di queste sono modifiche di rilievo, ma dovrebbero richiedere solo modifiche relativamente minime al codice. Per le istruzioni su come modificare il codice per usare la nuova versione dell'SDK, vedere [Passaggi per eseguire l'aggiornamento](#UpgradeSteps) .

> [!NOTE]
> Se si usa la versione 4,0-Preview o precedente, è consigliabile eseguire prima l'aggiornamento alla versione 5, quindi eseguire l'aggiornamento alla versione 9. Per istruzioni, vedere [aggiornamento a .NET SDK di ricerca di Azure versione 5](search-dotnet-sdk-migration-version-5.md) .
>
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Novità della versione 9
La versione 9 di Azure search .NET SDK è destinata alla versione più recente disponibile a livello generale dell'API REST di ricerca di Azure, in particolare 2019-05-06. Questo rende possibile usare le nuove funzionalità di Ricerca di Azure da un'applicazione .NET, incluse le seguenti:

* La [ricerca cognitiva](cognitive-search-concept-intro.md) è una funzionalità di intelligenza artificiale in ricerca di Azure, usata per estrarre il testo da immagini, BLOB e altre origini dati non strutturate, arricchire il contenuto per renderlo più ricercabile in un indice di ricerca di Azure.
* Il supporto per i [tipi complessi](search-howto-complex-data-types.md) consente di modellare praticamente qualsiasi struttura JSON annidata in un indice di ricerca di Azure.
* Il [completamento automatico](search-autocomplete-tutorial.md) offre un'alternativa all'API **suggest** per l'implementazione di un comportamento di tipo ricerca in quanto si digita. Completamento automatico "completa" la parola o la frase che un utente sta digitando.
* La [modalità di analisi JsonLines](search-howto-index-json-blobs.md), parte dell'indicizzazione BLOB di Azure, crea un documento di ricerca per entità JSON separato da una nuova riga.

### <a name="new-preview-features-in-version-80-preview"></a>Nuove funzionalità di anteprima nella versione 8,0-Preview
Versione 8,0-Preview di Azure search .NET SDK targets API versione 2017-11-11-Preview. Questa versione include tutte le stesse funzionalità della versione 9, oltre a:

* [Chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) per la crittografia del lato servizio-inattivo è una nuova funzionalità di anteprima. Oltre alla crittografia inattiva integrata gestita da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui l'utente è l'unico proprietario delle chiavi.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A seconda della struttura del codice, la ricompilazione potrebbe essere eseguita correttamente. In questo caso è possibile procedere.

Se la compilazione non riesce, sarà necessario correggere ogni errore di compilazione. Per informazioni dettagliate su come risolvere ogni potenziale errore di compilazione, vedere [modifiche di rilievo nella versione 9](#ListOfChanges) .

È possibile che vengano visualizzati avvisi di compilazione aggiuntivi correlati a proprietà o metodi obsoleti. Gli avvisi indicheranno istruzioni sulle operazioni da eseguire al posto della funzionalità deprecata. Se, ad esempio, l'applicazione usa `DataSourceType.DocumentDb` la proprietà, verrà visualizzato un avviso che indica che questo membro è deprecato. Usare invece CosmosDb ".

Dopo avere corretto eventuali errori o avvisi di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità nell'SDK sono descritte in dettaglio in novità della [versione 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Modifiche di rilievo nella versione 9

Sono presenti diverse modifiche di rilievo nella versione 9 che possono richiedere modifiche al codice oltre alla ricompilazione dell'applicazione.

> [!NOTE]
> L'elenco delle modifiche seguenti non è esaustivo. Alcune modifiche probabilmente non comporteranno errori di compilazione, ma sono tecnicamente in pausa poiché interrompono la compatibilità binaria con gli assembly che dipendono da versioni precedenti degli assembly .NET SDK di ricerca di Azure. Tali modifiche non sono elencate di seguito. Ricompilare l'applicazione quando si esegue l'aggiornamento alla versione 9 per evitare eventuali problemi di compatibilità binaria.

### <a name="immutable-properties"></a>Proprietà non modificabili

Le proprietà pubbliche di diverse classi di modelli sono ora non modificabili. Se è necessario creare istanze personalizzate di queste classi per il test, è possibile usare i nuovi costruttori con parametri:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Modifiche al campo

La `Field` classe è cambiata ora che può anche rappresentare campi complessi.

Le seguenti `bool` proprietà ora ammettono valori null:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Questa situazione è dovuta al fatto che `null` queste proprietà devono ora trovarsi nel caso di campi complessi. Se si dispone di codice che legge queste proprietà, è necessario prepararsi per la `null`gestione. Si noti che tutte le altre `Field` proprietà di sono sempre state e continuano a essere nullable e alcune di queste saranno `null` anche nel caso di campi complessi, in particolare quanto segue:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Il costruttore senza parametri di `Field` è stato creato `internal`. Da questo momento in poi `Field` , ogni richiede un nome esplicito e un tipo di dati al momento della costruzione.

### <a name="simplified-batch-and-results-types"></a>Tipi di batch e di risultati semplificati

Nella versione 7,0-Preview e versioni precedenti, le varie classi che incapsulano gruppi di documenti sono state strutturate in gerarchie di classi parallele:

  -  `DocumentSearchResult`e `DocumentSearchResult<T>` ereditato da`DocumentSearchResultBase`
  -  `DocumentSuggestResult`e `DocumentSuggestResult<T>` ereditato da`DocumentSuggestResultBase`
  -  `IndexAction`e `IndexAction<T>` ereditato da`IndexActionBase`
  -  `IndexBatch`e `IndexBatch<T>` ereditato da`IndexBatchBase`
  -  `SearchResult`e `SearchResult<T>` ereditato da`SearchResultBase`
  -  `SuggestResult`e `SuggestResult<T>` ereditato da`SuggestResultBase`

I tipi derivati senza un parametro di tipo generico devono essere utilizzati in scenari con tipizzazione dinamica e si presuppone l' `Document` utilizzo del tipo.

A partire dalla versione 8,0-Preview, le classi base e le classi derivate non generiche sono state rimosse. Per gli scenari tipizzati in modo dinamico, è `IndexBatch<Document>`possibile `DocumentSearchResult<Document>`utilizzare, e così via.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum rimosso

La `ExtensibleEnum` classe base è stata rimossa. Tutte le classi che lo derivano sono ora struct, ad `AnalyzerName`esempio `DataType`, e `DataSourceType` . Anche `Create` i metodi sono stati rimossi. È possibile rimuovere solo le chiamate `Create` a perché questi tipi sono convertibili in modo implicito da stringhe. Se il risultato è un errore del compilatore, è possibile richiamare in modo esplicito l'operatore di conversione tramite il cast per evitare ambiguità tra i tipi. Ad esempio, è possibile modificare il codice come segue:

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

Le proprietà che contenevano valori facoltativi di questi tipi sono ora tipizzate in modo esplicito come Nullable, quindi continuano a essere facoltative.

### <a name="removed-facetresults-and-hithighlights"></a>Rimossi FacetResults e HitHighlights

Le `FacetResults` classi `HitHighlights` e sono state rimosse. I risultati dei facet sono `IDictionary<string, IList<FacetResult>>` ora tipizzati come `IDictionary<string, IList<string>>`e vengono evidenziati come. Un modo rapido per risolvere gli errori di compilazione introdotti da questa modifica `using` consiste nell'aggiungere alias nella parte superiore di ogni file che usa i tipi rimossi. Ad esempio:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Passa a SynonymMap 

Il costruttore `SynonymMap` non ha più un parametro `enum` per `SynonymMapFormat`. Tale enum aveva un solo valore ed era quindi ridondante. Se in conseguenza di ciò vengono visualizzati errori di compilazione, è sufficiente rimuovere i riferimenti al parametro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Modifiche alle classi di modelli varie

La `AutocompleteMode` proprietà di `AutocompleteParameters` non è più Nullable. Se si dispone di codice che assegna questa proprietà a `null`, è possibile rimuoverlo semplicemente e la proprietà verrà inizializzata automaticamente sul valore predefinito.

L'ordine dei parametri per il `IndexAction` costruttore è stato modificato ora che questo costruttore viene generato automaticamente. Anziché utilizzare il costruttore, è consigliabile utilizzare i metodi `IndexAction.Upload`Factory, `IndexAction.Merge`e così via.

### <a name="removed-preview-features"></a>Funzionalità di anteprima rimosse

Se si esegue l'aggiornamento dalla versione 8,0-Preview alla versione 9, tenere presente che la crittografia con chiavi gestite dal cliente è stata rimossa perché questa funzionalità è ancora in anteprima. In particolare, `EncryptionKey` le proprietà `Index` di `SynonymMap` e sono state rimosse.

Se l'applicazione ha una dipendenza rigida da questa funzionalità, non sarà possibile eseguire l'aggiornamento alla versione 9 di Azure search .NET SDK. È possibile continuare a usare la versione 8,0-Preview. Tenere presente, tuttavia, che **non è consigliabile usare SDK in anteprima nelle applicazioni di produzione**. Le funzionalità di anteprima sono destinate esclusivamente alla valutazione e sono soggette a modifiche.

> [!NOTE]
> Se sono stati creati indici crittografati o mappe sinonimo usando la versione 8,0-Preview dell'SDK, sarà comunque possibile usarli e modificarne le definizioni usando la versione 9 dell'SDK senza influire negativamente sullo stato della crittografia. La versione 9 dell'SDK non invierà la `encryptionKey` proprietà all'API REST e, di conseguenza, l'API REST non modificherà lo stato della crittografia della risorsa. 

### <a name="behavioral-change-in-data-retrieval"></a>Modifica comportamentale del recupero dei dati

Se si usano le API "tipizzate in modo `Search`dinamico `Suggest`", `Get` o che restituiscono istanze di `Document`tipo, tenere presente che `string[]`ora deserializzano matrici JSON vuote in `object[]` anziché.

## <a name="conclusion"></a>Conclusione
Per altri dettagli sull'uso di .NET SDK Ricerca di Azure, vedere le [Procedure .NET](search-howto-dotnet-sdk.md).

I commenti degli utenti sull'SDK saranno molto apprezzati. Se si verificano problemi, è possibile richiedere assistenza per [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Verificare di avere anteposto al titolo del problema il prefisso "[Ricerca di Azure]".

Grazie per avere usato Ricerca di Azure.
