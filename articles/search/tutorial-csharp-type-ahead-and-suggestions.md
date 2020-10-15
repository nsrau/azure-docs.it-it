---
title: Esercitazione per C# su completamento automatico e suggerimenti
titleSuffix: Azure Cognitive Search
description: Completamento automatico e suggerimenti per raccogliere l'input dei termini di ricerca dagli utenti usando l'elenco a discesa. Questa esercitazione si basa su un progetto di hotel esistente.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 202a7f6b01423045fe7c72db5b42c29ae58f648d
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739664"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Esercitazione: Aggiungere il completamento automatico e i suggerimenti con .NET SDK

Informazioni su come implementare il completamento automatico (query di completamento automatico e risultati suggeriti) quando un utente inizia a digitare in una casella di ricerca. In questa esercitazione le query di completamento automatico e i risultati suggeriti vengono visualizzati prima separatamente e quindi insieme. È possibile che l'utente debba digitare solo due o tre caratteri per individuare tutti i risultati disponibili.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Aggiungere suggerimenti
> * Aggiungere l'evidenziazione per i suggerimenti
> * Aggiungere il completamento automatico
> * Combinare il completamento automatico e i suggerimenti

## <a name="overview"></a>Panoramica

Questa esercitazione aggiunge il completamento automatico e i risultati suggeriti alla precedente esercitazione [Aggiungere la paginazione ai risultati della ricerca](tutorial-csharp-paging.md).

Una versione completa del codice di questa esercitazione si trova nel progetto seguente:

