---
title: Aggiungere il completamento automatico e suggerimenti in una casella di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query di ricerca durante la digitazione in Ricerca cognitiva di Azure creando suggerimenti e formulando le richieste che completano automaticamente una casella di ricerca con termini o frasi completati. Puoi anche restituire le corrispondenze suggerite.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758117"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Aggiungere il completamento automatico e suggerimenti alle app client

La ricerca come si digita è una tecnica comune per migliorare la produttività delle query avviate dall'utente. In Ricerca cognitiva di Azure questa esperienza è supportata tramite *il completamento automatico,* che termina un termine o una frase in base all'input parziale (completamento di "micro" con "microsoft"). Un altro modulo è *suggerimenti*: un breve elenco di documenti corrispondenti (restituzione di titoli di libri con un ID in modo che è possibile collegare a una pagina di dettaglio). Sia il completamento automatico che i suggerimenti sono basati su una corrispondenza nell'indice. Il servizio non offrirà query che restituiscono zero risultati.

Per implementare queste esperienze in Ricerca cognitiva di Azure, è necessario:To implement these experiences in Azure Cognitive Search, you will need:

+ Un *suggeritore* sul back-end.
+ Query *query* che specifica il [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) o [l'API di suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) nella richiesta.
+ Un *controllo dell'interfaccia utente* per gestire le interazioni di ricerca come di te nell'app client. Ti consigliamo di utilizzare una libreria JavaScript esistente a questo scopo.

In Ricerca cognitiva di Azure le query completate automaticamente e i risultati suggeriti vengono recuperati dall'indice di ricerca dai campi selezionati registrati con un suggerimento. Un suggerimento fa parte dell'indice e specifica quali campi forniranno il contenuto che completa una query, suggerisce un risultato o esegue entrambe le operazioni. Quando l'indice viene creato e caricato, una struttura di dati del suggerimento viene creata internamente per archiviare i prefissi utilizzati per la corrispondenza nelle query parziali. Per i suggerimenti, la scelta di campi adatti che sono unici, o almeno non ripetitivi, è essenziale per l'esperienza. Per ulteriori informazioni, consultate [Creare un suggerimento.](index-add-suggesters.md)

Il resto di questo articolo è incentrato sulle query e sul codice client. Per illustrare i punti chiave, viene utilizzato JavaScript e C. Gli esempi di API REST vengono usati per presentare in modo conciso ogni operazione. Per collegamenti a esempi di codice end-to-end, vedere [Passaggi successivi](#next-steps).

## <a name="set-up-a-request"></a>Impostare una richiesta

Gli elementi di una richiesta includono una delle API di ricerca durante la digitazione, una query parziale e un suggerimento. Lo script seguente illustra i componenti di una richiesta, usando l'API REST di completamento automatico come esempio.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

Il **suggesterName** fornisce i campi in grado di riconoscere i suggerimenti utilizzati per completare termini o suggerimenti. Per i suggerimenti, in particolare, l'elenco dei campi dovrebbe essere composto da quelli che offrono scelte chiare tra risultati corrispondenti. Su un sito che vende giochi per computer, il campo potrebbe essere il titolo del gioco.

Il parametro **search** fornisce la query parziale, in cui i caratteri vengono alimentati alla richiesta di query tramite il controllo di completamento automatico jQuery. Nell'esempio precedente, "minecraf" è un'illustrazione statica di ciò che il controllo potrebbe aver passato.

Le API non impongono requisiti di lunghezza minima alla query parziale; può essere appena un personaggio. Tuttavia, jQuery completamento automatico fornisce una lunghezza minima. Un minimo di due o tre caratteri è tipico.

Le corrispondenze si trovano all'inizio di un termine in qualsiasi punto della stringa di input. Data "la volpe marrone veloce", sia il completamento automatico che i suggerimenti corrisponderanno alle versioni parziali di "the", "quick", "brown" o "fox" ma non su termini parziali come "rown" o "ox". Inoltre, ogni partita imposta l'ambito per le espansioni a valle. Una query parziale di "quick br" corrisponderà su "marrone veloce" o "pane veloce", ma né "marrone" o "pane" da soli sarebbe abbinato a meno che "quick" non li preceda.

### <a name="apis-for-search-as-you-type"></a>API per la ricerca durante la digitazione

Seguire questi collegamenti per le pagine di riferimento di REST e .NET SDK:Follow these links for the REST and .NET SDK reference pages:

+ [API REST dei suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST di completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync (metodo)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metodo AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Strutturare una risposta

Le risposte per il completamento automatico e i suggerimenti sono quelli che ci si potrebbe aspettare per il modello: [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) restituisce un elenco di termini, [Suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) restituisce termini più un ID documento in modo che è possibile recuperare il documento (utilizzare l'API [documento di ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) per recuperare il documento specifico per una pagina di dettaglio).

Le risposte sono modellate dai parametri della richiesta. Per Completamento automatico, impostare [**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) per determinare se il completamento del testo si verifica su uno o due termini. Per Suggerimenti, il campo scelto determina il contenuto della risposta.

Per suggerimenti, è necessario perfezionare ulteriormente la risposta per evitare duplicati o quelli che sembrano essere risultati non correlati. Per controllare i risultati, includere più parametri nella richiesta. I parametri seguenti si applicano sia al completamento automatico che ai suggerimenti, ma sono forse più necessari per i suggerimenti, soprattutto quando un suggerimento include più campi.

| Parametro | Uso |
|-----------|-------|
| **$select** | Se in un suggerimento sono presenti più **campi sourceField,** `$select=GameTitle`utilizzare **$select** per scegliere il campo che contribuisce ai valori ( ). |
| **searchFields** | Vincolare la query a campi specifici. |
| **$filter** | Applicare i criteri di`$filter=Category eq 'ActionAdventure'`corrispondenza al set di risultati ( ). |
| **$top** | Limitare i risultati a`$top=5`un numero specifico ( ).|

## <a name="add-user-interaction-code"></a>Aggiungere il codice di interazione utente

Il riempimento automatico di un termine di query o l'eliminazione di un elenco di collegamenti corrispondenti richiede codice di interazione dell'utente, in genere JavaScript, che può utilizzare richieste provenienti da origini esterne, ad esempio query di completamento automatico o di suggerimento su un indice di Ricerca automatica.

Anche se è possibile scrivere questo codice in modo nativo, è molto più semplice usare le funzioni dalla libreria JavaScript esistente. In questo articolo vengono illustrati due, uno per i suggerimenti e un altro per il completamento automatico. 

+ Il widget di [completamento automatico (jQuery UI)](https://jqueryui.com/autocomplete/) viene utilizzato nell'esempio di suggerimento. È possibile creare una casella di ricerca e quindi farvi riferimento in una funzione JavaScript che utilizza il widget Completamento automatico. Le proprietà del widget impostano l'origine (una funzione di completamento automatico o suggerimenti), la lunghezza minima dei caratteri di input prima dell'azione e il posizionamento.

+ [Il plug-in completamento automatico XDSoft](https://xdsoft.net/jqplugins/autocomplete/) viene utilizzato nell'esempio di completamento automatico.

Queste librerie vengono usate per compilare la casella di ricerca che supporta i suggerimenti e il completamento automatico. Gli input raccolti nella casella di ricerca sono associati a suggerimenti e azioni di completamento automatico.

## <a name="suggestions"></a>Suggerimenti

In questa sezione viene illustrata un'implementazione dei risultati suggeriti, a partire dalla definizione della casella di ricerca. Viene inoltre illustrato come e lo script che richiama la prima libreria di completamento automatico JavaScript a cui si fa riferimento in questo articolo.

### <a name="create-a-search-box"></a>Creare una casella di ricerca

Supponendo che la libreria di [completamento automatico dell'interfaccia utente jQuery](https://jqueryui.com/autocomplete/) e un progetto MVC in C, è possibile definire la casella di ricerca utilizzando JavaScript nel file **Index.cshtml.** La libreria aggiunge l'interazione di ricerca come si digita alla casella di ricerca effettuando chiamate asincrone al controller MVC per recuperare i suggerimenti.

In **Index.cshtml,** nella cartella "Views", una riga per creare una casella di ricerca potrebbe essere la seguente:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Questo esempio contiene una casella di testo di input semplice con una classe per definire lo stile, un ID a cui JavaScript farà riferimento e un testo segnaposto.  

All'interno dello stesso file, incorporare JavaScript che fa riferimento alla casella di ricerca. La funzione seguente chiama l'API Suggest, che richiede documenti corrispondenti suggeriti in base a input di termini parziali:The following function calls the Suggest API, which requests suggested matching documents based on partial term inputs:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Il `source` indica la funzione di completamento automatico dell'interfaccia utente jQuery dove ottenere l'elenco di elementi da visualizzare sotto la casella di ricerca. Poiché questo progetto è un progetto MVC, chiama la funzione **Suggest** in **HomeController.cs** che contiene la logica per la restituzione dei suggerimenti di query. Questa funzione passa anche alcuni parametri per controllare evidenziazioni, corrispondenze fuzzy e termini. L'API JavaScript di completamento automatico aggiunge il parametro per i termini.

Il `minLength: 3` assicura che i suggerimenti verranno visualizzati solo quando sono presenti almeno tre caratteri nella casella di ricerca.

### <a name="enable-fuzzy-matching"></a>Abilita corrispondenza fuzzy

La ricerca fuzzy consente di ottenere risultati in base alle corrispondenze simili, anche se l'utente commette errori di digitazione di una parola nella casella di ricerca. La distanza di modifica è 1, il che significa che può esserci una discrepanza massima di un carattere tra l'input dell'utente e una corrispondenza. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Abilitare l'evidenziazione

L'evidenziazione applica lo stile del carattere ai caratteri nel risultato che corrispondono all'input. Ad esempio, se l'input parziale è "micro", il risultato apparirà come **micro**soft, **micro**ambito e così via. L'evidenziazione si basa sui parametri HighlightPreTag e HighlightPostTag, definiti inline con la funzione Suggestion.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Suggerisci funzione

Se si utilizza C e un'applicazione MVC, **HomeController.cs** file nella directory Controllers è in cui è possibile creare una classe per i risultati suggeriti. In .NET una funzione Suggest è basata sul [metodo DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

Il `InitSearch` metodo crea un client di indicizzazione HTTP autenticato per il servizio Ricerca cognitiva di Azure.The method creates an authenticated HTTP index client to the Azure Cognitive Search service. Per altre informazioni su .NET SDK, vedere Come usare Ricerca cognitiva di [Azure da un'applicazione .NET.](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
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

## <a name="autocomplete"></a>Completamento automatico

Finora, il codice dell'esperienza utente di ricerca è stato centrato sui suggerimenti. Il blocco di codice successivo mostra il completamento automatico, usando la funzione di completamento automatico dell'interfaccia utente jQuery di XDSoft, passando una richiesta per il completamento automatico di Ricerca cognitiva di Azure.The next code block shows autocomplete, using the XDSoft jQuery UI Autocomplete function, passing in a request for Azure Cognitive Search autocomplete. Come per i suggerimenti, in un'applicazione in C, il codice che supporta l'interazione dell'utente viene letto in **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funzione di completamento automatico

Il completamento automatico si basa sul [metodo DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Come per i suggerimenti, questo blocco di codice andrebbe nel file **di HomeController.cs.**

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

La funzione Autocomplete accetta l'input del termine di ricerca. Il metodo crea un [oggetto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

## <a name="next-steps"></a>Passaggi successivi

Segui questi link per istruzioni end-to-end o codice che illustra entrambe le esperienze di ricerca durante la digitazione. Entrambi gli esempi di codice includono implementazioni ibride di suggerimenti e completamento automatico insieme.

+ [Esercitazione: Creare la prima app in C](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Esempio di codice in C: azure-search-dotnet-samples/create-first-app/3-add-typeahead/C'c' code sample: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [Esempio di codice side-by-side di C e JavaScript](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)