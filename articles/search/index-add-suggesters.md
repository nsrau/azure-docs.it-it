---
title: Creare uno strumento suggerimenti
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query type-ahead in Ricerca cognitiva di Azure creando suggerimenti e formulando richieste che richiamano termini di query di completamento automatico o suggeriti automaticamente.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 7eb2988628d60fa72c7d83b81a58a1e0fae5de33
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770092"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Creare un suggerimento per abilitare il completamento automatico e i risultati suggeriti in una query

In Ricerca cognitiva di Azure la funzione "ricerca come tipo" viene abilitata tramite un costrutto **di suggerimenti** aggiunto a un indice di [ricerca.](search-what-is-an-index.md) Un suggerimento supporta due esperienze: *completamento automatico*, che completa un input parziale per una query a tutto il termine e *suggerimenti* che invitano a fare clic su di una determinata corrispondenza. Il completamento automatico produce una query. I suggerimenti producono un documento corrispondente.

La schermata seguente di [Creare la prima app in C .](tutorial-csharp-type-ahead-and-suggestions.md) Completamento automatico anticipa un termine potenziale, finendo "tw" con "in". I suggerimenti sono risultati di ricerca mini, in cui un campo come il nome dell'hotel rappresenta un documento di ricerca dell'hotel corrispondente dall'indice. Per i suggerimenti, è possibile visualizzare qualsiasi campo che fornisca informazioni descrittive.

![Confronto visivo del completamento automatico e delle query suggerite](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Confronto visivo del completamento automatico e delle query suggerite")

È possibile utilizzare queste funzionalità separatamente o insieme. Per implementare questi comportamenti in Ricerca cognitiva di Azure, è disponibile un componente di indicizzazione e query. 

+ Nell'indice aggiungere un suggerimento a un indice. È possibile utilizzare il portale, [l'API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)o [.NET SDK.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) Il resto di questo articolo è incentrato sulla creazione di un suggerimento.

