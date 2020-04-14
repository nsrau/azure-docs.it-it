---
title: Creare uno strumento suggerimenti
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query type-ahead in Ricerca cognitiva di Azure creando suggerimenti e formulando richieste che richiamano termini di query di completamento automatico o suggeriti automaticamente.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d40d4cfe1b86448f1e8df307013905d69f203dcd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261058"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Creare un suggerimento per abilitare il completamento automatico e i risultati suggeriti in una query

In Ricerca cognitiva di Azure la funzione "ricerca come tipo" viene abilitata tramite un costrutto **di suggerimenti** aggiunto a un indice di [ricerca.](search-what-is-an-index.md) Un suggerimento supporta due esperienze: *completamento automatico*, che completa il termine o la frase, e *suggerimenti* che restituiscono un breve elenco di documenti corrispondenti.  

La schermata seguente di [Creare la prima app in C .](tutorial-csharp-type-ahead-and-suggestions.md) Completamento automatico anticipa un termine potenziale, finendo "tw" con "in". I suggerimenti sono risultati di ricerca mini, in cui un campo come il nome dell'hotel rappresenta un documento di ricerca dell'hotel corrispondente dall'indice. Per i suggerimenti, è possibile visualizzare qualsiasi campo che fornisca informazioni descrittive.

![Confronto visivo del completamento automatico e delle query suggerite](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Confronto visivo del completamento automatico e delle query suggerite")

È possibile utilizzare queste funzionalità separatamente o insieme. Per implementare questi comportamenti in Ricerca cognitiva di Azure, è disponibile un componente di indicizzazione e query. 

+ Nell'indice aggiungere un suggerimento a un indice. È possibile utilizzare il portale, [l'API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)o [.NET SDK.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) Il resto di questo articolo è incentrato sulla creazione di un suggerimento.

