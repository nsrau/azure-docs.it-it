---
title: Esercitazione per C# su completamento automatico e suggerimenti
titleSuffix: Azure Cognitive Search
description: Questa esercitazione si basa sul progetto "Paginazione dei risultati della ricerca - Ricerca cognitiva di Azure" per aggiungere il completamento automatico e i suggerimenti. L'obiettivo è di migliorare l'esperienza utente. Vengono fornite informazioni su come combinare un elenco a discesa di suggerimenti con il completamento automatico inline.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 959ae749f9ab8a025ec9c78d75640e2108868372
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786496"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>Esercitazione per C#: Aggiungere completamento automatico e suggerimenti - Ricerca cognitiva di Azure

In questa esercitazione viene spiegato come implementare il completamento automatico e i suggerimenti quando un utente inizia a digitare nella casella di ricerca. Nell'esercitazione i risultati del completamento automatico e dei suggerimenti verranno mostrati separatamente. Verrà quindi illustrato un metodo per combinarli e ottenere un'esperienza utente migliore. È possibile che l'utente debba digitare solo due o tre lettere per individuare tutti i risultati disponibili. Questa esercitazione si basa sul progetto di paginazione creato in [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca cognitiva di Azure](tutorial-csharp-paging.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Aggiungere suggerimenti
> * Aggiungere l'evidenziazione per i suggerimenti
> * Aggiungere il completamento automatico
> * Combinare il completamento automatico e i suggerimenti

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

Predisporre il progetto [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca cognitiva di Azure](tutorial-csharp-paging.md) attiva e in esecuzione. Questo progetto può essere una versione personalizzata, completata nell'esercitazione precedente, oppure può essere installato da GitHub: [Creare la prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Aggiungere suggerimenti

Per iniziare, esamineremo il caso più semplice per offrire alternative all'utente, ovvero un elenco a discesa di suggerimenti.

1. Nel file index.cshtml modificare l'istruzione **TextBoxFor** nella seguente.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    Notare che l'ID della casella di ricerca è stato impostato su **azureautosuggest**.

2. Dopo questa istruzione inserire lo script seguente dopo l'elemento finale **&lt;/div&gt;** .

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Lo script è stato connesso alla casella di ricerca usando lo stesso ID. Per attivare la ricerca, sono inoltre necessari almeno due caratteri. L'azione **Suggest** viene chiamata nel controller home con due parametri di query, ovvero **highlights** e **fuzzy**, entrambi impostati su false in questa istanza.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Aggiungere alla visualizzazione i riferimenti a script jquery

La funzione di completamento automatico chiamata nello script precedente non deve essere creata manualmente perché è già disponibile nella libreria jquery. 

