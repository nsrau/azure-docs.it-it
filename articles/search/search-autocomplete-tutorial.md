---
title: 'Aggiungere suggerimenti e completamento automatico in una casella di ricerca: ricerca di Azure'
description: Abilitare le azioni di query typeahead in ricerca di Azure creando suggerimenti e formulando richieste che compilano una casella di ricerca con termini o frasi completate.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183284"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Aggiungere suggerimenti o completare il completamento automatico nell'applicazione di ricerca di Azure

In questo articolo viene illustrato come utilizzare i [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) e il [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) per creare una casella di ricerca potente che supporti i comportamenti di ricerca in base al tipo.

+ I *suggerimenti* sono i risultati suggeriti generati durante la digitazione, dove ogni suggerimento è un singolo risultato dall'indice che corrisponde a quello digitato finora. 

+ *Completamento automatico* "termina" la parola o la frase che un utente sta digitando. Anziché restituire risultati, viene completata una query, che è quindi possibile eseguire per restituire i risultati. Come per i suggerimenti, una parola o una frase completata in una query viene predicata in base a una corrispondenza nell'indice. Il servizio non offrirà query che restituiscono zero risultati nell'indice.

È possibile scaricare ed eseguire il codice di esempio in **DotNetHowToAutocomplete** per valutare queste funzionalità. Il codice di esempio è destinato a un indice predefinito popolato con [i dati demo di NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). L'indice NYCJobs contiene un [costrutto suggeritore](index-add-suggesters.md), che è un requisito per l'utilizzo di suggerimenti o di completamento automatico. È possibile usare l'indice preparato ospitato in un servizio sandbox oppure [popolare il proprio indice](#configure-app) usando un caricatore di dati nella soluzione di esempio NYCJobs. 

