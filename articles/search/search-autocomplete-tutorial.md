---
title: Esercitazione per l'aggiunta del completamento automatico alla casella di ricerca tramite Ricerca di Azure | Microsoft Docs
description: Esempi di come migliorare l'esperienza dell'utente finale in riferimento alle applicazioni basate sui dati tramite il completamento automatico e i suggerimenti API di Ricerca di Azure.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: mcarter
ms.openlocfilehash: 7120080bfdc188c150c7065e1c0639ab8c04f173
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989685"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Esercitazione: aggiunta del completamento automatico alla casella di ricerca tramite Ricerca di Azure

In questa esercitazione apprenderai come usare i [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions), il [completamento automatico](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete) e i [facet](search-faceted-navigation.md) nell'[API REST](https://docs.microsoft.com/rest/api/searchservice/) e in [.NET SDK di Ricerca di Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) per creare una casella di ricerca avanzata. I *suggerimenti* forniscono consigli sui risultati effettivi in base a ciò che l'utente ha digitato. *Completamento automatico*, [una nuova funzionalità di anteprima](search-api-preview.md) in Ricerca di Azure, fornisce i termini provenienti dall'indice per il completamento di ciò che l'utente sta attualmente digitando. Verranno confrontate diverse tecniche per migliorare la produttività degli utenti e per trovare rapidamente e facilmente ciò che cercano arricchendo la dell'utente ricerca direttamente durante la digitazione.

