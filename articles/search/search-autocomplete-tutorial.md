---
title: Aggiungere i suggerimenti e completamento automatico in una casella di ricerca - ricerca di Azure
description: Abilitare azioni di query typeahead in ricerca di Azure tramite la creazione di componenti per il suggerimento e formulare le richieste che è riempire in una casella di ricerca con completati termini o frasi.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: ed2e0bd352823a932cfea719c18e05ae6c913621
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495737"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Esempio: Aggiungere il completamento automatico o suggerimenti all'applicazione di ricerca di Azure

In questo articolo, informazioni su come usare [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) per compilare una casella di ricerca avanzata che supporta i comportamenti di ricerca---digitazione.

+ *I suggerimenti* vengono suggeriti i risultati generati durante la digitazione, in cui ogni suggerimento è un singolo risultato dall'indice che corrisponde a quanto digitato finora. 

+ *Completamento automatico*, [una funzionalità di anteprima](search-api-preview.md), "completa", la parola o frase che un utente è attualmente la digitazione. Invece di restituire i risultati, il completamento di una query, che è quindi possibile eseguire per restituire i risultati. Come con i suggerimenti, una completato parola o frase in una query viene affermata su una corrispondenza dell'indice. Il servizio non offerta le query che restituiscono zero risultati nell'indice.

È possibile scaricare ed eseguire il codice di esempio **DotNetHowToAutocomplete** valutare queste funzionalità. Il codice di esempio è destinato a un indice predefinito popolato con [NYCJobs demo dati](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). L'indice NYCJobs contiene un [costrutto dello strumento suggerimenti](index-add-suggesters.md), che rappresenta un requisito per l'uso di suggerimenti o il completamento automatico. È possibile usare l'indice preparata ospitata in un servizio sandbox, oppure [popolare il proprio indice](#configure-app) usando un caricatore dei dati nella soluzione di esempio NYCJobs. 

Il **DotNetHowToAutocomplete** esempio di seguito viene illustrato come sia i suggerimenti e il completamento automatico, sia in C# e le versioni di linguaggio JavaScript. C#gli sviluppatori possono eseguire un'applicazione basata su ASP.NET MVC che usa il [Azure Search .NET SDK](https://aka.ms/search-sdk). La logica per il completamento automatico e le chiamate di query suggeriti è reperibile nel file HomeController.cs. Gli sviluppatori JavaScript per la logica di query equivalenti nella troveranno IndexJavaScript.cshtml, che include le chiamate dirette al [API REST di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Per entrambe le versioni della lingua, l'esperienza utente front-end si basa sul [interfaccia utente di jQuery](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) librerie. Utilizziamo queste librerie per compilare la casella di ricerca che supporta sia i suggerimenti e il completamento automatico. Gli input raccolti nella casella di ricerca vengono associati i suggerimenti e le azioni di completamento automatico, ad esempio quelli come definito in HomeController.cs o IndexJavaScript.cshtml.

Questo esercizio illustra le attività seguenti:

> [!div class="checklist"]
> * Implementare una casella di input di ricerca in JavaScript ed emettere richieste per le possibili corrispondenze o condizioni di completamento automatico
> * In C#, definire le azioni di completamento automatico e suggerimenti in HomeController.cs
> * In JavaScript, chiamare le API REST direttamente per fornire la stessa funzionalità

## <a name="prerequisites"></a>Prerequisiti

