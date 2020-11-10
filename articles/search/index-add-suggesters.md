---
title: Creare uno strumento suggerimenti
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query di tipo Ahead in Azure ricerca cognitiva creando suggerimenti e formulando richieste che richiamano i termini di query con completamento automatico o con suggerimenti automatici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 498934c01970b296c1491e7ccd36ad947324306a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445337"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Creare un suggerimento per abilitare il completamento automatico e i risultati suggeriti in una query

In Azure ricerca cognitiva "Search-As-You-Type" viene abilitato tramite un costrutto del **Suggerimento** aggiunto a un [indice di ricerca](search-what-is-an-index.md). Un suggerimento supporta due esperienze: *completamento automatico* , che completa un input parziale per una query di termini interi, e *suggerimenti* che invitano clic su una corrispondenza specifica. Il completamento automatico genera una query. I suggerimenti producono un documento corrispondente.

La schermata seguente illustra come [creare la prima app in C#](tutorial-csharp-type-ahead-and-suggestions.md) . Il completamento automatico prevede un termine potenziale, terminando "TW" con "in". I suggerimenti sono i risultati della ricerca minima, dove un campo come il nome dell'hotel rappresenta un documento di ricerca di un hotel corrispondente dall'indice. Per i suggerimenti, è possibile esporre qualsiasi campo che fornisca informazioni descrittive.

![Confronto visivo tra completamento automatico e query suggerite](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Confronto visivo tra completamento automatico e query suggerite")

È possibile utilizzare queste funzionalità separatamente o insieme. Per implementare questi comportamenti in Azure ricerca cognitiva, è disponibile un componente di query e di indice. 

+ Aggiungere un componente di suggerimento a una definizione dell'indice di ricerca. Il resto di questo articolo è incentrato sulla creazione di un suggerimento.

+ Chiamare una query abilitata per il suggerimento, sotto forma di richiesta di suggerimento o di completamento automatico, usando una delle [API elencate di seguito](#how-to-use-a-suggester).

Il supporto per la ricerca in base al tipo è abilitato per ogni singolo campo per i campi stringa. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si vuole un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta *Documents* di un indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="what-is-a-suggester"></a>Che cos'è un suggerimento?

Un suggerimento è una struttura di dati interna che supporta i comportamenti di ricerca in base al tipo, archiviando i prefissi per la corrispondenza in query parziali. Come per i termini in formato token, i prefissi vengono archiviati in indici invertiti, uno per ogni campo specificato in una raccolta di campi del suggerimento.

## <a name="how-to-create-a-suggester"></a>Come creare un suggerimento

Per creare un componente di suggerimento, aggiungerne uno a una [definizione di indice](/rest/api/searchservice/create-index). Un suggerimento riceve un nome e una raccolta di campi in cui è abilitata l'esperienza typeahead. e [impostare ciascuna proprietà](#property-reference). Il momento migliore per creare un suggerimento è quando si definisce anche il campo che lo utilizzerà.

+ USA solo campi stringa

+ Usare l'analizzatore Lucene standard predefinito ( `"analyzer": null` ) o un [analizzatore del linguaggio](index-add-language-analyzers.md) (ad esempio, `"analyzer": "en.Microsoft"` ) nel campo

Se si tenta di creare un suggerimento usando i campi preesistenti, l'API non lo consentirà. I prefissi vengono generati durante l'indicizzazione, quando i termini parziali in due o più combinazioni di caratteri vengono suddivisi in token insieme a termini interi. Dato che i campi esistenti sono già in formato token, sarà necessario ricompilare l'indice se si desidera aggiungerli a un suggerimento. Per altre informazioni, vedere [How to rebuild an Azure ricerca cognitiva index](search-howto-reindex.md).

### <a name="choose-fields"></a>Selezionare i campi

Sebbene un suggerimento includa diverse proprietà, è principalmente una raccolta di campi stringa per cui si Abilita un'esperienza di ricerca in modalità di tipo. È presente un suggerimento per ogni indice, pertanto l'elenco di suggerimenti deve includere tutti i campi che contribuiscono al contenuto per i suggerimenti e il completamento automatico.

Il completamento automatico offre vantaggi da un pool di campi più ampio da cui creare dati, perché il contenuto aggiuntivo ha un potenziale completamento del termine.

I suggerimenti, d'altra parte, producono risultati migliori quando la scelta del campo è selettiva. Tenere presente che il suggerimento è un proxy per un documento di ricerca, in modo da volere i campi che rappresentano meglio un singolo risultato. Nomi, titoli o altri campi univoci che distinguono tra più corrispondenze funzionano meglio. Se i campi sono costituiti da valori ripetitivi, i suggerimenti sono costituiti da risultati identici e un utente non saprà quale fare clic.

Per soddisfare entrambe le esperienze di ricerca in quanto si digita, aggiungere tutti i campi necessari per il completamento automatico, ma usare **$Select** , **$Top** , **$Filter** e **searchFields** per controllare i risultati per i suggerimenti.

### <a name="choose-analyzers"></a>Scegli analizzatori

La scelta di un analizzatore determina il modo in cui i campi vengono suddivisi in token e successivamente preceduti. Ad esempio, per una stringa con trattino come "sensibile al contesto", l'uso di un analizzatore del linguaggio determinerà le combinazioni di token seguenti: "context", "sensitive", "sensibile al contesto". Se è stato usato l'analizzatore Lucene standard, la stringa sillabata non esiste. 

Quando si valutano gli analizzatori, provare a usare l' [API di analisi del testo](/rest/api/searchservice/test-analyzer) per informazioni dettagliate sull'elaborazione dei termini. Una volta compilato un indice, è possibile provare diversi analizzatori su una stringa per visualizzare l'output del token.

I campi che usano [analizzatori personalizzati](index-add-custom-analyzers.md) o [analizzatori predefiniti](index-add-custom-analyzers.md#predefined-analyzers-reference) (ad eccezione di Lucene standard) non sono consentiti in modo esplicito per evitare risultati insoddisfacenti.

> [!NOTE]
> Se è necessario aggirare il vincolo dell'analizzatore, ad esempio se è necessaria una parola chiave o Ngram Analyzer per determinati scenari di query, è necessario usare due campi distinti per lo stesso contenuto. Questo consentirà a uno dei campi di avere un suggerimento, mentre l'altro può essere configurato con una configurazione dell'analizzatore personalizzato.

## <a name="create-using-the-portal"></a>Creare usando il portale

Quando si usa **Aggiungi indice** o **importazione guidata dati** per creare un indice, è possibile abilitare un suggerimento:

1. Nella definizione dell'indice immettere un nome per il suggerimento.

1. In ogni definizione di campo per i nuovi campi selezionare una casella di controllo nella colonna del suggerimento. Una casella di controllo è disponibile solo nei campi stringa. 

Come indicato in precedenza, la scelta dell'analizzatore influisca sulla suddivisione in token e sul prefisso. Si consideri l'intera definizione di campo quando si abilitano i suggerimenti. 

## <a name="create-using-rest"></a>Crea con REST

Nell'API REST aggiungere i suggerimenti tramite [create index](/rest/api/searchservice/create-index) o [Update index](/rest/api/searchservice/update-index). 

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

In C# definire un [oggetto SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` è una raccolta in un oggetto SearchIndex, ma può assumere solo un elemento. 

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    //var suggester = new SearchSuggester("sg", sourceFields = "HotelName", "Category");

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category"});

    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

|Proprietà      |Descrizione      |
|--------------|-----------------|
|`name`        | Specificato nella definizione del suggerimento, ma anche chiamato su una richiesta di completamento automatico o di suggerimenti. |
|`sourceFields`| Specificato nella definizione del suggerimento. Si tratta di un elenco di uno o più campi nell'indice che sono l'origine del contenuto per i suggerimenti. I campi devono essere di tipo `Edm.String` e `Collection(Edm.String)` . Se un analizzatore viene specificato nel campo, deve essere un analizzatore lessicale denominato da [questo elenco](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (non un analizzatore personalizzato).<p/> Come procedura consigliata, specificare solo i campi che si prestano a una risposta prevista e appropriata, indipendentemente dal fatto che si tratti di una stringa completata in una barra di ricerca o in un elenco a discesa.<p/>Il nome di un hotel è un buon candidato perché ha una precisione. I campi dettagliati come descrizioni e commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Gli esempi includono "Category" per dimostrare che è possibile includere più campi. |
|`searchMode`  | Parametro solo REST, ma anche visibile nel portale. Questo parametro non è disponibile in .NET SDK. Indica la strategia utilizzata per la ricerca di frasi candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching` , che attualmente corrisponde all'inizio di un termine.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Usa un suggerimento

In una query viene utilizzato un suggerimento. Dopo aver creato un suggerimento, chiamare una delle API seguenti per un'esperienza di ricerca in modalità di tipo:

+ [API REST suggerimenti](/rest/api/searchservice/suggestions)
+ [API REST di completamento automatico](/rest/api/searchservice/autocomplete)
+ [Metodo SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Metodo AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

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

+ [Creare la prima app in C# (lezione 3-aggiungere un esempio di ricerca in base al tipo)](tutorial-csharp-type-ahead-and-suggestions.md) illustra le query suggerite, il completamento automatico e l'esplorazione in base a facet. Questo esempio di codice viene eseguito in un servizio sandbox Azure ricerca cognitiva e usa un indice degli Alberghi precaricato con un suggerimento già creato, quindi è sufficiente premere F5 per eseguire l'applicazione. Non è necessaria alcuna sottoscrizione o accesso.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla formulazione delle richieste, è consigliabile usare l'articolo seguente.

> [!div class="nextstepaction"]
> [Aggiungere il completamento automatico e suggerimenti al codice client](search-autocomplete-tutorial.md)