---
title: Aggiungere query typeahead a un indice-ricerca di Azure
description: Consente di abilitare le azioni di query di tipo Ahead in ricerca di Azure creando suggerimenti e formulando richieste che richiamano i termini di query con completamento automatico o con suggerimenti automatici.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648850"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Aggiungere suggerimenti a un indice per typeahead in ricerca di Azure

Un **Suggerimento** è costituito da un costrutto in un [indice di ricerca di Azure](search-what-is-an-index.md) che supporta un'esperienza di tipo "Search-As-You-Type". Contiene un elenco di campi per i quali si desidera abilitare gli input della query typeahead. All'interno di un indice, lo stesso suggeritore supporta una o entrambe queste due varianti di typeahead: completamento automatico completa il termine o la frase digitando, *suggerimenti* fornisce un breve elenco di risultati. 

Lo screenshot seguente illustra entrambe le funzionalità typeahead. In questa pagina di ricerca di Xbox, gli elementi di completamento automatico consentono di passare a una nuova pagina dei risultati di ricerca per tale query, mentre i suggerimenti sono risultati effettivi che consentono di passare a una pagina per quel particolare gioco. È possibile limitare il completamento automatico a un elemento in una barra di ricerca o fornire un elenco come quello illustrato di seguito. Per i suggerimenti, è possibile esporre qualsiasi parte di un documento che meglio descrive il risultato.

![Confronto visivo tra completamento automatico e query suggerite](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Confronto visivo tra completamento automatico e query suggerite")

Per implementare questi comportamenti in ricerca di Azure, sono presenti un componente index e query. 

+ Il componente index è un suggerimento. Per creare un suggerimento, è possibile usare il portale, l'API REST o .NET SDK. 

+ Il componente query è un'azione specificata nella richiesta di query, ovvero un suggerimento o un'azione di completamento automatico. 

Il supporto per la ricerca in base al tipo è abilitato per ogni singolo campo. È possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca se si vuole un'esperienza simile a quella indicata nella schermata. Entrambe le richieste sono destinate alla raccolta Documents di un indice specifico e le risposte vengono restituite dopo che un utente ha fornito almeno una stringa di input di tre caratteri.

## <a name="create-a-suggester"></a>Creare uno strumento suggerimenti

Sebbene un suggerimento abbia diverse proprietà, è principalmente una raccolta di campi per i quali si Abilita un'esperienza di typeahead. Per un'app di viaggio, ad esempio, potrebbe essere necessario abilitare la ricerca con completamento automatico su destinazioni, città e attrazioni. Di conseguenza, tutti e tre i campi vengono inseriti nella raccolta Fields.

Per creare un componente di suggerimento, aggiungerne uno a uno schema di indice. È possibile avere un suggerimento in un indice (in particolare un suggerimento nella raccolta suggesters). 

### <a name="use-the-rest-api"></a>Usare l'API REST

Nell'API REST è possibile aggiungere suggerimenti tramite [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
Dopo aver creato un suggerimento, aggiungere l'API [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) o l' [API di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nella logica di query per richiamare la funzionalità.

### <a name="use-the-net-sdk"></a>Usare .NET SDK

In C#definire un [oggetto del suggerimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`è una raccolta ma può assumere solo un elemento. 

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

I punti chiave da notare sui suggerimenti sono la presenza di un nome (ai suggerimenti a cui viene fatto riferimento in base al nome in una richiesta), un searchMode (attualmente solo uno, "analyzingInfixMatching") e l'elenco dei campi per i quali typeahead è abilitato. 

Un componente per il suggerimento è definito dalle proprietà seguenti:

|Proprietà      |Descrizione      |
|--------------|-----------------|
|`name`        |Nome del suggerimento. Usare il nome del suggerimento quando si chiama l' [API REST suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) o l' [API REST di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. È possibile usare solo campi per i quali non è impostato un analizzatore di lingua personalizzato.<p/>Specificare solo i campi che si prestano a una risposta prevista e appropriata, sia che si tratti di una stringa completata in una barra di ricerca o di un elenco a discesa.<p/>Il nome di un hotel è un buon candidato perché ha una precisione. I campi dettagliati come descrizioni e commenti sono troppo densi. Analogamente, i campi ripetitivi, ad esempio categorie e tag, sono meno efficaci. Gli esempi includono "Category" per dimostrare che è possibile includere più campi. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>Analisi di SourceFields in un suggerimento

Ricerca di Azure analizza il contenuto del campo per consentire l'esecuzione di query su singoli termini. Per i suggerimenti è necessario indicizzare i prefissi oltre ai termini completi, che richiedono un'analisi aggiuntiva sui campi di origine. Le configurazioni dell'analizzatore personalizzato possono combinare uno dei diversi Tokenizer e filtri, spesso in modi che rendono impossibile produrre i prefissi necessari per i suggerimenti. Per questo motivo, **ricerca di Azure impedisce che i campi con analizzatori personalizzati vengano inclusi in un suggerimento**.

> [!NOTE] 
>  L'approccio consigliato per aggirare la limitazione precedente consiste nell'usare 2 campi distinti per lo stesso contenuto. Questo consentirà a uno dei campi di presentare suggerimenti e l'altro può essere configurato con una configurazione dell'analizzatore personalizzato.

## <a name="when-to-create-a-suggester"></a>Quando creare un suggerimento

Per evitare la ricompilazione di un indice, è necessario creare contemporaneamente un `sourceFields` suggerimento e i campi specificati in.

Se si aggiunge un suggerimento a un indice esistente, in cui sono inclusi i campi esistenti `sourceFields`in, la definizione del campo viene modificata in modo sostanziale ed è necessaria una ricompilazione. Per altre informazioni, vedere [come ricompilare un indice di ricerca di Azure](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Come usare un suggerimento

Come indicato in precedenza, è possibile usare un suggerimento per le query suggerite, il completamento automatico o entrambi. 

Si fa riferimento a un suggerimento nella richiesta insieme all'operazione. Ad esempio, in una chiamata Get Rest specificare `suggest` o `autocomplete` nella raccolta Documents. Per REST, dopo la creazione di un suggerimento, usare l' [API suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) o l' [API di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nella logica di query.

Per .NET, usare [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) o [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Per un esempio in cui vengono illustrate entrambe le richieste, vedere [esempio per l'aggiunta di completamento automatico e suggerimenti in ricerca di Azure](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Codice di esempio

L'esempio [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) contiene il C# codice e Java e illustra una costruzione del suggerimento, le query suggerite, il completamento automatico e l'esplorazione facet. 

Usa un servizio di ricerca di Azure sandbox e un indice precaricato, quindi è sufficiente premere F5 per eseguirlo. Non è necessaria alcuna sottoscrizione o accesso.

## <a name="next-steps"></a>Passaggi successivi

Si consiglia l'esempio seguente per vedere come vengono formulate le richieste.

> [!div class="nextstepaction"]
> [Suggerimenti e esempi di completamento automatico](search-autocomplete-tutorial.md) 
