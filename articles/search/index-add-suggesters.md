---
title: Aggiungere query typeahead a un indice - ricerca di Azure
description: Abilitare azioni di completamento automatico delle query in ricerca di Azure tramite la creazione di componenti per il suggerimento e formulare le richieste che richiamano il completamento automatico o autosuggested termini della query.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: a8bc86c2d3511fa04e595b8b2988d9a98bf084b2
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650462"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Aggiungere suggester a un indice per typeahead in ricerca di Azure

Oggetto **dello strumento suggerimenti** è un costrutto in un [indice di ricerca di Azure](search-what-is-an-index.md) che supporta un'esperienza di "ricerca---digitazione". Contiene un elenco di campi per il quale si desidera abilitare gli input di query typeahead. All'interno di un indice, il componente stesso supporta una o entrambe queste due varianti typeahead: *autocomplete* completa termine o frase si digita, *suggerimenti* offre un breve elenco di risultati. 

Lo screenshot seguente illustra entrambe le funzionalità typeahead. In questa pagina di ricerca Xbox, gli elementi di completamento automatico indirizzano a una nuova pagina di risultati di ricerca per tale query, mentre i suggerimenti rappresentano i risultati effettivi che visualizzano una pagina del gioco specifico. È possibile limitare il completamento automatico per un elemento in una barra di ricerca o fornire un elenco simile a quello illustrato di seguito. Per suggerimenti, è possibile della superficie di attacco qualsiasi parte di un documento che meglio descrive il risultato.

![Confronto tra Visual di completamento automatico e le query suggerite](./media/index-add-suggesters/visual-comparison-suggest-complete.png "confronto visivo del completamento automatico e le query suggerite")

Per implementare questi comportamenti in ricerca di Azure, è un componente dell'indice e query. 

+ Il componente dell'indice è un componente di suggerimento. È possibile usare il portale, l'API REST o .NET SDK per creare un componente di suggerimento. 

+ Il componente di query è un'azione specificata nella richiesta di query (azione di suggerimenti o il completamento automatico). 

> [!Important]
> Completamento automatico è attualmente in anteprima, disponibile in anteprima le API REST e .NET SDK. Non si tratta delle applicazioni di produzione. 

Ricerca---digitazione supporto è abilitato in base al campo. Se si desidera un'esperienza simile a quello indicato nella schermata, è possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca. Destinazione entrambe le richieste il *documenti* raccolta dell'indice specifico e le risposte vengono restituiti dopo che un utente ha fornito ad almeno una stringa di input di tre caratteri.

## <a name="create-a-suggester"></a>Creare uno strumento suggerimenti

Anche se un componente per il suggerimento ha diverse proprietà, è principalmente una raccolta di campi per il quale si sta abilitando un'esperienza typeahead. Per un'app di viaggio, ad esempio, potrebbe essere necessario abilitare la ricerca con completamento automatico su destinazioni, città e attrazioni. Di conseguenza, tutte e tre i campi viene memorizzato nella raccolta di campi.

Per creare un componente di suggerimento, aggiungerne uno a uno schema di indice. È possibile avere un suggerimento in un indice (in particolare, un suggerimento nella raccolta suggesters). 

### <a name="use-the-rest-api"></a>Usare l'API REST

Nell'API REST, è possibile aggiungere suggesters attraverso [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) oppure [indice ad aggiornamento](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
Dopo la creazione di un componente di suggerimento, aggiungere il [API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) oppure [Autocomplete API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nella logica di query per richiamare la funzionalità.

### <a name="use-the-net-sdk"></a>Usare .NET SDK

In C#, definire un [oggetto suggerimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` è una raccolta, ma può richiedere solo un elemento. 

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

## <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

I punti principali da notare sui componenti per il suggerimento è che ci sia un nome (suggesters viene fatto riferimento in base al nome in una richiesta), un searchMode (attualmente solo una, "analyzingInfixMatching") e l'elenco dei campi per cui è abilitato typeahead. 

Un componente per il suggerimento è definito dalle proprietà seguenti:

|Proprietà      |DESCRIZIONE      |
|--------------|-----------------|
|`name`        |Il nome del componente. Utilizza il nome del componente quando si chiama il [API REST per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) oppure [Autocomplete REST API (anteprima)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. È possibile usare solo campi per i quali non è impostato un analizzatore di lingua personalizzato.<p/>Specificare solo i campi che si prestano a una risposta prevista e appropriata, sia che si tratti di una stringa completa in una barra di ricerca o un elenco a discesa.<p/>Un nome di un hotel è un candidato valido perché contiene la precisione. I campi dettagliati, ad esempio le descrizioni e i commenti sono troppo ad alta densità. Analogamente, i campi ripetitivi, quali categorie e tag, risultano meno efficaci. Negli esempi si includono "category" comunque per dimostrare che è possibile includere più campi. |

## <a name="when-to-create-a-suggester"></a>Quando creare un componente di suggerimento

Per evitare una ricompilazione dell'indice, un componente di suggerimento e i campi specificati nella `sourceFields` deve essere creata nello stesso momento.

Se si aggiunge un componente di suggerimento per un indice esistente, in cui sono inclusi i campi esistenti nel `sourceFields`, la definizione di campo in sostanza, cambia e un'operazione di ricompilazione è obbligatorio. Per altre informazioni, vedere [come ricompilare un indice di ricerca di Azure](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Come usare un componente di suggerimento

Come indicato in precedenza, è possibile usare un componente di suggerimento per le query suggerite, completamento automatico o entrambi. 

Un componente di suggerimento fa riferimento alla richiesta con l'operazione. Ad esempio, in una chiamata GET REST, specificare `suggest` o `autocomplete` nella raccolta di documenti. Per REST, dopo la creazione di un componente di suggerimento, usare il [API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) o nella [Autocomplete API (anteprima)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nella logica di query.

Per .NET, usare [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) oppure [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Per un esempio che illustra entrambe le richieste, vedere [esempio per l'aggiunta di completamento automatico e i suggerimenti in ricerca di Azure](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Codice di esempio

Il [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) esempio contiene sia C# e il codice Java e viene illustrata una costruzione dello strumento suggerimenti, le query suggerite, completamento automatico e navigazione facet. 

Usa un ambiente sandbox del servizio di ricerca di Azure e un indice precaricato in modo che tutto è necessario eseguire è premere F5 per eseguirla. Nessun abbonamento o iscriversi in necessarie.

## <a name="next-steps"></a>Passaggi successivi

È consigliabile l'esempio seguente per vedere come vengono formulate le richieste.

> [!div class="nextstepaction"]
> [Suggerimenti ed esempi di completamento automatico](search-autocomplete-tutorial.md) 