Un servizio di ricerca di Azure è facoltativo per questo esercizio poiché la soluzione Usa un servizio sandbox in tempo reale che ospita un indice demo NYCJobs preparato. Se si vuole eseguire in questo esempio nel proprio servizio di ricerca, vedere [indice dei processi NYC configurare](#configure-app) per le istruzioni.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

* Scaricare il [DotNetHowToAutoComplete esempio](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

L'esempio è completo, i suggerimenti di copertura, completamento automatico, facet e la memorizzazione nella cache lato client. È consigliabile esaminare il file Leggimi e commenti per una descrizione completa di offerte di esempio.

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Aprire **AutocompleteTutorial.sln** in Visual Studio. La soluzione contiene un progetto MVC ASP.NET con una connessione all'indice demo dei processi di New YORK.

2. Premi F5 per far partire il progetto e caricare la pagina nel browser che preferisci.

Nella parte superiore, vedrai un'opzione per la selezione di C# o JavaScript. Il C# opzione chiama la classe HomeController dal browser e Usa Azure Search .NET SDK per recuperare i risultati. 

L'opzione JavaScript chiama l'API REST di Ricerca di Azure direttamente dal browser. Questa opzione ha in genere prestazioni nettamente migliori perché esclude i controller dal flusso. È possibile scegliere l'opzione adatta alle proprie esigenze e preferenze linguistiche. Nella pagina sono disponibili alcuni esempi di completamento automatico con istruzioni per ognuno. Ogni esempio presenta un testo di esempio consigliato che è possibile provare.  

Prova a digitare alcune lettere in ciascuna casella di ricerca per scoprire cosa succede.

## <a name="search-box"></a>Casella di ricerca

Sia per C# e versioni di JavaScript, l'implementazione di casella di ricerca è esattamente la stessa. 

Aprire il **index. cshtml** file sotto la cartella \Views\Home per visualizzare il codice:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Si tratta di una casella di testo di input semplice con una classe per lo stile, un ID a cui fa riferimento JavaScript e testo segnaposto.  Il magic è il codice JavaScript incorporato.

Il C# esempio di linguaggio Usa JavaScript in Index. cshtml per sfruttare i [libreria di completamento automatico dell'interfaccia utente di jQuery](https://jqueryui.com/autocomplete/). Questa libreria offre l'esperienza di completamento automatico nella casella di ricerca di effettuando chiamate asincrone ai controller MVC per recuperare i suggerimenti. La versione del linguaggio JavaScript è in IndexJavaScript.cshtml. Include lo script seguente per la barra di ricerca, nonché chiamate all'API REST a ricerca di Azure.

Esaminiamo il codice JavaScript nel primo esempio, che chiama una funzione di completamento automatico dell'interfaccia utente, il passaggio in una richiesta per i suggerimenti di jQuery:

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

Il codice sopra riportato viene eseguito nel browser al caricamento della pagina per configurare il completamento automatico dell'interfaccia utente di jQuery per la casella di input "example1a".  `minLength: 3` assicura che i consigli verranno visualizzati solo quando sono presenti almeno tre caratteri nella casella di ricerca.  Il valore di origine è importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

La riga precedente indica alla funzione di completamento automatico dell'interfaccia utente di jQuery come ottenere l'elenco di elementi da visualizzare sotto la casella di ricerca. Poiché si tratta di un progetto MVC, chiama la funzione Suggest in HomeController.cs che contiene la logica per la restituzione di suggerimenti di query (ulteriori informazioni su Suggerisci nella sezione successiva). Questa funzione passa anche alcuni parametri al controllo Evidenzia la corrispondenza fuzzy e termini. L'API JavaScript di completamento automatico aggiunge il parametro per i termini.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Ingrandire l'esempio per supportare la corrispondenza fuzzy

La ricerca fuzzy consente di ottenere risultati in base alle corrispondenze simili, anche se l'utente commette errori di digitazione di una parola nella casella di ricerca. Sebbene non sia necessario, in modo significativo migliora l'affidabilità dell'esperienza di completamento automatico. Proviamo a farlo modificando la riga di origine per abilitare la corrispondenza fuzzy.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>completamento automatico supportato da ricerca di Azure il completamento automatico jQuery

Fino ad ora, la ricerca di codice dell'esperienza utente è stata centrata sui suggerimenti. Il blocco di codice successivo mostra la funzione di completamento automatico dell'interfaccia utente di jQuery (riga 91 in Index. cshtml), passando una richiesta per il completamento automatico di ricerca di Azure:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
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

Dopo aver esaminato il codice JavaScript per la pagina web, è possibile esaminare il C# codice del controller sul lato server che consente di recuperare effettivamente le corrispondenze suggerite usando .NET SDK ricerca di Azure.

Aprire il **HomeController.cs** file nella directory del controller. 

La prima cosa si può notare è un metodo nella parte superiore della classe chiamata `InitSearch`. Questo genererà un client di indice HTTP autenticato nel servizio Ricerca di Azure. Per altre informazioni, vedere [come usare ricerca di Azure da un'applicazione .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Nella riga 41, si noti che la funzione Suggest. Si basa il [DocumentsOperationsExtensions.Suggest metodo](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

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

La funzione Suggest include due parametri che determinano se vengono restituiti i risultati evidenziati o se la corrispondenza fuzzy è utilizzata in completamento all'inserimento del termine di ricerca. Il metodo crea un' [SuggestParameters oggetto](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), che viene quindi passata all'API di suggerire. Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

Nella riga 69, si noti che la funzione di completamento automatico. Si basa il [DocumentsOperationsExtensions.Autocomplete metodo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

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

La funzione di completamento automatico accetta l'input di termine di ricerca. Il metodo crea un' [AutoCompleteParameters oggetto](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

(Facoltativo) Aggiungi un punto di interruzione all'inizio della funzione Suggest ed esegui il codice passo a passo. Si noti che la risposta restituita dal SDK e come viene convertito nel risultato restituito dal metodo.

Gli altri esempi nella pagina di seguano lo stesso modello per aggiungere l'evidenziazione dei riscontri e i facet per supportare la memorizzazione nella cache lato client dei risultati di completamento automatico. Esamina ciascuno di questi elementi per comprendere come funzionano e come usarli al meglio nella tua esperienza di ricerca.

## <a name="javascript-example"></a>Esempio JavaScript

Un'implementazione Javascript di completamento automatico e i suggerimenti chiama l'API REST, usando un URI come origine per specificare l'indice e l'operazione. 

Per controllare l'implementazione JavaScript, aprire **IndexJavaScript.cshtml**. Si noti che la funzione di completamento automatico dell'interfaccia utente di jQuery viene usata anche per la casella di ricerca, la raccolta di input di termine di ricerca e chiamate asincrone a ricerca di Azure per il recupero suggerito corrispondenze o completato termini. 

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

Se lo si confronta con l'esempio precedente che chiama il controller Home, noterai diverse analogie.  La configurazione del completamento automatico per `minLength` e `position` è identica. 

La modifica sostanziale risiede nell'origine. Invece di chiamare il metodo Suggerisci nel controller home, una richiesta REST viene creata in una funzione JavaScript e viene eseguito tramite Ajax. La risposta viene quindi elaborata in "success" e utilizzare come origine.

Le chiamate REST utilizzano gli URI per specificare se un' [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) o [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) è stata effettuata la chiamata API. Gli URI seguenti si trovano su righe 9 e 10, rispettivamente.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Nella riga 148, è possibile trovare uno script che chiama il `autocompleteUri`. La prima chiamata a `suggestUri` si trova nella riga 39.

> [!Note]
> Eseguire chiamate REST al servizio in JavaScript è disponibile come una dimostrazione pratica dell'API REST, ma non devono essere interpretate come una procedura consigliata o l'indicazione. L'inclusione di una chiave API e l'endpoint in uno script viene aperto il servizio fino a attacchi denial of service a chiunque può leggere tali valori disattivare lo script. Durante il relativo safe per utilizzare JavaScript ai fini dell'apprendimento, ad esempio relative agli indici ospitati nel servizio gratuito, è consigliabile usare Java o C# per le operazioni di indicizzazione e query nel codice di produzione. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurare NYCJobs per l'esecuzione nel servizio

Fino ad ora, si sta usando l'indice di demo NYCJobs ospitato. Se si desidera che la visibilità completa in tutto il codice, tra cui l'indice, seguire queste istruzioni per creare e caricare l'indice nel servizio di ricerca.

1. [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo esempio. 

   > [!Note]
   > Se usi il servizio Ricerca di Azure gratuito, hai un limite di tre indici. Il caricatore dati NYCJobs crea due indici. Assicurati di disporre dello spazio necessario perché il servizio possa per ospitare i nuovi indici.

1. Scaricare [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) esempi di codice.

1. Nella cartella del codice di esempio NYCJobs DataLoader, aprire **DataLoader.sln** in Visual Studio.

1. Aggiungere le informazioni di connessione per il servizio di ricerca di Azure. Apri App.config all'interno del progetto DataLoader e modifica le appSettings TargetSearchServiceName e TargetSearchServiceApiKey in modo tale da rispecchiare il servizio di Ricerca di Azure e la chiave API del servizio di Ricerca di Azure. Questi sono rintracciabili nel portale di Azure.

1. Premere F5 per avviare l'applicazione, creazione di due indici non cluster e l'importazione dei dati di esempio NYCJob.

1. Aprire **AutocompleteTutorial.sln** e modificare il file Web. config nel **AutocompleteTutorial** progetto. Modificare i valori SearchServiceName e searchserviceapikey in base ai valori validi per il servizio di ricerca.

1. Premere F5 per eseguire l'applicazione. App web di esempio apre nel browser predefinito. L'esperienza è identica a quella di sandbox, solo l'indice e i dati sono ospitati nel servizio.

## <a name="next-steps"></a>Passaggi successivi

Questo esempio illustra i passaggi di base per la creazione di una casella di ricerca che supporta la funzionalità Completamento automatico e i suggerimenti. È stato illustrato come è possibile compilare un'applicazione ASP.NET MVC e usare Azure Search .NET SDK o l'API REST per recuperare i suggerimenti.

Come passaggio successivo, il tentativo di integrazione di completamento automatico e suggerimenti nell'esperienza di ricerca. Gli articoli di riferimento seguenti potrebbero essere utili.

> [!div class="nextstepaction"]
> [L'API REST Completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST per i consigli](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Attributo dell'indice dei facet in un'API REST di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index)