1. Per accedere alla libreria jquery, modificare la sezione &lt;head&gt; del file della visualizzazione sostituendola con il codice seguente.

    ```cs
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

2. È inoltre necessario rimuovere o impostare come commento una riga che fa riferimento a jquery nel file _Layout.cshtml (nella cartella **Views/Shared**). Individuare le righe seguenti e impostare come commento la prima riga dello script, come illustrato di seguito. Questa modifica consente di evitare riferimenti in conflitto a jquery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    A questo punto, è possibile usare le funzioni jquery di completamento automatico predefinite.

### <a name="add-the-suggest-action-to-the-controller"></a>Aggiungere l'azione Suggest al controller

1. Nel controller home aggiungere l'azione **Suggest** (ad esempio, dopo l'azione **Page**).

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Il parametro **Top** specifica il numero di risultati da restituire. Se non è specificato, l'impostazione predefinita è 5. Viene inoltre specificato uno _strumento suggerimenti_ per l'indice di Azure. Questa operazione viene eseguita dopo la configurazione dei dati e non da un'app client, come nel caso di questa esercitazione. In questo caso, lo strumento suggerimenti si chiama "sg" e consente solo di eseguire ricerche nel campo **HotelName**. 

    Con la corrispondenza fuzzy l'output include anche suggerimenti non completamente corretti. Se il parametro **highlights** è impostato su true, all'output vengono aggiunti i tag HTML del grassetto. Questi parametri verranno impostati su true nella sezione successiva.

2. È possibile che vengano restituiti alcuni errori di sintassi. In tal caso, aggiungere le due istruzioni **using** seguenti all'inizio del file.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Eseguire l'app. Se immettendo "po", ad esempio, sono state visualizzate diverse opzioni, provare ora con "pa".

    ![Con "po" vengono visualizzati due suggerimenti](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Tenere presente che le lettere immesse _devono_ essere quelle iniziali di una parola e non essere semplicemente incluse nella parola.

4. Nello script di visualizzazione impostare **&fuzzy** su true ed eseguire di nuovo l'app. A questo punto, immettere "po". Notare che la ricerca presuppone che una lettera sia errata.
 
    !["po" con fuzzy impostato su true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Se si è interessati, nell'articolo [Sintassi di query Lucene in Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/query-lucene-syntax) è descritta in dettaglio la logica usata nelle ricerche fuzzy.

## <a name="add-highlighting-to-the-suggestions"></a>Aggiungere l'evidenziazione per i suggerimenti

Per migliorare leggermente l'aspetto dei suggerimenti per l'utente, è possibile impostare il parametro **highlights** su true. È però prima necessario aggiungere alla visualizzazione il codice per visualizzare il testo in grassetto.

1. Nella visualizzazione (index.cshtml) aggiungere lo script seguente dopo lo script **azureautosuggest** inserito in precedenza.

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

2. Modificare ora l'ID della casella di testo in modo che sia simile alla seguente.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Eseguire di nuovo l'app. Il testo immesso verrà ora visualizzato in grassetto nei suggerimenti. Provare, ad esempio, a digitare "pa".
 
    !["pa" con evidenziazione](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. La logica usata nello script di evidenziazione precedente non è infallibile. Se si immette un termine che è presente due volte nello stesso nome, i risultati in grassetto non corrispondono a quelli previsti. Provare a digitare "mo".

    Una delle questioni che uno sviluppatore deve valutare è decidere quando uno script funziona in modo relativamente corretto e quando è necessario risolvere i comportamenti anomali. L'evidenziazione non verrà ulteriormente usata in questa esercitazione, ma è opportuno individuare un algoritmo preciso se si intende usarla ancora.

## <a name="add-autocompletion"></a>Aggiungere il completamento automatico

Un'altra variante, leggermente diversa dai suggerimenti, è il cosiddetto completamento automatico. Anche in questo caso si inizierà con l'implementazione più semplice prima di passare al miglioramento dell'esperienza utente.

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

2. Modificare ora l'ID della casella di testo in modo che sia simile alla seguente.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Nel controller home è necessario aggiungere l'azione **Autocomplete**, ad esempio sotto l'azione **Suggest**.

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Si noti che per la ricerca con completamento automatico viene usata la stessa funzione *strumento suggerimenti*, denominato "sg", già usato per i suggerimenti, quindi si intende solo provare il completamento automatico dei nomi degli alberghi.

    Sono disponibili numerose impostazioni di **AutocompleteMode**. In questo caso verrà usata quella **OneTermWithContext**. Per una descrizione delle opzioni disponibili, vedere l'articolo su [Completamento automatico di Azure](https://docs.microsoft.com/rest/api/searchservice/autocomplete).

4. Eseguire l'app. Notare che le opzioni visualizzate nell'elenco a discesa sono singole parole. Provare a digitare parole che iniziano con "re". Notare che il numero di opzioni si riduce via via che si digitano le lettere.

    ![Digitazione con completamento automatico di base](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Allo stato attuale, lo script dei suggerimenti eseguito in precedenza è probabilmente più utile di questo script di completamento automatico. Per semplificare l'uso del completamento automatico, è preferibile aggiungerlo alla ricerca con suggerimenti.

## <a name="combine-autocompletion-and-suggestions"></a>Combinare il completamento automatico e i suggerimenti

La combinazione di completamento automatico e suggerimenti è la più complessa delle opzioni presentate e, probabilmente, quella che offre la migliore esperienza utente. L'intento è visualizzare, in linea con il testo digitato, la prima scelta di Ricerca cognitiva di Azure per il completamento automatico del testo. Si intende inoltre visualizzare un elenco a discesa che includa vari suggerimenti.

Sono disponibili librerie che offrono questa funzionalità, nota spesso come "completamento automatico inline". In questo caso, però, questa funzionalità verrà implementata in modo nativo per offrire maggiori informazioni su tutti i passaggi. In questo esempio si inizierà a lavorare prima sul controller.

1. È necessario aggiungere al controller un'azione che restituisca un solo risultato di completamento automatico, unitamente al numero specificato di suggerimenti. Questa azione verrà denominata **AutocompleteAndSuggest**. Nel controller home aggiungere l'azione seguente, dopo le altre azioni nuove.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    All'inizio dell'elenco **results** viene restituita una sola opzione di completamento automatico, seguita da tutti i suggerimenti.

2. Nella visualizzazione viene innanzitutto implementato un espediente che consente di visualizzare una parola di completamento automatico in grigio chiaro proprio sotto il testo in grassetto immesso dall'utente. A questo scopo, il linguaggio HTML include il posizionamento relativo. Modificare l'istruzione **TextBoxFor** (e le istruzioni &lt;div&gt; circostanti) sostituendola con quella indicata di seguito, tenendo presente che sotto la casella di ricerca normale è presente una seconda casella di ricerca, identificata come **underneath**, che è spostata di 39 pixel rispetto alla posizione predefinita di quella normale.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    L'ID viene modificato ancora una volta, in questo caso in **azureautocomplete**.

3. Sempre nella visualizzazione inserire lo script seguente, dopo tutti gli script inseriti finora. Il numero degli script è particolarmente elevato.

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
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
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

    Notare l'uso intelligente della funzione **interval** per cancellare il testo sottostante quando non corrisponde più a quanto digitato dall'utente, nonché per impostare lo stesso uso di maiuscole/minuscole durante la digitazione (dal momento che "pa" corrisponde a "PA", "pA", "Pa" durante la ricerca), in modo che il testo sovrapposto risulti accurato.

    Per saperne di più, leggere i commenti presenti nello script.

4. È infine necessario apportare una modifica secondaria alle due classi HTML per renderle trasparenti. Aggiungere la riga seguente alle classi **searchBoxForm** e **searchBox** nel file hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. A questo punto, eseguire l'app. Immettere "pa" nella casella di ricerca. Si ottiene "palace" come suggerimento del completamento automatico, unitamente ai due nomi di albergo che includono le lettere "pa"?

    ![Digitazione con suggerimenti e completamento automatico inline](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Provare a premere TAB per accettare il suggerimento del completamento automatico, quindi provare a selezionare i suggerimenti con i tasti di direzione e TAB, infine provare di nuovo usando il mouse e un singolo clic. Verificare che lo script gestisca in modo accurato tutte queste situazioni.

    È possibile decidere che è più semplice caricare una libreria che offre già questa funzionalità, ma almeno ora si conosce almeno un modo per far funzionare il completamento automatico inline.

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