L'esempio **DotNetHowToAutocomplete** illustra sia i suggerimenti che il completamento automatico, C# sia in che nelle versioni in linguaggio JavaScript. C#gli sviluppatori possono passare da un'applicazione basata su MVC ASP.NET che usa [Azure search .NET SDK](https://aka.ms/search-sdk). La logica per eseguire il completamento automatico e le chiamate di query suggerite si trova nel file HomeController.cs. Gli sviluppatori JavaScript troveranno la logica di query equivalente in IndexJavaScript. cshtml, che include chiamate dirette all' [API REST di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Per entrambe le versioni del linguaggio, l'esperienza utente front-end si basa sull' [interfaccia utente jQuery](https://jqueryui.com/autocomplete/) e sulle librerie [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) . Queste librerie vengono usate per compilare la casella di ricerca che supporta i suggerimenti e il completamento automatico. Gli input raccolti nella casella di ricerca sono abbinati ai suggerimenti e alle azioni di completamento automatico, ad esempio quelli definiti in HomeController.cs o IndexJavaScript. cshtml.

Questo esercizio illustra le attività seguenti:

> [!div class="checklist"]
> * Implementare una casella di input di ricerca in JavaScript e inviare richieste per corrispondenze suggerite o termini autocompletati
> * In C#, definire i suggerimenti e le azioni di completamento automatico in HomeController.cs
> * In JavaScript chiamare direttamente le API REST per fornire la stessa funzionalità

## <a name="prerequisites"></a>Prerequisiti

Un servizio di ricerca di Azure è facoltativo per questo esercizio perché la soluzione USA un servizio sandbox attivo che ospita un indice demo NYCJobs preparato. Se si vuole eseguire questo esempio nel servizio di ricerca, vedere Configurare l' [Indice dei processi NYC](#configure-app) per istruzioni.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

* Scaricare l' [esempio DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

L'esempio è completo, per la copertura di suggerimenti, il completamento automatico, l'esplorazione in base a facet e la memorizzazione nella cache sul lato client. Per una descrizione completa dell'offerta dell'esempio, vedere il file Leggimi e i commenti.

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Aprire **AutocompleteTutorial. sln** in Visual Studio. La soluzione contiene un progetto MVC ASP.NET con una connessione all'indice demo dei processi NYC.

2. Premi F5 per far partire il progetto e caricare la pagina nel browser che preferisci.

Nella parte superiore, vedrai un'opzione per la selezione di C# o JavaScript. L' C# opzione chiama HomeController dal browser e USA Azure search .NET SDK per recuperare i risultati. 

L'opzione JavaScript chiama l'API REST di Ricerca di Azure direttamente dal browser. Questa opzione ha in genere prestazioni nettamente migliori perché esclude i controller dal flusso. È possibile scegliere l'opzione adatta alle proprie esigenze e preferenze linguistiche. Nella pagina sono disponibili alcuni esempi di completamento automatico con istruzioni per ognuno. Ogni esempio presenta un testo di esempio consigliato che è possibile provare.  

Prova a digitare alcune lettere in ciascuna casella di ricerca per scoprire cosa succede.

## <a name="search-box"></a>Casella di ricerca

Per entrambe C# le versioni e JavaScript, l'implementazione della casella di ricerca è esattamente la stessa. 

Aprire il file **index. cshtml** nella cartella \Views\Home per visualizzare il codice:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Questo esempio è una casella di testo di input semplice con una classe per lo stile, un ID a cui fa riferimento JavaScript e il testo segnaposto.  Il Magic è il linguaggio JavaScript incorporato.

Nell' C# esempio di linguaggio viene usato JavaScript in index. cshtml per sfruttare la [libreria di completamento automatico dell'interfaccia utente di jQuery](https://jqueryui.com/autocomplete/). Questa libreria aggiunge l'esperienza di completamento automatico alla casella di ricerca effettuando chiamate asincrone al controller MVC per recuperare i suggerimenti. La versione del linguaggio JavaScript è in IndexJavaScript. cshtml. Include lo script seguente per la barra di ricerca, nonché le chiamate API REST a ricerca di Azure.

Esaminiamo il codice JavaScript per il primo esempio, che chiama la funzione di completamento automatico dell'interfaccia utente di jQuery, passando una richiesta di suggerimenti:

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

Il codice precedente viene eseguito nel browser al caricamento della pagina per configurare il completamento automatico dell'interfaccia utente jQuery per la casella di input "example1a".  `minLength: 3` assicura che i consigli verranno visualizzati solo quando sono presenti almeno tre caratteri nella casella di ricerca.  Il valore di origine è importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

La riga sopra indicata indica alla funzione di completamento automatico dell'interfaccia utente di jQuery dove ottenere l'elenco di elementi da visualizzare nella casella di ricerca. Poiché questo progetto è un progetto MVC, chiama la funzione suggest in HomeController.cs che contiene la logica per la restituzione di suggerimenti per la query (altre informazioni sul suggerimento nella sezione successiva). Questa funzione passa anche alcuni parametri per controllare le evidenziazioni, la corrispondenza fuzzy e il termine. L'API JavaScript di completamento automatico aggiunge il parametro per i termini.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Ingrandire l'esempio per supportare la corrispondenza fuzzy

La ricerca fuzzy consente di ottenere risultati in base alle corrispondenze simili, anche se l'utente commette errori di digitazione di una parola nella casella di ricerca. Sebbene non sia necessario, migliora significativamente l'affidabilità di un'esperienza di typeahead. Proviamo a farlo modificando la riga di origine per abilitare la corrispondenza fuzzy.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>completamento automatico jQuery supportato da ricerca di Azure

Fino ad ora, il codice UX di ricerca è stato centrato sui suggerimenti. Il blocco di codice successivo Mostra la funzione di completamento automatico dell'interfaccia utente di jQuery (riga 91 in index. cshtml), passando una richiesta di completamento automatico di ricerca di Azure:

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

Ora che è stato esaminato il codice JavaScript per la pagina Web, esaminiamo il C# codice del controller lato server che recupera effettivamente le corrispondenze suggerite usando Azure search .NET SDK.

Aprire il file **HomeController.cs** nella directory Controllers. 

La prima cosa che si può notare è un metodo nella parte superiore della classe denominata `InitSearch`. Questo metodo crea un client di indice HTTP autenticato per il servizio ricerca di Azure. Per altre informazioni, vedere [come usare ricerca di Azure da un'applicazione .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Alla riga 41, si noti la funzione Suggerisci. Si basa sul [Metodo DocumentsOperationsExtensions. suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

La funzione Suggest include due parametri che determinano se vengono restituiti i risultati evidenziati o se la corrispondenza fuzzy è utilizzata in completamento all'inserimento del termine di ricerca. Il metodo crea un [oggetto SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), che viene quindi passato all'API suggest. Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

Alla riga 69, si noti la funzione di completamento automatico. Si basa sul [Metodo DocumentsOperationsExtensions. AutoComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

La funzione di completamento automatico accetta l'input del termine di ricerca. Il metodo crea un [oggetto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

(Facoltativo) Aggiungi un punto di interruzione all'inizio della funzione Suggest ed esegui il codice passo a passo. Si noti la risposta restituita dall'SDK e la relativa modalità di conversione nel risultato restituito dal metodo.

Gli altri esempi nella pagina seguono lo stesso modello per aggiungere l'evidenziazione dei risultati e i facet per supportare la memorizzazione nella cache sul lato client dei risultati di completamento automatico. Esamina ciascuno di questi elementi per comprendere come funzionano e come usarli al meglio nella tua esperienza di ricerca.

## <a name="javascript-example"></a>Esempio JavaScript

Un'implementazione JavaScript di completamento automatico e suggerimenti chiama l'API REST, usando un URI come origine per specificare l'indice e l'operazione. 

Per esaminare l'implementazione di JavaScript, aprire **IndexJavaScript. cshtml**. Si noti che la funzione di completamento automatico dell'interfaccia utente jQuery viene usata anche per la casella di ricerca, raccogliendo gli input dei termini di ricerca ed effettuando chiamate asincrone a ricerca di Azure per recuperare le corrispondenze suggerite o i termini 

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

Se si confronta questo esempio con l'esempio precedente che chiama il controller Home, si noteràno diverse analogie.  La configurazione del completamento automatico per `minLength` e `position` è identica. 

La modifica sostanziale risiede nell'origine. Anziché chiamare il metodo suggest nel controller Home, viene creata una richiesta REST in una funzione JavaScript ed eseguita con AJAX. La risposta viene quindi elaborata in "success" e utilizzare come origine.

Le chiamate REST usano gli URI per specificare se viene eseguita una chiamata API di [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) o [suggestioni](https://docs.microsoft.com/rest/api/searchservice/suggestions) . Gli URI seguenti sono rispettivamente sulle righe 9 e 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Alla riga 148 è possibile trovare uno script che chiama `autocompleteUri`. La prima chiamata a `suggestUri` è alla riga 39.

> [!Note]
> L'esecuzione di chiamate REST al servizio in JavaScript viene offerta qui come una dimostrazione pratica dell'API REST, ma non deve essere interpretata come procedura consigliata o consigliata. L'inclusione di una chiave API e di un endpoint in uno script consente di aprire il servizio fino a attacchi di tipo Denial of Service a chiunque possa leggere tali valori dallo script. Sebbene sia sicuro usare JavaScript ai fini dell'apprendimento, ad esempio negli indici ospitati nel servizio gratuito, è consigliabile usare C# Java o per l'indicizzazione e le operazioni di query nel codice di produzione. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurare NYCJobs per l'esecuzione nel servizio

Fino ad ora, è stato usato l'indice demo di NYCJobs ospitato. Se si vuole ottenere la visibilità completa di tutto il codice, incluso l'indice, seguire queste istruzioni per creare e caricare l'indice nel servizio di ricerca.

1. [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. Per questo esempio è possibile usare un servizio gratuito. 

   > [!Note]
   > Se usi il servizio Ricerca di Azure gratuito, hai un limite di tre indici. Il caricatore dati NYCJobs crea due indici. Assicurati di disporre dello spazio necessario perché il servizio possa per ospitare i nuovi indici.

1. Scaricare il codice di esempio [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) .

1. Nella cartella Dataloader del codice di esempio NYCJobs aprire **Dataloader. sln** in Visual Studio.

1. Aggiungere le informazioni di connessione per il servizio ricerca di Azure. Apri App.config all'interno del progetto DataLoader e modifica le appSettings TargetSearchServiceName e TargetSearchServiceApiKey in modo tale da rispecchiare il servizio di Ricerca di Azure e la chiave API del servizio di Ricerca di Azure. Queste informazioni sono disponibili nella portale di Azure.

1. Premere F5 per avviare l'applicazione, creare due indici e importare i dati di esempio NYCJob.

1. Aprire **AutocompleteTutorial. sln** e modificare il file Web. config nel progetto **AutocompleteTutorial** . Modificare i valori SearchServiceName e SearchServiceApiKey in valori validi per il servizio di ricerca.

1. Premere F5 per eseguire l'applicazione. L'app Web di esempio viene aperta nel browser predefinito. L'esperienza è identica alla versione sandbox, solo l'indice e i dati sono ospitati nel servizio.

## <a name="next-steps"></a>Passaggi successivi

In questo esempio vengono illustrati i passaggi di base per la creazione di una casella di ricerca che supporta il completamento automatico e i suggerimenti. È stato illustrato come creare un'applicazione ASP.NET MVC e usare Azure search .NET SDK o l'API REST per recuperare i suggerimenti.

Come passaggio successivo, provare a integrare i suggerimenti e a completare l'esperienza di ricerca. Gli articoli di riferimento seguenti dovrebbero essere utili.

> [!div class="nextstepaction"]
> [L'API REST Completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST per i consigli](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Attributo dell'indice dei facet in un'API REST di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index)

