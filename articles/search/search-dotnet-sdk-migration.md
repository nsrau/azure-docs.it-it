---
title: Aggiornamento a .NET SDK Ricerca di Azure versione 3 | Documentazione Microsoft
description: Aggiornamento ad Azure Search .NET SDK versione 3
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: 48238788e06057ccaba41d1d3f500b5c10c93cb7
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Aggiornamento ad Azure Search .NET SDK versione 3
Se si usa la versione 2.0 di anteprima o precedente di [Azure Search .NET SDK](https://aka.ms/search-sdk), questo articolo include informazioni utili per aggiornare l'applicazione per l'uso della versione 3.

Per una procedura dettagliata più generale relativa all'SDK, inclusi gli esempi, vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

La versione 3 di Azure Search .NET SDK contiene alcune modifiche rispetto alle versioni precedenti. ma per lo più secondarie, quindi il codice potrà essere modificato facilmente. Per le istruzioni su come modificare il codice per usare la nuova versione dell'SDK, vedere [Passaggi per eseguire l'aggiornamento](#UpgradeSteps) .

> [!NOTE]
> Se si usa la versione 1.0.2-preview o precedente, è consigliabile eseguire prima l'aggiornamento alla versione 1.1 e quindi l'aggiornamento alla versione 3. Per informazioni, vedere [Aggiornamento a .NET SDK Ricerca di Azure versione 1.1](search-dotnet-sdk-migration-version-1.md).
>
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Novità della versione 3
La versione 3 di Azure Search .NET SDK ha come destinazione la versione più recente disponibile a livello generale dell'API REST di Ricerca di Azure, in particolare la versione 2016-09-01. Questo rende possibile usare molte nuove funzionalità di Ricerca di Azure da un'applicazione .NET, incluse le seguenti:

* [Analizzatori personalizzati](https://aka.ms/customanalyzers)
* Supporto per gli indicizzatori di [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md) e [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
* Personalizzazione dell'indicizzatore tramite [mapping dei campi](search-indexer-field-mappings.md)
* Supporto di eTag per consentire l'aggiornamento simultaneo sicuro di definizioni di indice, indicizzatori e origini dati
* Supporto per la creazione di definizioni dei campi di indice in modo dichiarativo, decorando la classe modello e usando la nuova classe `FieldBuilder`.
* Supporto per .NET Core e .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A seconda della struttura del codice, la ricompilazione potrebbe essere eseguita correttamente. In questo caso è possibile procedere.

Se la compilazione non riesce, viene visualizzato un errore di compilazione simile al seguente:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Il passaggio successivo consiste nel correggere l'errore di compilazione. Vedere [Modifiche di rilievo della versione 3](#ListOfChanges) per informazioni dettagliate sulle cause dell'errore e sulla risoluzione.

È possibile che vengano visualizzati avvisi di compilazione aggiuntivi correlati a proprietà o metodi obsoleti. Gli avvisi indicheranno istruzioni sulle operazioni da eseguire al posto della funzionalità deprecata. Ad esempio, se l'applicazione usa la proprietà `IndexingParameters.Base64EncodeKeys`, dovrebbe essere visualizzato l'avviso `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Dopo avere corretto gli errori di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità nell'SDK sono descritte in dettaglio in [Novità della versione 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Modifiche di rilievo nella versione 3
La versione 3 include poche modifiche di rilievo che potrebbero richiedere modifiche al codice oltre alla ricompilazione dell'applicazione.

### <a name="indexesgetclient-return-type"></a>Tipo restituito Indexes.GetClient
Il metodo `Indexes.GetClient` dispone di un nuovo tipo restituito. In precedenza, veniva restituito `SearchIndexClient`, ma questo valore è stato modificato in `ISearchIndexClient` nella versione 2.0-preview e questa modifica è ancora presente nella versione 3. Serve a supportare i clienti che vogliono simulare il metodo `GetClient` per i test di unità tramite la restituzione di un'implementazione fittizia di `ISearchIndexClient`.

#### <a name="example"></a>Esempio
Se il codice è simile a questo:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

È possibile sostituirlo con questo per correggere gli errori di compilazione:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType e altri tipi non possono più essere convertiti in modo implicito in stringhe
Esistono molti tipi in Azure Search .NET SDK che derivano da `ExtensibleEnum`. In precedenza, questi tipi erano tutti convertibili in modo implicito nel tipo `string`. Tuttavia, è stato rilevato un bug nell'implementazione di `Object.Equals` per queste classi e per correggere il bug è stato necessario disabilitare la conversione implicita. La conversione esplicita in `string` è ancora consentita.

#### <a name="example"></a>Esempio
Se il codice è simile a questo:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

È possibile sostituirlo con questo per correggere gli errori di compilazione:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Rimuovere i membri obsoleti

È possibile che vengano visualizzati errori di compilazione correlati a metodi o proprietà contrassegnati come obsoleti nella versione 2.0-preview e successivamente rimossi nella versione 3. Se si verificano tali errori, ecco come risolverli:

- Se l'errore riguarda il costruttore `ScoringParameter(string name, string value)`, sostituirlo con `ScoringParameter(string name, IEnumerable<string> values)`
- Se l'errore riguarda la proprietà `ScoringParameter.Value`, sostituirla con la proprietà `ScoringParameter.Values` o il metodo `ToString`.
- Se l'errore riguarda la proprietà `SearchRequestOptions.RequestId`, sostituirla con la proprietà `ClientRequestId`.

### <a name="removed-preview-features"></a>Funzionalità di anteprima rimosse

Se esegue l'aggiornamento dalla versione 2.0-preview alla versione 3, tenere presente che è stato rimosso il supporto dell'analisi JSON e CSV per gli indicizzatori BLOB, perché queste funzionalità sono ancora in anteprima. In particolare, sono stati rimossi i metodi seguenti della classe `IndexingParametersExtensions`:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se l'applicazione dipende in modo sostanziale da queste funzionalità, non sarà possibile completare l'aggiornamento alla versione 3 di Azure Search .NET SDK. È possibile continuare a usare la versione 2.0-preview. Tenere presente, tuttavia, che **non è consigliabile usare SDK in anteprima nelle applicazioni di produzione**. Le funzionalità di anteprima sono destinate esclusivamente alla valutazione e sono soggette a modifiche.

## <a name="conclusion"></a>Conclusioni
Per altri dettagli sull'uso di .NET SDK Ricerca di Azure, vedere le [Procedure .NET](search-howto-dotnet-sdk.md).

I commenti degli utenti sull'SDK saranno molto apprezzati. In caso di problemi, è possibile richiedere assistenza nel [forum MSDN su Ricerca di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Verificare di avere anteposto al titolo del problema il prefisso "[Azure Search]".

Grazie per avere usato Ricerca di Azure.
