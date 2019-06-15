---
title: Eseguire l'aggiornamento a Azure Search .NET SDK versione 9 - ricerca di Azure
description: Eseguire la migrazione di codice ad Azure Search .NET SDK versione 9 da versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a59deed4ac0cec669ddc5e0335f7274586c702e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541771"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>Eseguire l'aggiornamento a Azure Search .NET SDK versione 9

Se si usa versione 7.0-preview o precedente del [Azure Search .NET SDK](https://aka.ms/search-sdk), questo articolo consente di aggiornare l'applicazione per usare la versione 9.

> [!NOTE]
> Se si desidera utilizzare versione 8.0 per l'anteprima per valutare le funzionalità che non sono ancora disponibili a livello generale, è anche possibile seguire le istruzioni in questo articolo per eseguire l'aggiornamento all'anteprima di 8.0 rispetto alle versioni precedenti.

Per una procedura dettagliata più generale relativa all'SDK, inclusi gli esempi, vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

Versione 9 di Azure Search .NET SDK contiene molte modifiche rispetto alle versioni precedenti. Alcune di queste sono modifiche di rilievo, ma richiedono solo relativamente piccole modifiche al codice. Per le istruzioni su come modificare il codice per usare la nuova versione dell'SDK, vedere [Passaggi per eseguire l'aggiornamento](#UpgradeSteps) .

> [!NOTE]
> Se si usa versione 4.0 di anteprima o precedente, dovrebbe innanzitutto l'aggiornamento alla versione 5 e quindi eseguire l'aggiornamento alla versione 9. Visualizzare [l'aggiornamento a Azure Search .NET SDK versione 5](search-dotnet-sdk-migration-version-5.md) per le istruzioni.
>
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Quali sono le novità nella versione 9
Versione 9 di Azure Search .NET SDK è destinato l'ultima versione disponibile a livello generale dell'API REST ricerca di Azure, in particolare 2019-05-06. Questo rende possibile usare le nuove funzionalità di Ricerca di Azure da un'applicazione .NET, incluse le seguenti:

* [Ricerca cognitiva](cognitive-search-concept-intro.md) è una funzionalità di intelligenza artificiale in ricerca di Azure usato per estrarre il testo da immagini, BLOB e altre origini dati non strutturati - arricchimento il contenuto per renderlo più ricercabile in un indice di ricerca di Azure.
* Supporto per [i tipi complessi](search-howto-complex-data-types.md) consente di modellare quasi ogni struttura JSON nidificata in un indice di ricerca di Azure.
* [Completamento automatico](search-autocomplete-tutorial.md) offre un'alternativa per il **Suggerisci** API per l'implementazione del comportamento di ricerca---digitazione. Completamento automatico "completa" parola o frase che un utente è attualmente la digitazione.
* [Modalità di analisi JsonLines](search-howto-index-json-blobs.md), parte del Blob di Azure l'indicizzazione, crea un documento di ricerca per ogni entità JSON che è separata da un carattere di nuova riga.

### <a name="new-preview-features-in-version-80-preview"></a>Nuove funzionalità di anteprima nella versione 8.0-preview
Versione 8.0 per l'anteprima di Azure Search .NET SDK è destinato a API versione 2017-11-11-Preview. Questa versione include le stesse funzionalità della versione 9, oltre a:

* [Le chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) per il lato del servizio la crittografia dati inattivi sono una nuova funzionalità in anteprima. Oltre l'incorporati crittografia inattivi gestito da Microsoft, è possibile applicare un ulteriore livello di crittografia in cui si è l'unica proprietaria dei tasti.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A seconda della struttura del codice, la ricompilazione potrebbe essere eseguita correttamente. In questo caso è possibile procedere.

Se la compilazione non riesce, è necessario risolvere ogni errore di compilazione. Visualizzare [modifiche di rilievo nella versione 9](#ListOfChanges) per informazioni dettagliate su come risolvere ogni potenziale errore di compilazione.

È possibile che vengano visualizzati avvisi di compilazione aggiuntivi correlati a proprietà o metodi obsoleti. Gli avvisi indicheranno istruzioni sulle operazioni da eseguire al posto della funzionalità deprecata. Ad esempio, se l'applicazione usa il `DataSourceType.DocumentDb` proprietà, è necessario ottenere un messaggio di avviso con la dicitura "questo membro è deprecato. Usare COSMOS DB invece".

Dopo avere corretto eventuali errori o avvisi di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Nuove funzionalità nel SDK sono descritti in dettaglio [quali sono le novità nella versione 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Modifiche di rilievo nella versione 9

Sono disponibili numerose modifiche di rilievo nella versione 9 che potrebbe richiedere modifiche al codice oltre alla ricompilazione dell'applicazione.

> [!NOTE]
> L'elenco delle modifiche seguente non è completo. Alcune modifiche probabilmente non genererà errori di compilazione, ma sono tecnicamente rilievo dal momento che interrompono la compatibilità binaria con gli assembly che dipendono da versioni precedenti degli assembly di Azure Search .NET SDK. Tali modifiche non sono elencate di seguito. Ricompilare l'applicazione durante l'aggiornamento alla versione 9 per evitare eventuali problemi di compatibilità binaria.

### <a name="immutable-properties"></a>Proprietà non modificabili

Le proprietà pubbliche di diverse classi modello sono ora modificabili. Se è necessario creare istanze personalizzate di queste classi per il test, è possibile usare i nuovi costruttori con parametri:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Modifiche apportate al campo

Il `Field` classe è stata modificata a questo punto che può anche rappresentare campi complessi.

Nell'esempio `bool` proprietà ora sono nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Infatti queste proprietà devono essere `null` nel caso di campi complessi. Se si dispone di codice che legge queste proprietà, deve essere preparati a gestire `null`. Si noti che tutte le altre proprietà della `Field` sono sempre state e continuano a essere nullable, e anche alcune di esse sarà `null` nel caso di campi complessi, in particolare quanto segue:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Il costruttore senza parametri del `Field` apportata `internal`. D'ora in ogni `Field` richiede un nome e i dati di tipo esplicito al momento della costruzione.

### <a name="simplified-batch-and-results-types"></a>Tipi di batch e risultati semplificati

Nella versione 7.0-preview e versioni precedenti, le varie classi che incapsulano i gruppi di documenti sono state strutturate in gerarchie di classi parallele:

  -  `DocumentSearchResult` e `DocumentSearchResult<T>` ereditata da `DocumentSearchResultBase`
  -  `DocumentSuggestResult` e `DocumentSuggestResult<T>` ereditata da `DocumentSuggestResultBase`
  -  `IndexAction` e `IndexAction<T>` ereditata da `IndexActionBase`
  -  `IndexBatch` e `IndexBatch<T>` ereditata da `IndexBatchBase`
  -  `SearchResult` e `SearchResult<T>` ereditata da `SearchResultBase`
  -  `SuggestResult` e `SuggestResult<T>` ereditata da `SuggestResultBase`

I tipi derivati senza un parametro di tipo generico sono stato concepiti per essere utilizzate negli scenari di "tipizzate in modo dinamico" e presuppone l'utilizzo del `Document` tipo.

A partire da versione 8.0-preview, le classi base e le classi derivate non generica tutti rimossi. Per gli scenari tipizzate in modo dinamico, è possibile usare `IndexBatch<Document>`, `DocumentSearchResult<Document>`e così via.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum rimosso

Il `ExtensibleEnum` classe di base è stato rimosso. Tutte le classi derivate da quest'ultimo sono ora gli struct, ad esempio `AnalyzerName`, `DataType`, e `DataSourceType` , ad esempio. Loro `Create` metodi sono inoltre state rimosse. È possibile rimuovere le chiamate a `Create` poiché questi tipi sono implicitamente convertibili da stringhe. Se che genera errori del compilatore, è possibile richiamare in modo esplicito l'operatore di conversione tramite il cast per evitare ambiguità tra i tipi. Ad esempio, è possibile modificare il codice simile al seguente:

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

Le proprietà che conteneva valori facoltativi di questi tipi ora vengono tipizzate in modo esplicito come ammette valori null in modo che continuino a essere facoltativo.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults rimosso e HitHighlights

Il `FacetResults` e `HitHighlights` classi sono state rimosse. I risultati di facet ora vengono tipizzati come `IDictionary<string, IList<FacetResult>>` e premere evidenzia come `IDictionary<string, IList<string>>`. Un modo rapido per risolvere gli errori di compilazione dovuti a questa modifica consiste nell'aggiungere `using` gli alias nella parte superiore di ogni file che usa tipi rimossi. Ad esempio:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Impostare su SynonymMap 

Il costruttore `SynonymMap` non ha più un parametro `enum` per `SynonymMapFormat`. Tale enum aveva un solo valore ed era quindi ridondante. Se in conseguenza di ciò vengono visualizzati errori di compilazione, è sufficiente rimuovere i riferimenti al parametro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Modifiche alle classi di modello esterno

Il `AutocompleteMode` proprietà di `AutocompleteParameters` non è più che ammette valori null. Se si dispone di codice che assegna questa proprietà su `null`, è semplicemente possibile rimuoverla e la proprietà verrà inizializzata automaticamente sul valore predefinito.

L'ordine dei parametri per il `IndexAction` costruttore è stato modificato dopo che questo costruttore è stato generato automaticamente. Invece di usare il costruttore, è consigliabile usare i metodi factory `IndexAction.Upload`, `IndexAction.Merge`e così via.

### <a name="removed-preview-features"></a>Funzionalità di anteprima rimosse

Se esegue l'aggiornamento dalla versione 8.0-preview alla versione 9, tenere presente che la crittografia con chiavi gestite dal cliente rimossa poiché questa funzionalità è ancora in anteprima. In particolare, il `EncryptionKey` delle proprietà di `Index` e `SynonymMap` sono state rimosse.

Se l'applicazione presenta una dipendenza rigida su questa funzionalità, non sarà in grado di eseguire l'aggiornamento alla versione 9 di Azure Search .NET SDK. È possibile continuare a usare versione 8.0-preview. Tenere presente, tuttavia, che **non è consigliabile usare SDK in anteprima nelle applicazioni di produzione**. Le funzionalità di anteprima sono destinate esclusivamente alla valutazione e sono soggette a modifiche.

> [!NOTE]
> Se è stato creato crittografato indici o sinonimo mappe con versione 8.0-preview del SDK, sarà comunque in grado di usarli e modificarne le definizioni Usa la versione 9 del SDK senza influire negativamente sul relativo stato di crittografia. Versione 9 del SDK non invierà il `encryptionKey` proprietà per l'API REST e di conseguenza l'API REST non modificherà lo stato della crittografia della risorsa. 

### <a name="behavioral-change-in-data-retrieval"></a>Modifica comportamentale il recupero dei dati

Se si usa "dinamicamente tipizzata" `Search`, `Suggest`, o `Get` le API che restituiscono istanze di tipo `Document`, tenere presente che sono ora deserializzare matrici JSON vuote `object[]` invece di `string[]`.

## <a name="conclusion"></a>Conclusioni
Per altri dettagli sull'uso di .NET SDK Ricerca di Azure, vedere le [Procedure .NET](search-howto-dotnet-sdk.md).

I commenti degli utenti sull'SDK saranno molto apprezzati. Se si verificano problemi, è possibile richiedere assistenza sul [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Verificare di avere anteposto al titolo del problema il prefisso "[Ricerca di Azure]".

Grazie per avere usato Ricerca di Azure.
