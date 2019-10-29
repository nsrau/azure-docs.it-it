---
title: Aggiungere suggerimenti e completamento automatico in una casella di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di completamento automatico delle query in Ricerca cognitiva di Azure creando strumenti suggerimenti e formulando richieste per compilare una casella di ricerca con termini o frasi completate.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792516"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Aggiungere suggerimenti o completamento automatico all'applicazione Ricerca cognitiva di Azure

Questo articolo illustra come usare i [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) e il [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) per creare una casella di ricerca potente che supporti i comportamenti di ricerca durante la digitazione.

+ I *suggerimenti* sono i risultati suggeriti generati durante la digitazione, dove ogni suggerimento è un singolo risultato dall'indice corrispondente a quanto digitato fino a quel momento. 

+ Il *completamento automatico* "termina" la parola o la frase che un utente sta digitando. Anziché restituire risultati, completa una query, che può quindi essere eseguita per restituire i risultati. Come per i suggerimenti, una parola o una frase completata in una query viene predicata in base a una corrispondenza nell'indice. Il servizio non offrirà query che non restituiscono risultati nell'indice.

È possibile scaricare ed eseguire il codice di esempio in **DotNetHowToAutocomplete** per valutare queste funzionalità. Il codice di esempio contiene un indice predefinito popolato con i [dati demo di NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). L'indice NYCJobs contiene un [costrutto di strumento suggerimenti](index-add-suggesters.md), che è un requisito per l'utilizzo dei suggerimenti o del completamento automatico. È possibile usare l'indice preparato in un servizio sandbox o [popolare un indice personalizzato](#configure-app) utilizzando un caricatore dati nella soluzione di esempio NYCJobs. 