Questa esercitazione esplora le caratteristiche di un'applicazione basata su ASP.NET MVC che usa C# per chiamare le [librerie client .NET di Ricerca di Azure](https://aka.ms/search-sdk)e JavaScript per chiamare direttamente l'API REST di Ricerca di Azure. L'applicazione di questa esercitazione ha come riferimento un indice popolato da dati campione di [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). È possibile usare l'indice già configurato nella demo di NYC Jobs o popolare il proprio indice utilizzando un caricatore dati nella soluzione di esempio NYCJobs. L'esempio usa l'[interfaccia utente di jQuery](https://jqueryui.com/autocomplete/) e le librerie JavaScript [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) per creare una casella di ricerca che supporti il completamento automatico. Usando questi componenti unitamente a Ricerca di Azure, noterai diversi esempi relativi alle modalità di supporto al completamento automatico con digitazione nella casella di ricerca.

Eseguirai le seguenti attività:

> [!div class="checklist"]
> * Scaricare e configurare la soluzione
> * Aggiungere le informazioni sul servizio di ricerca alle impostazioni dell'applicazione
> * Implementare un riquadro di input di ricerca
> * Aggiungere un supporto per un elenco di completamento automatico i cui dati sono stati spostati in modo coatto da un'origine remota 
> * Recuperare i suggerimenti e il completamento automatico tramite l'API REST e .Net SDK
> * Supportare la memorizzazione nella cache lato client per migliorare le prestazioni 

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio 2017. È possibile usare la versione [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) gratuita. 

* Scarica il [codice sorgente](https://github.com/azure-samples/search-dotnet-getting-started) di esempio per l'esercitazione.

* (Facoltativo) Un account Azure attivo e un servizio di Ricerca di Azure. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/). Per assistenza sul provisioning del servizio, vedi [Creare un servizio di ricerca](search-create-service-portal.md). L'account e il servizio sono facoltativi in quanto questa esercitazione può essere completata usando un indice NYCJobs ospitato già presente per una demo diversa.

* (Facoltativo) Scarica il codice di esempio [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) per importare i dati NYCJobs in un indice all'interno del servizio di Ricerca di Azure.

> [!Note]
> Se usi il servizio Ricerca di Azure gratuito, hai un limite di tre indici. Il caricatore dati NYCJobs crea due indici. Assicurati di disporre dello spazio necessario perché il servizio possa per ospitare i nuovi indici.

### <a name="set-up-azure-search-optional"></a>Configurare la Ricerca di Azure (facoltativo)

Segui i passaggi descritti in questa sezione se vuoi importare i dati per l'applicazione di esempio NYCJobs nel tuo indice. Questo passaggio è facoltativo.  Se vuoi usare l'indice di esempio fornito, passa direttamente alla prossima sezione, iniziando l’esecuzione dell'esempio.

1. Nella cartella DataLoader del codice di esempio NYCJobs, apri il file di soluzione DataLoader.sln in Visual Studio.

1. Aggiorna le informazioni di connessione per il servizio di Ricerca di Azure.  Apri App.config all'interno del progetto DataLoader e modifica le appSettings TargetSearchServiceName e TargetSearchServiceApiKey in modo tale da rispecchiare il servizio di Ricerca di Azure e la chiave API del servizio di Ricerca di Azure.  Questi sono rintracciabili nel portale di Azure.

1. Premi F5per avviare l'applicazione.  Questa operazione crea 2 indici e importa i dati di esempio NYCJob.

1. Nel codice di esempio dell'esercitazione, apri il file di soluzione AutocompleteTutorial.sln in Visual Studio.  Apri Web. config all'interno del progetto AutocompleteTutorial e modifica i valori SearchServiceName e SearchServiceApiKey come quelli mostrati sopra.

### <a name="running-the-sample"></a>Esecuzione dell'esempio

A questo punto è possibile far partire l'applicazione di esempio dell'esercitazione.  Apri il file di soluzione AutocompleteTutorial.sln in Visual Studio per far partire l'esercitazione.  La soluzione contiene un progetto ASP.NET MVC.  Premi F5 per far partire il progetto e caricare la pagina nel browser che preferisci.  Nella parte superiore, vedrai un'opzione per la selezione di C# o JavaScript.  L'opzione C# chiama la classe HomeController dal browser e usa .Net SDK di Ricerca di Azure per ottenere risultati.  L'opzione JavaScript chiama l'API REST di Ricerca di Azure direttamente dal browser.  Questa opzione ha in genere prestazioni nettamente migliori poiché esclude i controller dal flusso.  È possibile scegliere l'opzione adatta alle proprie esigenze e preferenze linguistiche.  Sono disponibili nella pagina alcuni esempi di completamento automatico con alcune indicazioni specifiche.  Ogni esempio presenta un testo di esempio consigliato che è possibile provare.  Prova a digitare alcune lettere in ciascuna casella di ricerca per scoprire cosa succede.

## <a name="how-this-works-in-code"></a>Funzionamento in codice

Adesso che hai visualizzato gli esempi nel browser, esaminiamone in dettaglio il primo per riesaminare i componenti coinvolti e il loro funzionamento.

### <a name="search-box"></a>Casella di ricerca

Per ciascuna opzione linguistica, la casella di ricerca rimane esattamente la stessa.  Apri il file Index.cshtml nella cartella \Visualizzazioni\Home. La casella di ricerca in sé è semplice:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Si tratta di una casella di testo di input semplice con una classe per definire lo stile, un id che dovrà essere utilizzato da JavaScript e un testo segnaposto.  Il trucco è JavaScript.

### <a name="javascript-code-c"></a>Codice JavaScript (C#)

L'esempio di linguaggio C# usa JavaScript in Index.cshtml per sfruttare la libreria di completamento automatico dell'interfaccia utente di jQuery.  Questa libreria aggiunge l'esperienza di completamento automatico alla casella di ricerca effettuando chiamate asincrone al controller MVC per recuperare consigli.  Esaminiamo il codice JavaScript nel primo esempio:

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

Questo codice viene eseguito dal browser al caricamento della pagina per configurare il completamento automatico della casella di input "example1a".  `minLength: 3` assicura che i consigli verranno visualizzati solo quando sono presenti almeno tre caratteri nella casella di ricerca.  Il valore di origine è importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Questa riga indica all'API di completamento automatico dove poter reperire l'elenco di elementi da visualizzare nella casella di ricerca.  Poiché si tratta di un progetto MVC, la funzione Suggest viene chiamata in HomeController.cs.  Esamineremo questo punto nella prossima sezione.  Vengono inoltre trasferiti alcuni parametri per controllare elementi in evidenza, corrispondenze fuzzy e termini.  L'API JavaScript di completamento automatico aggiunge il parametro di termine.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Ingrandire l'esempio per supportare la corrispondenza fuzzy

La ricerca fuzzy consente di ottenere risultati in base alle corrispondenze simili, anche se l'utente commette errori di digitazione di una parola nella casella di ricerca.  Proviamo a farlo modificando la riga di origine per abilitare la corrispondenza fuzzy.

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

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Dopo aver esaminato il codice JavaScript come esempio, esaminiamo quello del controller C# che riesce a recuperare i consigli usando .NET SDK di Ricerca di Azure.

1. Apri il file HomeController.cs nella directory dei controller. 

1. La prima cosa che puoi notare è un metodo nella parte superiore della classe chiamata InitSearch.  Questo genererà un client di indice HTTP autenticato nel servizio Ricerca di Azure.  Se vuoi altre informazioni sul funzionamento, guarda l'esercitazione seguente: [Come usare Ricerca di Azure da un'applicazione .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Passa alla funzione Suggest.

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

La funzione Suggest include due parametri che determinano se vengono restituiti i risultati evidenziati o se la corrispondenza fuzzy è utilizzata in completamento all'inserimento del termine di ricerca.  Il metodo crea un oggetto SuggestParameters che viene quindi passato all'API Suggest. Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.
(Facoltativo) Aggiungi un punto di interruzione all'inizio della funzione Suggest ed esegui il codice passo a passo.  Nota la risposta restituita da SDK e come viene convertita nel risultato restituito dal metodo.

Gli altri esempi nella pagina seguono lo stesso modello per aggiungere risultati evidenziati, completamento automatico per i consigli di completamento automatico e facet per supportare la memorizzazione nella cache lato client dei risultati di completamento automatico.  Esamina ciascuno di questi elementi per comprendere come funzionano e come usarli al meglio nella tua esperienza di ricerca.

### <a name="javascript-language-example"></a>Esempio di linguaggio JavaScript

Per l'esempio del linguaggio JavaScript, il codice JavaScript nella pagina IndexJavaScript.cshtml sfrutta la funzionalità Completamento automatico dell'interfaccia utente di jQuery.  Si tratta di una libreria che esegue la maggior parte del carico di lavoro presentando la casella di ricerca dall'aspetto professionale interessante e rende più semplice a Ricerca di Azure l'esecuzione di chiamate asincrone per recuperare consigli.  Esaminiamo il codice JavaScript nel primo esempio:

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

Se lo si confronta con l'esempio precedente che chiama il controller Home, noterai diverse analogie.  La configurazione per il completamento automatico di `minLength` e `position` è esattamente uguale.  La modifica sostanziale risiede nell'origine.  Invece di chiamare il metodo Suggest nel controller home, una richiesta REST viene creata in una funzione JavaScript ed eseguita tramite ajax.  La risposta viene quindi elaborata in "success" e utilizzare come origine.

## <a name="takeaways"></a>Risultati

Questa esercitazione illustra i passaggi di base per la creazione di una casella di ricerca che supporta il completamento automatico e i consigli.  È stato illustrato come è possibile creare un'applicazione ASP.NET MVC e usare .Net SDK o l'API REST di Ricerca di Azure per recuperare i consigli.

## <a name="next-steps"></a>Passaggi successivi

Integra i consigli e il completamento automatico nella tua esperienza di ricerca.  Prendi in considerazione come l’uso di .Net SDK o l'API REST consenta automaticamente di trasferire la potenza di Ricerca di Azure agli utenti mentre digitano in modo da renderli più produttivi.

> [!div class="nextstepaction"]
> [L'API REST Completamento automatico](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete)
> [API REST per i consigli](https://docs.microsoft.com/en-us/rest/api/searchservice/suggestions)
> [Attributo dell'indice dei facet in un'API REST di creazione dell'indice](https://docs.microsoft.com/en-us/rest/api/searchservice/create-index)