+ Nella richiesta di query chiamare una delle [API elencate di seguito.](#how-to-use-a-suggester)

Il supporto search-as-you-type è abilitato in base al campo per i campi stringa. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si desidera un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta di *documenti* di indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="what-is-a-suggester"></a>Che cos'è un suggeritore?

Un suggerimento è una struttura di dati interna che supporta i comportamenti di ricerca durante la digitazione archiviando i prefissi per la corrispondenza nelle query parziali. Come per i termini in formato token, i prefissi vengono archiviati in indici invertiti, uno per ogni campo specificato in una raccolta di campi del suggerimento.

## <a name="define-a-suggester"></a>Definire un suggerimento

Per creare un suggerimento, aggiungerne uno a [uno schema](https://docs.microsoft.com/rest/api/searchservice/create-index) di indice e [impostare ogni proprietà](#property-reference). Il momento migliore per creare un suggerimento è quando si definisce anche il campo che lo utilizzerà.

+ Utilizzare solo campi stringa

+ Utilizzare l'analizzatore`"analyzer": null`Lucene standard predefinito ( `"analyzer": "en.Microsoft"`) o un [analizzatore del linguaggio](index-add-language-analyzers.md) (ad esempio, ) nel campo

### <a name="choose-fields"></a>Selezionare i campi

Anche se un suggerimento dispone di diverse proprietà, è principalmente una raccolta di campi stringa per i quali si sta abilitando un'esperienza di ricerca durante la digitazione. C'è un suggerimento per ogni indice, quindi l'elenco dei suggerimenti deve includere tutti i campi che contribuiscono al contenuto sia per i suggerimenti che per il completamento automatico.

Il completamento automatico trae vantaggio da un pool più ampio di campi da cui attingere perché il contenuto aggiuntivo ha un potenziale di completamento dei termini maggiore.

I suggerimenti, d'altra parte, producono risultati migliori quando la scelta sul campo è selettiva. Tenere presente che il suggerimento è un proxy per un documento di ricerca, pertanto si desidera che i campi che meglio rappresentano un singolo risultato. I nomi, i titoli o altri campi univoci che distinguono tra più corrispondenze funzionano meglio. Se i campi sono costituiti da valori ripetitivi, i suggerimenti sono costituiti da risultati identici e un utente non saprà su quale fare clic.

Per soddisfare entrambe le esperienze di ricerca durante la digitazione, aggiungere tutti i campi necessari per il completamento automatico, ma utilizzare **$select**, **$top**, **$filter e**campi **di ricerca** per controllare i risultati per i suggerimenti.

### <a name="choose-analyzers"></a>Scegliere gli analizzatori

La scelta di un analizzatore determina il modo in cui i campi vengono tokenizzati e successivamente preceduti. Ad esempio, per una stringa sillabata come "sensibile al contesto", l'utilizzo di un analizzatore di linguaggio comporterà queste combinazioni di token: "context", "sensitive", "context-sensitive". Se avessi usato l'analizzatore Lucene standard, la stringa sillabata non esisterebbe. 

Quando si valutano gli analizzatori, è consigliabile usare [l'API Analizza testo](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) per informazioni dettagliate su come i termini vengono suddivisi in token e successivamente con il prefisso. Dopo aver compilato un indice, è possibile provare vari analizzatori su una stringa per visualizzare l'output del token.

I campi che [utilizzano analizzatori personalizzati](index-add-custom-analyzers.md) o [analizzatori predefiniti](index-add-custom-analyzers.md#predefined-analyzers-reference) (ad eccezione dello standard Lucene) non sono esplicitamente autorizzati a evitare risultati scadenti.

> [!NOTE]
> Se è necessario aggirare il vincolo dell'analizzatore, ad esempio se è necessaria una parola chiave o un analizzatore di ngrammi per determinati scenari di query, è necessario utilizzare due campi separati per lo stesso contenuto. Ciò consentirà a uno dei campi di avere un suggerimento, mentre l'altro può essere impostato con una configurazione analizzatore personalizzata.

### <a name="when-to-create-a-suggester"></a>Quando creare un suggerimento

Il momento migliore per creare un suggerimento è quando si crea anche la definizione di campo stesso.

Se si tenta di creare un suggerimento utilizzando campi preesistenti, l'API non lo consentirà. I prefissi vengono generati durante l'indicizzazione, quando i termini parziali in due o più combinazioni di caratteri vengono tokenizzati insieme a termini interi. Dato che i campi esistenti sono già in formato token, sarà necessario ricostruire l'indice se si desidera aggiungerli a un suggerimento. Per altre informazioni, vedere [Come ricompilare un indice di Ricerca cognitiva](search-howto-reindex.md)di Azure.For more information, see How to rebuild an Azure Cognitive Search index.

## <a name="create-using-rest"></a>Creare usando RESTCreate using REST

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

## <a name="create-using-net"></a>Creare utilizzando .NET

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

## <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

|Proprietà      |Descrizione      |
|--------------|-----------------|
|`name`        |Nome dello strumento suggerimenti.|
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità `analyzingInfixMatching`attualmente supportata è , che attualmente corrisponde all'inizio di un termine.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. I campi devono `Edm.String` `Collection(Edm.String)`essere di tipo e . Se un analizzatore viene specificato nel campo, deve essere un analizzatore denominato da [questo elenco](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (non un analizzatore personalizzato).<p/> Come procedura consigliata, specificare solo i campi che si prestano a una risposta prevista e appropriata, che si tratti di una stringa completata in una barra di ricerca o in un elenco a discesa.<p/>Un nome di hotel è un buon candidato perché ha precisione. I campi dettagliati come le descrizioni e i commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Negli esempi, includiamo comunque "categoria" per dimostrare che è possibile includere più campi. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Utilizzare un suggerimento

In una query viene utilizzato un suggerimento. Dopo aver creato un suggerimento, chiama una delle seguenti API per un'esperienza di ricerca durante la digitazione:

+ [API REST dei suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync (metodo)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metodo AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

In un'applicazione di ricerca, il codice client deve sfruttare una libreria come [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) per raccogliere la query parziale e fornire la corrispondenza. Per ulteriori informazioni su questa attività, vedere [Aggiungere il completamento automatico o i risultati suggeriti al codice client.](search-autocomplete-tutorial.md)

L'utilizzo dell'API è illustrato nella chiamata seguente all'API REST di completamento automatico. Questo esempio è tratto da due asporto. In primo luogo, come con tutte le query, l'operazione è contraria alla raccolta di documenti di un indice e la query include un parametro **di ricerca,** che in questo caso fornisce la query parziale. In secondo luogo, è necessario aggiungere **suggesterName** alla richiesta. Se un suggerimento non è definito nell'indice, una chiamata al completamento automatico o i suggerimenti avranno esito negativo.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Codice di esempio

+ L'esempio per la creazione di [una prima app in C, ad esempio Ricerca come si digita,](tutorial-csharp-type-ahead-and-suggestions.md) illustra la costruzione di un suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice viene eseguito in un servizio di ricerca cognitiva di Azure sandbox e usa un indice Hotels precaricato in modo che tutto ciò che devi fare è premere F5 per eseguire l'applicazione. Non è necessario alcun abbonamento o accesso.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) è un esempio meno recente che contiene sia il codice C , che java. Vengono inoltre illustrate la costruzione di un suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. In questo esempio di codice vengono utilizzati i dati di esempio [DI NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) ospitati. 

## <a name="next-steps"></a>Passaggi successivi

Si consiglia il seguente articolo per saperne di più su come richiede la formulazione.

> [!div class="nextstepaction"]
> [Aggiungere il completamento automatico e suggerimenti al codice clientAdd autocomplete and suggestions to client code](search-autocomplete-tutorial.md) 