Nell'esempio **DotNetHowToAutocomplete** vengono illustrati sia i suggerimenti sia il completamento automatico, nelle versioni dei linguaggi C# e JavaScript. Gli sviluppatori C# possono eseguire un'applicazione basata su MVC ASP.NET che usa [.NET SDK di Ricerca cognitiva di Azure](https://aka.ms/search-sdk). La logica per eseguire chiamate alle query di completamento automatico e suggerite è disponibile nel file HomeController.cs. Gli sviluppatori JavaScript troveranno la logica di query equivalente nel file IndexJavaScript.cshtml, che include le chiamate dirette all'[API REST di Ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Per entrambe le versioni dei linguaggi, l'esperienza utente front-end è basata sulle librerie di [jQuery](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/). Queste librerie vengono usate per compilare la casella di ricerca che supporta i suggerimenti e il completamento automatico. Gli input raccolti nella casella di ricerca sono abbinati a suggerimenti e azioni di completamento automatico, ad esempio quelli definiti in HomeController.cs o IndexJavaScript.cshtml.

L'esercizio guida l'utente nell'esecuzione delle le attività seguenti:

> [!div class="checklist"]
> * Implementare una casella di input di ricerca in JavaScript e inviare le richieste per corrispondenze suggerite o termini completati automaticamente
> * In C# definire i suggerimenti e le azioni di completamento automatico in HomeController.cs
> * In JavaScript chiamare direttamente le API REST per fornire la funzionalità equivalente

## <a name="prerequisites"></a>Prerequisiti

Un servizio di ricerca cognitiva di Azure è facoltativo per questo esercizio perché la soluzione usa un servizio sandbox live che ospita un indice demo di NYCJobs preparato. Se si vuole eseguire questo esempio nel proprio servizio di ricerca, vedere [Configure l'indice di NYC Jobs](#configure-app) per le istruzioni.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

* Scaricare l'[esempio DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

L'esempio è completo e include suggerimenti, completamento automatico, esplorazione in base a facet e memorizzazione nella cache sul lato client. Per una descrizione completa di quanto offerto dall'esempio, vedere il file Leggimi e i commenti.

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Aprire **AutocompleteTutorial.sln** in Visual Studio. La soluzione contiene un progetto MVC ASP.NET con una connessione all'indice demo di NYC Jobs.

2. Premi F5 per far partire il progetto e caricare la pagina nel browser che preferisci.

Nella parte superiore, vedrai un'opzione per la selezione di C# o JavaScript. L'opzione C# chiama la classe HomeController dal browser e usa .NET SDK di Ricerca cognitiva di Azure per recuperare i risultati. 

L'opzione JavaScript chiama l'API REST di Ricerca cognitiva di Azure direttamente dal browser. Questa opzione ha in genere prestazioni nettamente migliori perché esclude i controller dal flusso. È possibile scegliere l'opzione adatta alle proprie esigenze e preferenze linguistiche. Nella pagina sono disponibili alcuni esempi di completamento automatico con istruzioni per ognuno. Ogni esempio presenta un testo di esempio consigliato che è possibile provare.  

Prova a digitare alcune lettere in ciascuna casella di ricerca per scoprire cosa succede.

## <a name="search-box"></a>Casella di ricerca

Per entrambe le versioni C# e JavaScript, l'implementazione della casella di ricerca è esattamente la stessa. 

Aprire il file **Index.cshtml** nella cartella \Visualizzazioni\Home per visualizzare il codice:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Questo esempio contiene una casella di testo di input semplice con una classe per definire lo stile, un ID a cui JavaScript farà riferimento e un testo segnaposto.  Il trucco è nel codice JavaScript incorporato.

L'esempio di linguaggio C# usa JavaScript in Index.cshtml per sfruttare la [libreria di completamento automatico dell'interfaccia utente di jQuery](https://jqueryui.com/autocomplete/). Questa libreria aggiunge l'esperienza di completamento automatico alla casella di ricerca effettuando chiamate asincrone al controller MVC per recuperare suggerimenti. La versione del linguaggio JavaScript si trova in IndexJavaScript.cshtml. Include lo script seguente per la barra di ricerca, nonché le chiamate dell'API REST a Ricerca cognitiva di Azure.

A questo punto verrà esaminato il codice JavaScript per il primo esempio, che chiama la funzione di completamento automatico dell'interfaccia utente di jQuery, passando una richiesta di suggerimenti:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Questo codice viene eseguito nel browser al caricamento della pagina per configurare il completamento automatico dell'interfaccia utente di jQuery per la casella di input "example1a".  `minLength: 3` assicura che i consigli verranno visualizzati solo quando sono presenti almeno tre caratteri nella casella di ricerca.  Il valore di origine è importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Questa riga indica alla funzione di completamento automatico dell'interfaccia utente di jQuery dove recuperare l'elenco di voci da visualizzare sotto la casella di ricerca. Poiché questo progetto è un progetto MVC, chiama la funzione Suggest in HomeController.cs che contiene la logica per la restituzione di suggerimenti per le query (altre informazioni sulla funzione Suggest verranno fornite nella sezione successiva). Questa funzione passa anche alcuni parametri per controllare evidenziazioni, corrispondenze fuzzy e termini. L'API JavaScript di completamento automatico aggiunge il parametro per i termini.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Ingrandire l'esempio per supportare la corrispondenza fuzzy

La ricerca fuzzy consente di ottenere risultati in base alle corrispondenze simili, anche se l'utente commette errori di digitazione di una parola nella casella di ricerca. Sebbene non sia necessario, migliora significativamente l'affidabilità di un'esperienza di completamento automatico. Proviamo a farlo modificando la riga di origine per abilitare la corrispondenza fuzzy.

Sostituisci la riga seguente da:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

con questo:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Avvia l'applicazione premendo F5.

Provare a digitare qualcosa, ad esempio "execative" e nota come vengono restituiti risultati per "executive", anche se non si tratta di una corrispondenza esatta con le lettere digitate.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Completamento automatico di jQuery supportato dal completamento automatico di Ricerca cognitiva di Azure

Fino a questo momento, il codice dell'esperienza utente di ricerca è stato centrato sui suggerimenti. Il blocco di codice successivo mostra la funzione di completamento automatico dell'interfaccia utente di jQuery (riga 91 in index. cshtml), passando una richiesta per il completamento automatico di Ricerca cognitiva di Azure:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Esempio in C#

Dopo aver esaminato il codice JavaScript per la pagina Web, verrà esaminato quello del controller C# sul lato server che recupera effettivamente le corrispondenze suggerite tramite .NET SDK di Ricerca cognitiva di Azure.

Aprire il file **HomeController.cs** nella directory Controllers. 

Il primo elemento che si osserva è un metodo nella parte superiore della classe denominata `InitSearch`. Questo metodo genererà un client di indice HTTP autenticato nel servizio di ricerca cognitiva di Azure. Per altre informazioni, vedere [Come usare Ricerca cognitiva di Azure da un'applicazione .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Alla riga 41, si noti la funzione Suggest. È basata sul [metodo DocumentsOperationsExtensions.suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

La funzione Suggest include due parametri che determinano se vengono restituiti i risultati evidenziati o se la corrispondenza fuzzy è utilizzata in completamento all'inserimento del termine di ricerca. Il metodo crea un [oggetto SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet) che viene quindi passato all'API Suggest. Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

Alla riga 69, si noti la funzione Autocomplete. È basata sul [metodo DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

La funzione Autocomplete accetta l'input del termine di ricerca. Il metodo crea un [oggetto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

(Facoltativo) Aggiungi un punto di interruzione all'inizio della funzione Suggest ed esegui il codice passo a passo. Notare la risposta restituita dall'SDK e come viene convertita nel risultato restituito dal metodo.

Gli altri esempi nella pagina seguono lo stesso modello per aggiungere l'evidenziazione dei risultati e i facet per supportare la memorizzazione nella cache lato client dei risultati del completamento automatico. Esamina ciascuno di questi elementi per comprendere come funzionano e come usarli al meglio nella tua esperienza di ricerca.

## <a name="javascript-example"></a>Esempio JavaScript

Un'implementazione JavaScript di completamento automatico e suggerimenti chiama l'API REST, usando un URI come origine per specificare l'indice e l'operazione. 

Per esaminare l'implementazione JavaScript, aprire il file **IndexJavaScript.cshtml**. Si noti che la funzione di completamento automatico dell'interfaccia utente di jQuery viene usata anche per la casella di ricerca, raccogliendo gli input dei termini di ricerca ed effettuando chiamate asincrone a Ricerca cognitiva di Azure per recuperare le corrispondenze suggerite o i termini completati. 

Esaminiamo il codice JavaScript nel primo esempio:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Se lo si confronta con l'esempio precedente che chiama il controller Home, si osserveranno numerose analogie.  La configurazione del completamento automatico per `minLength` e `position` è identica. 

La modifica sostanziale risiede nell'origine. Invece di chiamare il metodo Suggest nel controller Home, una richiesta REST viene creata in una funzione JavaScript ed eseguita tramite ajax. La risposta viene quindi elaborata in "success" e utilizzare come origine.

Le chiamate REST usano gli URI per specificare se viene eseguita una chiamata API di [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) o [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions). Gli URI seguenti sono rispettivamente alle righe 9 e 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Alla riga 148 è possibile trovare uno script che chiama `autocompleteUri`. La prima chiamata a `suggestUri` è alla riga 39.

> [!Note]
> L'esecuzione di chiamate REST al servizio in JavaScript viene riportata qui ai fini di una dimostrazione pratica dell'API REST, ma non deve essere interpretata come procedura consigliata o raccomandazione. L'inclusione di una chiave API e di un endpoint in uno script apre il servizio ad attacchi di tipo Denial of Service a chiunque possa leggere tali valori dallo script. Sebbene sia sicuro usare JavaScript ai fini dell'apprendimento, ad esempio negli indici ospitati nel servizio gratuito, è consigliabile usare C# o Java per l'indicizzazione e le operazioni di query nel codice di produzione. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurare NYCJobs per l'esecuzione nel servizio

Fino ad adesso, è stato usato l'indice demo di NYCJobs ospitato. Per una visibilità completa su tutto il codice, incluso l'indice, seguire queste istruzioni per creare e caricare l'indice nel proprio servizio di ricerca.

1. [Creare un servizio di ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. Per questo esempio è possibile usare un servizio gratuito. 

   > [!Note]
   > Se si usa il servizio di ricerca cognitiva di Azure gratuito, è previsto un limite di tre indici. Il caricatore dati NYCJobs crea due indici. Assicurati di disporre dello spazio necessario perché il servizio possa per ospitare i nuovi indici.

1. Scaricare il codice di esempio [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

1. Nella cartella DataLoader del codice di esempio NYCJobs aprire **DataLoader.sln** in Visual Studio.

1. Aggiungere le informazioni di connessione per il servizio di ricerca cognitiva di Azure. Aprire App.config all'interno del progetto DataLoader e modifica le impostazioni app TargetSearchServiceName e TargetSearchServiceApiKey affinché riflettano il servizio di ricerca cognitiva di Azure e la chiave API del servizio di ricerca cognitiva di Azure. Queste informazioni sono disponibili nel portale di Azure.

1. Premere F5 per avviare l'applicazione, creare due indici e importare i dati di esempio NYCJob.

1. Aprire **AutocompleteTutorial.sln** e modificare Web.config nel progetto **AutocompleteTutorial**. Modificare i valori di SearchServiceName e SearchServiceApiKey e impostarli su valori validi per il servizio di ricerca.

1. Premere F5 per eseguire l'applicazione. L'app Web di esempio viene aperta nel browser predefinito. L'esperienza è identica a quella della versione sandbox, ma l'indice e i dati sono ospitati nel servizio dell'utente.

## <a name="next-steps"></a>Passaggi successivi

Questa esempio illustra i passaggi di base per la creazione di una casella di ricerca che supporta il completamento automatico e i suggerimenti. È stato illustrato come è possibile creare un'applicazione MVC ASP.NET e usare .NET SDK o l'API REST di Ricerca cognitiva di Azure per recuperare i suggerimenti.

Il passaggio successivo prevede l'integrazione dei suggerimenti e del completamento automatico nell'esperienza di ricerca. Per altre informazioni, vedere gli articoli di riferimento seguenti.

> [!div class="nextstepaction"]
> [L'API REST Completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST per i consigli](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Attributo dell'indice dei facet in un'API REST di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index)

