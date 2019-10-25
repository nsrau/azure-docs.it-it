---
title: Aggiungere query typeahead a un indice
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query di tipo Ahead in Azure ricerca cognitiva creando suggerimenti e formulando richieste che richiamano i termini di query con completamento automatico o con suggerimenti automatici.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790114"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Aggiungere suggerimenti a un indice per typeahead in Azure ricerca cognitiva

In ricerca cognitiva di Azure, la funzionalità "Cerca in base al tipo" o typeahead si basa su un costrutto del **suggeritore** aggiunto a un [indice di ricerca](search-what-is-an-index.md). Si tratta di un elenco di uno o più campi per i quali si vuole abilitare typeahead.

Un suggerimento supporta due varianti di typeahead: *completamento automatico*, che completa il termine o la frase che si sta digitando e *suggerimenti* che restituiscono un breve elenco di documenti corrispondenti.  

La schermata seguente, dall'esempio [creare la prima app in C# ](tutorial-csharp-type-ahead-and-suggestions.md) , illustra typeahead. Il completamento automatico prevede le informazioni che l'utente potrebbe digitare nella casella di ricerca. L'input effettivo è "TW", il cui completamento automatico termina con "in", che viene risolto come "gemello" come termine di ricerca potenziale. I suggerimenti vengono visualizzati nell'elenco a discesa. Per i suggerimenti, è possibile esporre qualsiasi parte di un documento che meglio descrive il risultato. In questo esempio, i suggerimenti sono nomi di Hotel. 

![Confronto visivo tra completamento automatico e query suggerite](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Confronto visivo tra completamento automatico e query suggerite")

Per implementare questi comportamenti in Azure ricerca cognitiva, è disponibile un componente di query e di indice. 

+ Nell'indice aggiungere un componente di suggerimento a un indice. È possibile usare il portale, l' [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Il resto di questo articolo è incentrato sulla creazione di un suggerimento. 

+ Nella richiesta di query, chiamare una delle [API elencate di seguito](#how-to-use-a-suggester).

Il supporto per la ricerca in base al tipo è abilitato per ogni singolo campo. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si vuole un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta *Documents* di un indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="create-a-suggester"></a>Creare uno strumento suggerimenti

Sebbene un suggerimento abbia diverse proprietà, è principalmente una raccolta di campi per i quali si Abilita un'esperienza di typeahead. Per un'app di viaggio, ad esempio, potrebbe essere necessario abilitare la ricerca con completamento automatico su destinazioni, città e attrazioni. Di conseguenza, tutti e tre i campi vengono inseriti nella raccolta Fields.

Per creare un componente di suggerimento, aggiungerne uno a uno schema di indice. È possibile avere un suggerimento in un indice (in particolare un suggerimento nella raccolta suggesters). 

### <a name="when-to-create-a-suggester"></a>Quando creare un suggerimento

Il momento migliore per creare un suggerimento è quando si crea anche la definizione di campo.

Se si tenta di creare un suggerimento usando i campi preesistenti, l'API non lo consentirà. Il testo typeahead viene creato durante l'indicizzazione, quando i termini parziali in due o più combinazioni di caratteri vengono suddivisi in token insieme a termini interi. Dato che i campi esistenti sono già in formato token, sarà necessario ricompilare l'indice se si desidera aggiungerli a un suggerimento. Per ulteriori informazioni sulla reindicizzazione, vedere [How to rebuild an Azure ricerca cognitiva index](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Creare con l'API REST

Nell'API REST aggiungere i suggerimenti tramite [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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


### <a name="create-using-the-net-sdk"></a>Creare con .NET SDK

In C#definire un [oggetto del suggerimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` è una raccolta ma può assumere solo un elemento. 

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

|Proprietà      |Description      |
|--------------|-----------------|
|`name`        |Nome del suggerimento.|
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. I campi devono essere di tipo `Edm.String` e `Collection(Edm.String)`. Se un analizzatore viene specificato nel campo, deve essere un analizzatore denominato da [questo elenco](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (non un analizzatore personalizzato).<p/>Come procedura consigliata, specificare solo i campi che si prestano a una risposta prevista e appropriata, indipendentemente dal fatto che si tratti di una stringa completata in una barra di ricerca o in un elenco a discesa.<p/>Il nome di un hotel è un buon candidato perché ha una precisione. I campi dettagliati come descrizioni e commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Gli esempi includono "Category" per dimostrare che è possibile includere più campi. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Restrizioni dell'analizzatore per sourceFields in un suggerimento

Azure ricerca cognitiva analizza il contenuto del campo per consentire l'esecuzione di query su singoli termini. Per i suggerimenti è necessario indicizzare i prefissi oltre ai termini completi, che richiedono un'analisi aggiuntiva sui campi di origine. Le configurazioni dell'analizzatore personalizzato possono combinare uno dei diversi Tokenizer e filtri, spesso in modi che rendono impossibile produrre i prefissi necessari per i suggerimenti. Per questo motivo, Azure ricerca cognitiva impedisce che i campi con analizzatori personalizzati vengano inclusi in un suggerimento.

> [!NOTE] 
>  Se è necessario aggirare la limitazione precedente, usare due campi distinti per lo stesso contenuto. Questo consentirà a uno dei campi di avere un suggerimento, mentre l'altro può essere configurato con una configurazione dell'analizzatore personalizzato.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Usare un suggerimento in una query

Dopo aver creato un suggerimento, chiamare l'API appropriata nella logica di query per richiamare la funzionalità. 

+ [API REST suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metodo SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metodo AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

L'utilizzo delle API è illustrato nella chiamata seguente all'API REST di completamento automatico. Questo esempio presenta due considerazioni. In primo luogo, come per tutte le query, l'operazione viene eseguita sulla raccolta Documents di un indice. In secondo luogo, è possibile aggiungere parametri di query. Sebbene molti parametri di query siano comuni a entrambe le API, l'elenco è diverso per ciascuno di essi.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Se un suggerimento non è definito nell'indice, una chiamata al completamento automatico o ai suggerimenti avrà esito negativo.

## <a name="sample-code"></a>Codice di esempio

+ [Creare la prima app nell' C# ](tutorial-csharp-type-ahead-and-suggestions.md) esempio illustra una costruzione del suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice viene eseguito in un servizio sandbox ricerca cognitiva di Azure e usa un indice degli Alberghi precaricato, quindi è sufficiente premere F5 per eseguire l'applicazione. Non è necessaria alcuna sottoscrizione o accesso.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) è un esempio precedente che contiene C# il codice e Java. Viene inoltre illustrata una costruzione del suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice usa i dati di esempio [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) ospitati. 


## <a name="next-steps"></a>Passaggi successivi

Si consiglia l'esempio seguente per vedere come vengono formulate le richieste.

> [!div class="nextstepaction"]
> [Suggerimenti e esempi di completamento automatico](search-autocomplete-tutorial.md) 
