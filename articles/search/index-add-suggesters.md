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
ms.openlocfilehash: a6c4051a5b3d557f9ac2927a62492425e7636c0d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113476"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggestions-in-azure-cognitive-search"></a>Creare un suggerimento per abilitare il completamento automatico e i suggerimenti in Ricerca cognitiva di AzureCreate a suggester to enable autocomplete and suggestions in Azure Cognitive Search

In Ricerca cognitiva di Azure la funzionalità "ricerca in base al tipo" o typeahead si basa su un costrutto **del suggerimento** aggiunto a un indice di [ricerca.](search-what-is-an-index.md) Un suggerimento supporta due varianti di ricerca durante la digitazione: *completamento automatico*, che completa il termine o la frase che si sta digitando, e *suggerimenti* che restituiscono un breve elenco di documenti corrispondenti.  

La schermata seguente, dall'esempio [Creare la prima app in C ,](tutorial-csharp-type-ahead-and-suggestions.md) illustra entrambe le esperienze. Completamento automatico anticipa ciò che l'utente potrebbe digitare, finendo "tw" con "in" come termine di ricerca potenziale. I suggerimenti sono risultati di ricerca effettivi, ognuno dei quali rappresenta un documento corrispondente. Per suggerimenti, è possibile visualizzare qualsiasi parte di un documento che meglio descrive il risultato. In questo esempio, i suggerimenti sono rappresentati dal campo del nome dell'hotel.

![Confronto visivo del completamento automatico e delle query suggerite](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Confronto visivo del completamento automatico e delle query suggerite")

Per implementare questi comportamenti in Ricerca cognitiva di Azure, è disponibile un componente di indicizzazione e query. 

+ Nell'indice aggiungere un suggerimento a un indice. È possibile utilizzare il portale, [l'API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)o [.NET SDK.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) Il resto di questo articolo è incentrato sulla creazione di un suggerimento.

+ Nella richiesta di query chiamare una delle [API elencate di seguito.](#how-to-use-a-suggester)

Il supporto search-as-you-type è abilitato in base al campo. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si desidera un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta di *documenti* di indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="what-is-a-suggester"></a>Che cos'è un suggeritore?

Un suggerimento è una struttura di dati che supporta i comportamenti di ricerca come si digitaarchiviano i prefissi per la corrispondenza nelle query parziali. Analogamente ai termini in formato token, i prefissi vengono archiviati in indici invertiti, uno per ogni campo specificato in una raccolta di campi del suggerimento.

Quando si creano prefissi, un suggerimento ha una propria catena di analisi, simile a quella utilizzata per la ricerca full-text. Tuttavia, a differenza dell'analisi nella ricerca full-text, un suggerimento può utilizzare solo analizzatori predefiniti (Lucene standard, [analizzatori del linguaggio](index-add-language-analyzers.md)o altri analizzatori nell'elenco [degli analizzatori predefiniti.](index-add-custom-analyzers.md#predefined-analyzers-reference) [Gli analizzatori](index-add-custom-analyzers.md) e le configurazioni personalizzati non sono specificamente consentiti per evitare configurazioni casuali che producono risultati scadenti.

> [!NOTE]
> Se è necessario aggirare il vincolo dell'analizzatore, utilizzare due campi separati per lo stesso contenuto. Ciò consentirà a uno dei campi di avere un suggerimento, mentre l'altro può essere impostato con una configurazione analizzatore personalizzata.

## <a name="create-a-suggester"></a>Creare uno strumento suggerimenti

Anche se un suggerimento ha diverse proprietà, è principalmente una raccolta di campi per i quali si sta abilitando un'esperienza typeahead. Per un'app di viaggio, ad esempio, potrebbe essere necessario abilitare la ricerca con completamento automatico su destinazioni, città e attrazioni. Di conseguenza, tutti e tre i campi andrebbero nella raccolta di campi.

Per creare un suggerimento, aggiungerne uno a uno schema di indice. È possibile avere un suggerimento in un indice (in particolare, un suggeritore nella raccolta di suggerimenti).

### <a name="when-to-create-a-suggester"></a>Quando creare un suggerimento

Il momento migliore per creare un suggerimento è quando si crea anche la definizione di campo stesso.

Se si tenta di creare un suggerimento utilizzando campi preesistenti, l'API non lo consentirà. Il testo Typeahead viene creato durante l'indicizzazione, quando i termini parziali in due o più combinazioni di caratteri vengono tokenizzati insieme a termini interi. Dato che i campi esistenti sono già in formato token, sarà necessario ricostruire l'indice se si desidera aggiungerli a un suggerimento. Per altre informazioni sulla reindicizzazione, vedere Come ricostruire un indice di [Ricerca cognitiva](search-howto-reindex.md)di Azure.For more information about reindexing, see How to rebuild an Azure Cognitive Search index .

### <a name="create-using-the-rest-api"></a>Creare usando l'API RESTCreate using the REST API

Nell'API REST aggiungere i suggerimenti tramite [Crea indice](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Aggiorna indice.](https://docs.microsoft.com/rest/api/searchservice/update-index) 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
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
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
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
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. I campi devono `Edm.String` `Collection(Edm.String)`essere di tipo e . Se un analizzatore viene specificato nel campo, deve essere un analizzatore denominato da [questo elenco](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (non un analizzatore personalizzato).<p/>Come procedura consigliata, specificare solo i campi che si prestano a una risposta prevista e appropriata, che si tratti di una stringa completata in una barra di ricerca o in un elenco a discesa.<p/>Un nome di hotel è un buon candidato perché ha precisione. I campi dettagliati come le descrizioni e i commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Negli esempi, includiamo comunque "categoria" per dimostrare che è possibile includere più campi. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Usare un suggerimento in una query

Dopo aver creato un suggerimento, chiamare l'API appropriata nella logica di query per richiamare la funzionalità. 

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
