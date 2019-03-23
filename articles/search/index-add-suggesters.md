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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370653"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Aggiungere suggester a un indice per typeahead in ricerca di Azure

Oggetto **dello strumento suggerimenti** è un costrutto in un [indice di ricerca di Azure](search-what-is-an-index.md) che supporta un'esperienza di "ricerca---digitazione". Contiene un elenco di campi per il quale si desidera abilitare gli input di query typeahead. Esistono due varianti typeahead: [ *autocomplete* ](search-autocomplete-tutorial.md) completamento termine o frase si digita, [ *suggerimenti automatici* ](search-autosuggest-example.md) fornisce un breve elenco di parole o frasi specifiche che è possibile selezionare come una query di input. Si sono visto indubbiamente questi comportamenti prima nei motori di ricerca commerciali.

![Confronto tra Visual della funzionalità Completamento automatico e suggerimenti automatici](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual confronto della funzionalità Completamento automatico e suggerimenti automatici")

Per implementare questi comportamenti in ricerca di Azure, è un componente dell'indice e query. 

+ In un indice, aggiungere un componente di suggerimento. È possibile usare il portale, l'API REST o .NET SDK per creare un componente di suggerimento. 

+ In una query, specificare l'azione di completamento automatico o suggerimenti automatici. 

> [!Important]
> Completamento automatico è attualmente disponibile in anteprima, disponibile in anteprima le API REST e .NET SDK e non è supportata per le applicazioni di produzione. 

Supporto Typeahead è abilitato in base al campo. Se si desidera un'esperienza simile a quello indicato nella schermata, è possibile implementare entrambi i comportamenti typeahead all'interno della stessa soluzione di ricerca. Destinazione entrambe le richieste il *documenti* raccolta dell'indice specifico e le risposte vengono restituiti dopo che un utente ha fornito ad almeno una stringa di input di tre caratteri.

## <a name="create-a-suggester"></a>Creare un componente di suggerimento

Anche se un componente per il suggerimento ha diverse proprietà, è principalmente una raccolta di campi per il quale si sta abilitando un'esperienza typeahead. Per un'app di viaggio, ad esempio, potrebbe essere necessario abilitare la ricerca con completamento automatico su destinazioni, città e attrazioni. Di conseguenza, tutte e tre i campi viene memorizzato nella raccolta di campi.

Per creare un componente di suggerimento, aggiungerne uno a uno schema di indice. È possibile avere un suggerimento in un indice (in particolare, un suggerimento nella raccolta suggesters). 

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

In .NET SDK, usare una [classe dello strumento suggerimenti](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Componente per il suggerimento è una raccolta, ma può richiedere solo un elemento.

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

I punti principali da notare sui componenti per il suggerimento è che ci sia un nome (suggesters viene fatto riferimento in base al nome in una richiesta), un searchMode (attualmente solo una, "analyzingInfixMatching") e l'elenco dei campi per cui è abilitato typeahead. 

Dopo aver creato uno strumento suggerimenti, aggiungere l'[API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) nella logica di query per richiamare la funzionalità.

### <a name="property-reference"></a>Informazioni di riferimento sulle proprietà

Un componente per il suggerimento è definito dalle proprietà seguenti:

|Proprietà      |DESCRIZIONE      |
|--------------|-----------------|
|`name`        |Il nome del componente. Utilizza il nome del componente quando si chiama il [API REST per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) oppure [Autocomplete REST API (anteprima)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.|
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. È possibile usare solo campi per i quali non è impostato un analizzatore di lingua personalizzato.<p/>Specificare solo i campi che si prestano a una risposta prevista e appropriata, sia che si tratti di una stringa completa in una barra di ricerca o un elenco a discesa.<p/>Un nome di un hotel è un candidato valido perché contiene la precisione. I campi dettagliati, ad esempio le descrizioni e i commenti sono troppo ad alta densità. Analogamente, i campi ripetitivi, quali categorie e tag, risultano meno efficaci. Negli esempi si includono "category" comunque per dimostrare che è possibile includere più campi. |

### <a name="index-rebuilds-for-existing-fields"></a>Ricompilazioni degli indici per i campi esistenti

Suggesters contengono campi e se si aggiunge un componente di suggerimento a un indice esistente o si modifica la composizione dei campi, è molto probabilmente sarà necessario ricompilare l'indice.

| Azione | Impatto |
|--------|--------|
| Creare nuovi campi e creare un nuovo componente di suggerimento contemporaneamente nello stesso aggiornamento | Impatto minimo. Se l'indice contiene i campi aggiunti in precedenza, l'aggiunta di nuovi campi e un nuovo componente di suggerimento non incide sui campi esistenti. |
| Aggiungere campi esistenti a un componente di suggerimento | Impatto elevato. L'aggiunta di un campo di modifica la definizione di campo, è necessario eseguire un' [ricompilazione completa](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Usare un componente di suggerimento

Come indicato in precedenza, è possibile usare un componente di suggerimento di suggerimento automatico, completamento automatico o entrambi. 

Un componente di suggerimento fa riferimento alla richiesta con l'operazione. Ad esempio, in una chiamata GET REST, specificare `suggest` o `autocomplete` nella raccolta di documenti. Per REST, dopo la creazione di un componente di suggerimento, usare il [API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) o nella [Autocomplete API (anteprima)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nella logica di query.

Per .NET, usare [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) oppure [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Esempi che illustrano ogni richiesta:

+ [Aggiungere suggerimenti automatici per le selezioni di query di elenco a discesa](search-autosuggest-example.md)

+ [Aggiungere il completamento automatico per gli input parziale di termini in ricerca di Azure](search-autocomplete-tutorial.md) (in anteprima) 

## <a name="sample-code"></a>Codice di esempio

Il [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) esempio contiene sia C# e il codice Java e viene illustrata una costruzione dello strumento suggerimenti, suggerimenti automatici, completamento automatico e navigazione facet. 

Usa un ambiente sandbox del servizio di ricerca di Azure e un indice precaricato in modo che tutto è necessario eseguire è premere F5 per eseguirla. Nessun abbonamento o iscriversi in necessarie.

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli esempi seguenti per vedere come vengono formulate le richieste:

+ [Esempio di query autosuggested](search-autosuggest-example.md) 
+ [Esempio di query di completamento automatico (anteprima)](search-autocomplete-tutorial.md) 