* [3-add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Prerequisiti

* Soluzione [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Questo progetto può essere una versione personalizzata, completata nell'esercitazione precedente, oppure una copia di GitHub.

L'esercitazione è stata aggiornata per usare il pacchetto [Azure.Search.Documents (versione 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Per una versione precedente di .NET SDK, vedere l'[esempio di codice Microsoft.Azure.Search (versione 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="add-suggestions"></a>Aggiungere suggerimenti

Per iniziare, verrà esaminato il caso più semplice di offerta di alternative all'utente, ovvero un elenco a discesa di risultati suggeriti.

1. Nel file index.cshtml modificare il parametro `@id` dell'istruzione **TextBoxFor** in **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Dopo questa istruzione inserire lo script seguente dopo l'elemento finale **&lt;/div&gt;** . Questo script usa il [widget di completamento automatico](https://api.jqueryui.com/autocomplete/) della libreria open source dell'interfaccia utente di jQuery per presentare l'elenco a discesa dei risultati suggeriti.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    L'ID `"azureautosuggest"` connette lo script precedente alla casella di ricerca. L'opzione source del widget è impostata su un metodo Suggest che chiama l'API Suggest con due parametri di query, ovvero **highlights** e **fuzzy**, entrambi impostati su false in questa istanza. Per attivare la ricerca, è inoltre necessario un minimo di due caratteri.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Aggiungere alla visualizzazione i riferimenti agli script jQuery

1. Per accedere alla libreria jQuery, modificare la sezione &lt;head&gt; del file della visualizzazione sostituendola con il codice seguente:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Dal momento che si sta introducendo un nuovo riferimento a jQuery, è anche necessario rimuovere o impostare come commento il riferimento a jQuery predefinito nel file _Layout.cshtml (nella cartella **Views/Shared**). Individuare le righe seguenti e impostare come commento la prima riga dello script, come illustrato di seguito. Questa modifica consente di evitare riferimenti in conflitto a jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    A questo punto, è possibile usare le funzioni jQuery di completamento automatico predefinite.

### <a name="add-the-suggest-action-to-the-controller"></a>Aggiungere l'azione Suggest al controller

1. Nel controller home aggiungere l'azione **SuggestAsync** dopo l'azione **PageAsync**.

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
        var options = new SuggestOptions()
        {
            UseFuzzyMatching = fuzzy,
            Size = 8,
        };

        if (highlights)
        {
            options.HighlightPreTag = "<b>";
            options.HighlightPostTag = "</b>";
        }

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    Il parametro **Size** specifica il numero di risultati da restituire. Se non specificato, il valore predefinito è 5. Quando viene creato l'indice di ricerca, viene specificato uno _strumento suggerimenti_. Nell'indice di hotel di esempio ospitato da Microsoft lo strumento suggerimenti è denominato "sg" e cerca le corrispondenze suggerite esclusivamente nel campo **HotelName**.

    Con la corrispondenza fuzzy l'output include anche suggerimenti non completamente corretti, fino a una distanza di edit. Se il parametro **highlights** è impostato su true, all'output vengono aggiunti i tag HTML del grassetto. Questi parametri verranno impostati entrambi su true nella sezione successiva.

2. È possibile che vengano restituiti alcuni errori di sintassi. In tal caso, aggiungere le due istruzioni **using** seguenti all'inizio del file.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Eseguire l'app. Se immettendo "po", ad esempio, sono state visualizzate diverse opzioni, provare ora con "pa".

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="La digitazione di *po* visualizza due suggerimenti" border="false":::

    Tenere presente che le lettere immesse _devono_ essere quelle iniziali di una parola e non essere semplicemente incluse nella parola.

4. Nello script di visualizzazione impostare **&fuzzy** su true ed eseguire di nuovo l'app. A questo punto, immettere "po". Si noti che la ricerca presuppone che una lettera sia errata.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="La digitazione di *po* visualizza due suggerimenti" border="false":::

    Se si è interessati, nell'articolo [Sintassi di query Lucene in Ricerca cognitiva di Azure](./query-lucene-syntax.md) è descritta in dettaglio la logica usata nelle ricerche fuzzy.

## <a name="add-highlighting-to-the-suggestions"></a>Aggiungere l'evidenziazione per i suggerimenti

Per migliorare l'aspetto dei suggerimenti per l'utente, è possibile impostare il parametro **highlights** su true. È però prima necessario aggiungere alla visualizzazione il codice per visualizzare il testo in grassetto.

1. Nella visualizzazione (index.cshtml) aggiungere lo script seguente dopo lo script `"azureautosuggest"` descritto in precedenza.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Modificare ora l'ID della casella di testo in modo che sia simile alla seguente.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Eseguire di nuovo l'app. Il testo immesso verrà ora visualizzato in grassetto nei suggerimenti. Provare a digitare "pa".
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="La digitazione di *po* visualizza due suggerimenti" border="false":::

   La logica usata nello script di evidenziazione precedente non è infallibile. Se si immette un termine che è presente due volte nello stesso nome, i risultati in grassetto non corrispondono a quelli previsti. Provare a digitare "mo".

   Una delle questioni che uno sviluppatore deve valutare è decidere quando uno script funziona in modo relativamente corretto e quando è necessario risolvere i comportamenti anomali. L'evidenziazione non verrà ulteriormente usata in questa esercitazione, ma è opportuno individuare un algoritmo preciso se l'evidenziazione non è efficace per i dati in uso. Per altre informazioni, vedere [Evidenziazione dei risultati](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Aggiungere il completamento automatico

Un'altra variante, leggermente diversa dai suggerimenti, è il completamento automatico, che consente di completare un termine di query. Anche in questo caso si inizierà con l'implementazione più semplice prima di migliorare l'esperienza utente.

1. Immettere lo script seguente nella visualizzazione, dopo gli script precedenti.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Modificare ora l'ID della casella di testo in modo che sia simile alla seguente.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Nel controller home aggiungere l'azione **AutocompleteAsync** dopo l'azione **SuggestAsync**.

    ```cs
    public async Task<ActionResult> AutoCompleteAsync(string term)
    {
        InitSearch();

        // Setup the autocomplete parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 6
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

        // Convert the autocompleteResult results to a list that can be displayed in the client.
        List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

        return new JsonResult(autocomplete);
    }
    ```

    Si noti che per la ricerca con completamento automatico viene usata la stessa funzione *strumento suggerimenti*, denominato "sg", già usato per i suggerimenti, quindi si intende solo provare il completamento automatico dei nomi degli alberghi.

    Sono disponibili numerose impostazioni di **AutocompleteMode**. In questo caso verrà usata quella **OneTermWithContext**. Per una descrizione delle opzioni aggiuntive, vedere [API di completamento automatico](/rest/api/searchservice/autocomplete).

1. Eseguire l'app. Notare che le opzioni visualizzate nell'elenco a discesa sono singole parole. Provare a digitare parole che iniziano con "re". Notare che il numero di opzioni si riduce via via che si digitano le lettere.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="La digitazione di *po* visualizza due suggerimenti" border="false":::

    Allo stato attuale, lo script dei suggerimenti eseguito in precedenza è probabilmente più utile di questo script di completamento automatico. Per rendere il completamento automatico più intuitivo, è consigliabile usarlo con i risultati suggeriti.

## <a name="combine-autocompletion-and-suggestions"></a>Combinare il completamento automatico e i suggerimenti

La combinazione di completamento automatico e suggerimenti è la più complessa delle opzioni presentate e, probabilmente, quella che offre la migliore esperienza utente. L'intento è visualizzare, in linea con il testo digitato, la prima scelta di Ricerca cognitiva di Azure per il completamento automatico del testo. Si intende inoltre visualizzare un elenco a discesa che includa vari suggerimenti.

Sono disponibili librerie che offrono questa funzionalità, nota spesso come "completamento automatico inline". In questo caso, però, questa funzionalità verrà implementata in modo nativo per offrire la possibilità di esplorare le API. In questo esempio si inizierà a lavorare prima sul controller.

1. Aggiungere al controller un'azione che restituisca un solo risultato di completamento automatico, unitamente al numero specificato di suggerimenti. Questa azione è denominata **AutoCompleteAndSuggestAsync**. Nel controller home aggiungere l'azione seguente, dopo le altre azioni nuove.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    All'inizio dell'elenco **results** viene restituita una sola opzione di completamento automatico, seguita da tutti i suggerimenti.

1. Nella visualizzazione viene innanzitutto implementato un espediente che consente di visualizzare una parola di completamento automatico in grigio chiaro proprio sotto il testo in grassetto immesso dall'utente. A questo scopo, il linguaggio HTML include il posizionamento relativo. Modificare l'istruzione **TextBoxFor** (e le istruzioni &lt;div&gt; circostanti) sostituendola con quella indicata di seguito, tenendo presente che sotto la casella di ricerca normale è presente una seconda casella di ricerca, identificata come **underneath**, che è spostata di 39 pixel rispetto alla posizione predefinita di quella normale.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Si noti che l'ID viene cambiato ancora una volta, in questo caso in **azureautocomplete**.

1. Sempre nella visualizzazione inserire lo script seguente, dopo tutti gli script inseriti finora. Lo script è troppo lungo e complesso a causa della varietà di comportamenti di input che gestisce.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Si noti l'uso intelligente della funzione **interval** sia per cancellare il testo sottostante quando non corrisponde più a quanto digitato dall'utente, sia per impostare lo stesso uso di maiuscole/minuscole durante la digitazione (dal momento che "pa" corrisponde a "PA", "pA", "Pa" durante la ricerca), in modo che il testo sovrapposto risulti accurato.

    Per saperne di più, leggere i commenti presenti nello script.

1. È infine necessario apportare una modifica secondaria alle due classi HTML per renderle trasparenti. Aggiungere la riga seguente alle classi **searchBoxForm** e **searchBox** nel file hotels.css.

    ```html
    background: rgba(0,0,0,0);
    ```

1. A questo punto, eseguire l'app. Immettere "pa" nella casella di ricerca. Si ottiene "palace" come suggerimento del completamento automatico, unitamente ai due nomi di albergo che includono le lettere "pa"?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="La digitazione di *po* visualizza due suggerimenti" border="false":::

1. Provare a premere TAB per accettare il suggerimento del completamento automatico, quindi provare a selezionare i suggerimenti con i tasti di direzione e TAB, infine provare di nuovo usando il mouse e un singolo clic. Verificare che lo script gestisca in modo accurato tutte queste situazioni.

    Si può decidere che è più semplice caricare una libreria che offre già questa funzionalità, ma ora si conosce almeno un modo per far funzionare il completamento automatico inline.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* Grazie al completamento automatico e ai suggerimenti l'utente può digitare solo alcune lettere per individuare esattamente quello che cerca.
* La combinazione di completamento automatico e suggerimenti consente di offrire un'esperienza utente migliorata.
* Testare sempre le funzioni di completamento automatico con tutti i tipi di input.
* La funzione **setInterval** può essere utile per verificare e correggere elementi dell'interfaccia utente.

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione verrà esaminato un altro modo per migliorare l'esperienza utente, usando i facet per limitare le ricerche con un singolo clic.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Usare facet per semplificare l'esplorazione - Ricerca cognitiva di Azure](tutorial-csharp-facets.md)
