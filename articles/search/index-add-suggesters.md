---
title: Creare uno strumento suggerimenti
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query di tipo Ahead in Azure ricerca cognitiva creando suggerimenti e formulando richieste che richiamano i termini di query con completamento automatico o con suggerimenti automatici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 2a0798ee923624aef9f29c1e9cc30f38b55770a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565327"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Creare un suggerimento per abilitare il completamento automatico e i risultati suggeriti in una query

In Azure ricerca cognitiva "Search-As-You-Type" viene abilitato tramite un costrutto del **Suggerimento** aggiunto a un [indice di ricerca](search-what-is-an-index.md). Un suggerimento supporta due esperienze: *completamento automatico*, che completa un input parziale per una query di termini interi, e *suggerimenti* che invita a fare clic su una corrispondenza specifica. Il completamento automatico genera una query. I suggerimenti producono un documento corrispondente.

La schermata seguente illustra come [creare la prima app in C#](tutorial-csharp-type-ahead-and-suggestions.md) . Il completamento automatico prevede un termine potenziale, terminando "TW" con "in". I suggerimenti sono i risultati della ricerca minima, dove un campo come il nome dell'hotel rappresenta un documento di ricerca di un hotel corrispondente dall'indice. Per i suggerimenti, è possibile esporre qualsiasi campo che fornisca informazioni descrittive.

![Confronto visivo tra completamento automatico e query suggerite](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Confronto visivo tra completamento automatico e query suggerite")

È possibile utilizzare queste funzionalità separatamente o insieme. Per implementare questi comportamenti in Azure ricerca cognitiva, è disponibile un componente di query e di indice. 

+ Nell'indice aggiungere un componente di suggerimento a un indice. È possibile usare il portale, l' [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Il resto di questo articolo è incentrato sulla creazione di un suggerimento.

+ Nella richiesta di query, chiamare una delle [API elencate di seguito](#how-to-use-a-suggester).

Il supporto per la ricerca in base al tipo è abilitato per ogni singolo campo per i campi stringa. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si vuole un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta *Documents* di un indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="what-is-a-suggester"></a>Che cos'è un suggerimento?

Un suggerimento è una struttura di dati interna che supporta i comportamenti di ricerca in base al tipo, archiviando i prefissi per la corrispondenza in query parziali. Come per i termini in formato token, i prefissi vengono archiviati in indici invertiti, uno per ogni campo specificato in una raccolta di campi del suggerimento.

## <a name="define-a-suggester"></a>Definire un suggerimento

Per creare un componente di suggerimento, aggiungerne uno a uno [schema dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index) e [impostare ciascuna proprietà](#property-reference). Il momento migliore per creare un suggerimento è quando si definisce anche il campo che lo utilizzerà.

+ USA solo campi stringa

+ Usare l'analizzatore Lucene standard predefinito ( `"analyzer": null` ) o un [analizzatore del linguaggio](index-add-language-analyzers.md) (ad esempio, `"analyzer": "en.Microsoft"` ) nel campo

### <a name="choose-fields"></a>Selezionare i campi

Sebbene un suggerimento includa diverse proprietà, è principalmente una raccolta di campi stringa per cui si Abilita un'esperienza di ricerca in modalità di tipo. È presente un suggerimento per ogni indice, pertanto l'elenco di suggerimenti deve includere tutti i campi che contribuiscono al contenuto per i suggerimenti e il completamento automatico.

Il completamento automatico offre vantaggi da un pool di campi più ampio da cui creare dati, perché il contenuto aggiuntivo ha un potenziale completamento del termine.

I suggerimenti, d'altra parte, producono risultati migliori quando la scelta del campo è selettiva. Tenere presente che il suggerimento è un proxy per un documento di ricerca, in modo da volere i campi che rappresentano meglio un singolo risultato. Nomi, titoli o altri campi univoci che distinguono tra più corrispondenze funzionano meglio. Se i campi sono costituiti da valori ripetitivi, i suggerimenti sono costituiti da risultati identici e un utente non saprà quale fare clic.

Per soddisfare entrambe le esperienze di ricerca in quanto si digita, aggiungere tutti i campi necessari per il completamento automatico, ma usare **$Select**, **$Top**, **$Filter**e **searchFields** per controllare i risultati per i suggerimenti.

### <a name="choose-analyzers"></a>Scegli analizzatori

La scelta di un analizzatore determina il modo in cui i campi vengono suddivisi in token e successivamente preceduti. Ad esempio, per una stringa con trattino come "sensibile al contesto", l'uso di un analizzatore del linguaggio determinerà le combinazioni di token seguenti: "context", "sensitive", "sensibile al contesto". Se è stato usato l'analizzatore Lucene standard, la stringa sillabata non esiste. 

Quando si valutano gli analizzatori, provare a usare l' [API di analisi del testo](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) per comprendere in che modo i termini vengono suddivisi in token e successivamente preceduti dal prefisso. Una volta compilato un indice, è possibile provare diversi analizzatori su una stringa per visualizzare l'output del token.

I campi che usano [analizzatori personalizzati](index-add-custom-analyzers.md) o [analizzatori predefiniti](index-add-custom-analyzers.md#predefined-analyzers-reference) (ad eccezione di Lucene standard) non sono consentiti in modo esplicito per evitare risultati insoddisfacenti.

> [!NOTE]
> Se è necessario aggirare il vincolo dell'analizzatore, ad esempio se è necessaria una parola chiave o Ngram Analyzer per determinati scenari di query, è necessario usare due campi distinti per lo stesso contenuto. Questo consentirà a uno dei campi di avere un suggerimento, mentre l'altro può essere configurato con una configurazione dell'analizzatore personalizzato.

### <a name="when-to-create-a-suggester"></a>Quando creare un suggerimento

Il momento migliore per creare un suggerimento è quando si crea anche la definizione di campo.

Se si tenta di creare un suggerimento usando i campi preesistenti, l'API non lo consentirà. I prefissi vengono generati durante l'indicizzazione, quando i termini parziali in due o più combinazioni di caratteri vengono suddivisi in token insieme a termini interi. Dato che i campi esistenti sono già in formato token, sarà necessario ricompilare l'indice se si desidera aggiungerli a un suggerimento. Per altre informazioni, vedere [How to rebuild an Azure ricerca cognitiva index](search-howto-reindex.md).

## <a name="create-using-rest"></a>Crea con REST

Nell'API REST aggiungere i suggerimenti tramite [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

## <a name="create-using-net"></a>Creare con .NET

In C# definire un [oggetto del suggerimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`è una raccolta ma può assumere solo un elemento. 

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
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching` , che attualmente corrisponde all'inizio di un termine.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. I campi devono essere di tipo `Edm.String` e `Collection(Edm.String)` . Se un analizzatore viene specificato nel campo, deve essere un analizzatore denominato da [questo elenco](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (non un analizzatore personalizzato).<p/> Come procedura consigliata, specificare solo i campi che si prestano a una risposta prevista e appropriata, indipendentemente dal fatto che si tratti di una stringa completata in una barra di ricerca o in un elenco a discesa.<p/>Il nome di un hotel è un buon candidato perché ha una precisione. I campi dettagliati come descrizioni e commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Gli esempi includono "Category" per dimostrare che è possibile includere più campi. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Usa un suggerimento

In una query viene utilizzato un suggerimento. Dopo aver creato un suggerimento, chiamare una delle API seguenti per un'esperienza di ricerca in modalità di tipo:

+ [API REST suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metodo SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metodo AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

In un'applicazione di ricerca, il codice client deve usare una libreria come il [completamento automatico dell'interfaccia utente jQuery](https://jqueryui.com/autocomplete/) per raccogliere la query parziale e fornire la corrispondenza. Per altre informazioni su questa attività, vedere [aggiungere il completamento automatico o i risultati suggeriti al codice client](search-autocomplete-tutorial.md).

L'utilizzo delle API è illustrato nella chiamata seguente all'API REST di completamento automatico. Questo esempio presenta due considerazioni. In primo luogo, come per tutte le query, l'operazione viene eseguita rispetto alla raccolta Documents di un indice e la query include un parametro di **ricerca** , che in questo caso fornisce la query parziale. In secondo luogo, è necessario aggiungere **suggesterName** alla richiesta. Se un suggerimento non è definito nell'indice, una chiamata al completamento automatico o ai suggerimenti avrà esito negativo.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Codice di esempio

+ [Creare la prima app in C# (lezione 3-aggiungere un esempio di ricerca in base al tipo)](tutorial-csharp-type-ahead-and-suggestions.md) illustra una costruzione del suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice viene eseguito in un servizio sandbox ricerca cognitiva di Azure e usa un indice degli Alberghi precaricato, quindi è sufficiente premere F5 per eseguire l'applicazione. Non è necessaria alcuna sottoscrizione o accesso.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) è un esempio precedente contenente codice C# e Java. Viene inoltre illustrata una costruzione del suggerimento, le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice usa i dati di esempio [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) ospitati. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla formulazione delle richieste, è consigliabile usare l'articolo seguente.

> [!div class="nextstepaction"]
> [Aggiungere il completamento automatico e suggerimenti al codice client](search-autocomplete-tutorial.md) 