+ Nella richiesta di query chiamare una delle [API elencate di seguito.](#how-to-use-a-suggester)

Il supporto search-as-you-type è abilitato in base al campo per i campi stringa. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si desidera un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta di *documenti* di indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="what-is-a-suggester"></a>Che cos'è un suggeritore?

Un suggerimento è una struttura di dati che supporta i comportamenti di ricerca come si digitaarchiviano i prefissi per la corrispondenza nelle query parziali. Analogamente ai termini in formato token, i prefissi vengono archiviati in indici invertiti, uno per ogni campo specificato in una raccolta di campi del suggerimento.

Quando si creano prefissi, un suggerimento ha una propria catena di analisi, simile a quella utilizzata per la ricerca full-text. Tuttavia, a differenza dell'analisi nella ricerca full-text, un suggerimento può operare solo su campi che utilizzano l'analizzatore Lucene standard (impostazione predefinita) o un [analizzatore di lingua](index-add-language-analyzers.md). I campi che [utilizzano analizzatori personalizzati](index-add-custom-analyzers.md) o [analizzatori predefiniti](index-add-custom-analyzers.md#predefined-analyzers-reference) (ad eccezione dello standard Lucene) non sono esplicitamente autorizzati a evitare risultati scadenti.

> [!NOTE]
> Se è necessario aggirare il vincolo dell'analizzatore, utilizzare due campi separati per lo stesso contenuto. Ciò consentirà a uno dei campi di avere un suggerimento, mentre l'altro può essere impostato con una configurazione analizzatore personalizzata.

## <a name="define-a-suggester"></a>Definire un suggerimento

Anche se un suggerimento ha diverse proprietà, è principalmente una raccolta di campi per i quali si sta abilitando un'esperienza di ricerca durante la digitazione. Ad esempio, un'app di viaggio potrebbe voler abilitare il completamento automatico in destinazioni, città e attrazioni. Di conseguenza, tutti e tre i campi andrebbero nella raccolta di campi.

Per creare un suggerimento, aggiungerne uno a uno schema di indice. È possibile avere un suggerimento in un indice (in particolare, un suggeritore nella raccolta di suggerimenti). Un suggerimento accetta un elenco di campi. 

+ Per i suggerimenti, scegliere i campi che meglio rappresentano un singolo risultato. I nomi, i titoli o altri campi univoci che distinguono tra i documenti funzionano meglio. Se i campi sono costituiti da valori simili o identici, i suggerimenti saranno composti da risultati identici e un utente non saprà su quale fare clic.

+ Assicurarsi che ogni campo `sourceFields` nell'elenco dei suggerimenti utilizzi`"analyzer": null`l'analizzatore Lucene `"analyzer": "en.Microsoft"`standard predefinito ( ) o un [analizzatore di linguaggio](index-add-language-analyzers.md) (ad esempio, ). 

  La scelta di un analizzatore determina il modo in cui i campi vengono tokenizzati e successivamente preceduti. Ad esempio, per una stringa sillabata come "sensibile al contesto", l'utilizzo di un analizzatore di linguaggio comporterà queste combinazioni di token: "context", "sensitive", "context-sensitive". Se avessi usato l'analizzatore Lucene standard, la stringa sillabata non esisterebbe.

> [!TIP]
> Prendi in considerazione l'uso [dell'API Analizza testo](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) per informazioni dettagliate su come i termini vengono suddivisi in token e successivamente con il prefisso. Dopo aver creato un indice, è possibile provare vari analizzatori su una stringa per visualizzare i token che genera.

### <a name="when-to-create-a-suggester"></a>Quando creare un suggerimento

Il momento migliore per creare un suggerimento è quando si crea anche la definizione di campo stesso.

Se si tenta di creare un suggerimento utilizzando campi preesistenti, l'API non lo consentirà. I prefissi vengono generati durante l'indicizzazione, quando i termini parziali in due o più combinazioni di caratteri vengono tokenizzati insieme a termini interi. Dato che i campi esistenti sono già in formato token, sarà necessario ricostruire l'indice se si desidera aggiungerli a un suggerimento. Per altre informazioni, vedere [Come ricompilare un indice di Ricerca cognitiva](search-howto-reindex.md)di Azure.For more information, see How to rebuild an Azure Cognitive Search index.

### <a name="create-using-the-rest-api"></a>Creare usando l'API RESTCreate using the REST API

Nell'API REST aggiungere i suggerimenti tramite [Crea indice](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Aggiorna indice.](https://docs.microsoft.com/rest/api/searchservice/update-index) 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

### <a name="create-using-the-net-sdk"></a>Creare utilizzando .NET SDK

Definire un oggetto [Suggester](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)nel linguaggio C. `Suggesters`è una raccolta, ma può richiedere un solo elemento. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

|Proprietà      |Descrizione      |
|--------------|-----------------|
|`name`        |Nome dello strumento suggerimenti.|
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. I campi devono `Edm.String` `Collection(Edm.String)`essere di tipo e . Se un analizzatore viene specificato nel campo, deve essere un analizzatore denominato da [questo elenco](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (non un analizzatore personalizzato).<p/> Come procedura consigliata, specificare solo i campi che si prestano a una risposta prevista e appropriata, che si tratti di una stringa completata in una barra di ricerca o in un elenco a discesa.<p/>Un nome di hotel è un buon candidato perché ha precisione. I campi dettagliati come le descrizioni e i commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Negli esempi, includiamo comunque "categoria" per dimostrare che è possibile includere più campi. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Utilizzare un suggerimento

In una query viene utilizzato un suggerimento. Dopo aver creato un suggerimento, chiamare l'API appropriata nella logica di query per richiamare la funzionalità. 

+ [API REST dei suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync (metodo)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metodo AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

L'utilizzo dell'API è illustrato nella chiamata seguente all'API REST di completamento automatico. Questo esempio è tratto da due asporto. In primo luogo, come con tutte le query, l'operazione è contro la raccolta di documenti di un indice. In secondo luogo, è possibile aggiungere parametri di query. Sebbene molti parametri di query siano comuni a entrambe le API, l'elenco è diverso per ognuno di essi.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Se un suggerimento non è definito nell'indice, una chiamata al completamento automatico o i suggerimenti avranno esito negativo.

## <a name="sample-code"></a>Codice di esempio

+ [Per creare la prima app nell'esempio in C,](tutorial-csharp-type-ahead-and-suggestions.md) vengono illustrate la costruzione di un suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice viene eseguito in un servizio di ricerca cognitiva di Azure sandbox e usa un indice Hotels precaricato in modo che tutto ciò che devi fare è premere F5 per eseguire l'applicazione. Non è necessario alcun abbonamento o accesso.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) è un esempio meno recente che contiene sia il codice C , che java. Vengono inoltre illustrate la costruzione di un suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. In questo esempio di codice vengono utilizzati i dati di esempio [DI NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) ospitati. 

## <a name="next-steps"></a>Passaggi successivi

Si consiglia di vedere come vengono formulate le richieste.

> [!div class="nextstepaction"]
> [Suggerimenti ed esempi di completamento automatico](search-autocomplete-tutorial.md) 
