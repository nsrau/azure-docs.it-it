---
title: Esercitazione per C# sulla paginazione dei risultati della ricerca
titleSuffix: Azure Cognitive Search
description: Aggiungere i pulsanti di paginazione e spostamento ai risultati della ricerca, in base a un progetto hotels esistente, per aggiungere i pulsanti per passare alla prima pagina, a quella successiva, a quella precedente e all'ultima, oltre ai pulsanti numerati. Un secondo sistema di paginazione usa lo scorrimento infinito, attivato dallo spostamento di una barra di scorrimento verticale fino al limite inferiore.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: a08756a1e3153aa69bd0e79dc23e88d4bf211e5d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950687"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Esercitazione: Aggiungere la paginazione ai risultati della ricerca con .NET SDK

Informazioni su come implementare due diversi sistemi di paginazione, il basato sui numeri di pagina e il secondo sullo scorrimento infinito. Entrambi i sistemi di paginazione sono ampiamente diffusi. Per selezionare quello più adatto, è necessario considerare l'esperienza utente che si intende ottenere con i risultati. 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Estendere l'app con la paginazione numerata
> * Estendere l'app con lo scorrimento infinito

## <a name="overview"></a>Panoramica

Questa esercitazione sovrappone un sistema di paginazione a un progetto creato in precedenza descritto nell'esercitazione [Creare la prima app di ricerca](tutorial-csharp-create-first-app.md).

Le versioni complete del codice che verrà distribuito in questa esercitazione si trovano nei progetti seguenti:

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Prerequisiti

* Progetto [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page). Questo progetto può essere una versione personalizzata, completata nell'esercitazione precedente, oppure una copia di GitHub.

L'esercitazione è stata aggiornata per usare il pacchetto [Azure.Search.Documents (versione 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Per una versione precedente di .NET SDK, vedere l'[esempio di codice Microsoft.Azure.Search (versione 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="extend-your-app-with-numbered-paging"></a>Estendere l'app con la paginazione numerata

La paginazione numerata è il sistema di paginazione scelto dai principali motori di ricerca Web commerciali e da molti altri siti Web di ricerca. La paginazione numerata include in genere le opzioni "successiva" e "precedente", oltre a un intervallo di numeri di pagina effettivi. Possono essere disponibili anche le opzioni "prima pagina" e "ultima pagina". Si tratta di opzioni che certamente consentono all'utente di controllare l'esplorazione dei risultati basati su pagine.

Nell'esercitazione verrà aggiunto un sistema che include le opzioni per prima, precedente, successiva e ultima pagina, oltre a numeri di pagina che non iniziano da 1, ma si riferiscono a pagine circostanti a quella attiva. Ad esempio, se l'utente sta esaminando la pagina 10, vengono visualizzati i numeri di pagina 8, 9, 10, 11 e 12.

Il sistema sarà abbastanza flessibile per consentire l'impostazione del numero di numeri di pagina visibili in una variabile globale.

Il sistema considererà speciali i pulsanti dei numeri di pagina all'estrema sinistra e all'estrema destra, in quanto attivano la modifica dell'intervallo di numeri di pagina visualizzati. Se, ad esempio, vengono visualizzati i numeri di pagina 8, 9, 10, 11 e 12 e l'utente fa clic su 8, l'intervallo di numeri di pagina visualizzati cambia in 6, 7, 8, 9 e 10. Se si seleziona 12, verrà applicato uno spostamento analogo verso destra.

### <a name="add-paging-fields-to-the-model"></a>Aggiungere campi di paginazione al modello

Aprire la soluzione della pagina di ricerca di base.

1. Aprire il file di modello SearchData.cs.

1. Aggiungere variabili globali per supportare la paginazione. In MVC le variabili globali sono dichiarate nella relativa classe statica. **ResultsPerPage** consente di impostare il numero di risultati per pagina. **MaxPageRange** determina il numero di numeri di pagina visibili nella visualizzazione. **PageRangeDelta** determina il numero di pagine che dovranno essere spostate a sinistra o a destra quando viene selezionato il numero di pagina all'estrema sinistra o all'estrema destra. Quest'ultimo numero si trova in genere a metà di **MaxPageRange**. Aggiungere il codice seguente nello spazio dei nomi.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Se si esegue questo progetto in un dispositivo con uno schermo di dimensioni ridotte, ad esempio un computer portatile, provare a impostare **ResultsPerPage** su 2.

1. Aggiungere le proprietà di paginazione alla classe **SearchData**, dopo la proprietà **searchText**.

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Aggiungere una tabella di opzioni di paginazione alla visualizzazione

1. Aprire il file index.cshtml e aggiungere il codice seguente prima del tag &lt;/body&gt; finale. Questo nuovo codice consente di visualizzare presenta una tabella di opzioni di paginazione per le pagine seguenti: prima, precedente, 1, 2, 3, 4, 5, successiva, ultima.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
    }
    ```

    Per allineare le opzioni, si usa una tabella HTML. Tutte le azioni provengono dalle istruzioni @Html.ActionLink, ognuna delle quali chiama il controller con un modello **new** creato con voci diverse per la proprietà **paging** aggiunta in precedenza.

    Le opzioni relative alla prima e all'ultima pagina non inviano stringhe quali "first" e "last", ma i numeri di pagina corretti.

1. Aggiungere le classi di paginazione all'elenco di stili HTML nel file hotels.css. La classe **pageSelected** consente di identificare la pagina corrente (applicando un formato in grassetto al numero di pagina) nell'elenco dei numeri di pagina.

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Aggiungere un'azione Page al controller

1. Aprire il file HomeController.cs e aggiungere l'azione **PageAsync**. Questa azione risponde a una qualsiasi delle opzioni di pagina selezionate.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    Il metodo **RunQueryAsync** visualizzerà ora un errore di sintassi a causa del terzo parametro che verrà descritto tra poco.

    > [!Note]
    > Le chiamate a **TempData** archiviano un valore (un **oggetto**) nell'archiviazione temporanea, anche se questo tipo di archiviazione viene mantenuto _solo_ per una chiamata. Qualsiasi cosa archiviata nei dati temporanei rimarrà disponibile per la chiamata successiva a un'azione del controller, ma non lo sarà più per le chiamate seguenti. In considerazione della durata ridotta, le proprietà di paginazione e il testo da cercare vengono salvati nuovamente nell'archiviazione temporanea a ogni chiamata a **PageAsync**.

1. Aggiornare l'azione **Index(model)** per archiviare le variabili temporanee e per aggiungere il parametro della pagina all'estrema sinistra alla chiamata a **RunQueryAsync**.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. Il metodo **RunQueryAsync**, introdotto nella lezione precedente, deve essere modificato per risolvere l'errore di sintassi. Si usano i campi **Skip**, **Top** e **IncludeTotalCount** della classe [**SearchOptions**](/dotnet/api/azure.search.documents.searchoptions) per richiedere una sola pagina di risultati, a partire dall'impostazione **Skip**. È inoltre necessario calcolare le variabili di paginazione per la visualizzazione. Sostituire l'intero metodo con il codice seguente.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        return View("Index", model);
    }
    ```

1. Infine, apportare una piccola modifica alla visualizzazione. La variabile **resultList.Results.TotalCount** conterrà ora il numero di risultati restituiti in una sola pagina (3 nell'esempio) e non il numero totale. Dal momento che **IncludeTotalCount** è impostato su true, la variabile **resultsList.TotalCount** ora contiene il numero totale di risultati. Individuare il punto il cui viene visualizzato il numero di risultati nella visualizzazione e sostituirlo con il codice seguente.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > È possibile notare un leggero calo delle prestazioni se si imposta **IncludeTotalCount** su true, perché questo totale deve essere calcolato da Ricerca cognitiva di Azure. Con i set di dati complessi viene visualizzato un avviso che informa che il valore restituito è un'_approssimazione_. Poiché l'ambito della ricerca di hotel è ridotto, il valore sarà accurato.

### <a name="compile-and-run-the-app"></a>Compilare ed eseguire l'app

A questo punto, selezionare **Avvia senza eseguire debug** (oppure premere F5).

1. Eseguire una ricerca in una stringa che restituisce molti risultati, ad esempio "wifi". Verificare se la paginazione dei risultati è accurata.

    ![Paginazione numerata nei risultati di "pool"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. Provare a fare clic sui numeri di pagina all'estrema destra e all'estrema sinistra. Verificare se i numeri di pagina vengono spostati in modo corretto al centro della pagina corrente.

1. Verificare inoltre l'utilità delle opzioni per la prima e l'ultima pagina. Alcuni motori di ricerca commerciali usano queste opzioni, mentre altri no.

1. Passare all'ultima pagina di risultati. L'ultima pagina è l'unica pagina che può contenere un numero di risultati inferiore a quello di **ResultsPerPage**.

    ![Esame dell'ultima pagina relativa a "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. Digitare "town" e fare clic sul pulsante di ricerca. Se i risultati sono di dimensioni inferiori a una pagina, non vengono visualizzate opzioni di paginazione.

    ![Ricerca di "town"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Salvare il progetto e passare alla sezione successiva per una forma alternativa di paginazione.

## <a name="extend-your-app-with-infinite-scrolling"></a>Estendere l'app con lo scorrimento infinito

Lo scorrimento infinito viene attivato quando un utente scorre una barra di scorrimento verticale fino all'ultimo dei risultati visualizzati. In questo caso viene effettuata una chiamata al servizio di ricerca per ottenere la pagina successiva di risultati. Se non sono presenti altri risultati, non viene restituito nulla e la barra di scorrimento verticale non cambia. Se sono presenti altri risultati, questi vengono aggiunti alla pagina corrente e la barra di scorrimento cambia per indicare che sono disponibili altri risultati.

Un punto importante da notare è che la pagina corrente non viene sostituita, ma piuttosto estesa per mostrare i risultati aggiuntivi. Un utente può sempre scorrere fino ai primi risultati della ricerca.

Per implementare lo scorrimento infinito, si inizierà con il progetto prima dell'aggiunta di uno qualsiasi degli elementi di scorrimento dei numeri di pagina. In GitHub si tratta della soluzione [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page).

### <a name="add-paging-fields-to-the-model"></a>Aggiungere campi di paginazione al modello

1. Aggiungere innanzitutto una proprietà **paging** alla classe **SearchData** (nel file del modello SearchData.cs).

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Questa variabile è una stringa che contiene "next" se deve essere inviata la pagina di risultati successiva oppure deve essere Null per la prima pagina di una ricerca.

1. Nello stesso file e nello spazio dei nomi, aggiungere una classe di variabili globali con un'unica proprietà. In MVC le variabili globali sono dichiarate nella relativa classe statica. **ResultsPerPage** consente di impostare il numero di risultati per pagina. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Aggiungere una barra di scorrimento verticale alla visualizzazione

1. Individuare la sezione del file index.cshtml che visualizza i risultati (inizia con **@if (Model != null)** ).

1. Sostituire la sezione con il codice seguente. La nuova sezione **&lt;div&gt;** racchiude l'area che dovrebbe essere scorrevole e aggiunge un attributo **overflow-y** e una chiamata a una funzione **onscroll** denominata "scrolled()", come illustrato di seguito.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Direttamente sotto il ciclo, dopo il tag &lt;/div&gt;, aggiungere la funzione **scrolled**.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    L'istruzione **if** nello script precedente verifica se l'utente ha effettuato lo scorrimento fino alla fine della barra di scorrimento verticale. In caso affermativo, nel controller **Home** viene effettuata una chiamata a un'azione denominata **NextAsync**. Il controller non ha bisogno di altre informazioni per restituire la pagina di dati successiva. Questi dati vengono quindi formattati usando gli stessi stili HTML della pagina originale. Se viene restituito alcun risultato, non viene aggiunto nulla e tutto rimane invariato.

### <a name="handle-the-next-action"></a>Gestire l'azione Next

Sono solo tre le azioni da inviare al controller: la prima esecuzione dell'app, che chiama **Index()** , la prima ricerca eseguita dall'utente, che chiama **Index(model)** e quindi le chiamate successive per ottenere ulteriori risultati tramite **Next(model)** .

1. Aprire il file del controller home ed eliminare il metodo **RunQueryAsync** dall'esercitazione originale.

1. Sostituire l'azione **Index(model)** con il codice seguente. Ora gestisce il campo **paging** quando è Null o impostato su "next", nonché gestisce la chiamata a Ricerca cognitiva di Azure.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Analogamente al metodo di paginazione numerata, si useranno le impostazioni di ricerca **Skip** e **Size** per richiedere solo i dati che devono essere restituiti.

1. Aggiungere l'azione **NextAsync** al controller home. Notare che questa azione restituisce un elenco al quale vengono aggiunti due elementi per ogni hotel, ovvero il nome e la descrizione. Questo formato è impostato in modo da corrispondere all'uso della funzione **scrolled** dei dati restituiti nella visualizzazione.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Se si riceve un errore di sintassi in **List&lt;string&gt;** , aggiungere la direttiva **using** seguente all'inizio del file del controller.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compilare ed eseguire il progetto

A questo punto, selezionare **Avvia senza eseguire debug** (oppure premere F5).

1. Cercare un termine per cui verranno restituiti numerosi risultati, ad esempio "pool" e quindi testare la barra di scorrimento verticale. Verificare se viene attivata una nuova pagina di risultati.

    ![Scorrimento infinito nei risultati di "pool"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Per garantire che nella prima pagina venga visualizzata una barra di scorrimento, la prima pagina di risultati deve superare leggermente l'altezza dell'area in cui viene visualizzata. Nell'esempio **.box1** ha un'altezza di 30 pixel, mentre **.box2** ha un'altezza di 100 pixel _e_ un margine inferiore di 24 pixel. Per ogni voce vengono quindi usati 154 pixel. Tre voci occupano fino a 3 x 154 = 462 pixel. Per garantire la visualizzazione di una barra di scorrimento verticale, l'altezza impostata per l'area di visualizzazione deve essere inferiore a 462 pixel: è sufficiente anche impostarla su 461. Questo problema si verifica solo nella prima pagina, dopo aver verificato che verrà sicuramente visualizzata una barra di scorrimento. La riga da aggiornare è: **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

1. Scorrere fino alla fine dei risultati. Notare che tutte le informazioni sono ora incluse nell'unica pagina della visualizzazione. È possibile scorrere fino all'inizio senza attivare alcuna chiamata al server.

Per attivare il caricamento di una nuova pagina di risultati, sistemi di scorrimento infinito più sofisticato possono prevedere l'uso della rotellina del mouse o di altri meccanismi analoghi. Lo scorrimento infinito non verrà ulteriormente trattato in queste esercitazioni, ma è certo interessante perché consente di evitare clic del mouse aggiuntivi, per cui è probabile che si vogliano valutare le altre opzioni disponibili.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* La paginazione numerata è utile per le ricerche in cui l'ordine dei risultati è in un certo senso arbitrario, ovvero quando i dati a cui gli utenti sono interessati potrebbero trovarsi nelle pagine successive.
* Lo scorrimento infinito è utile quando l'ordine dei risultati è particolarmente importante, ad esempio se i risultati vengono ordinati in base alla distanza dal centro di una città di destinazione.
* La paginazione numerata offre migliori funzionalità di spostamento. un utente può, ad esempio, ricordare che a pagina 6 era presente un risultato interessante. Con lo scorrimento infinito non esistono invece riferimenti ugualmente semplici.
* Lo scorrimento infinito è basato su un approccio semplice: basta scorrere verso l'alto o verso il basso senza dover fare clic su numeri di pagina.
* Una funzionalità chiave dello scorrimento infinito è che i risultati vengono aggiunti a una pagina esistente, senza sostituirla, garantendo una maggiore efficienza.
* L'archivio temporaneo viene mantenuto per una sola chiamata e deve essere reimpostato se si intende usarlo in altre chiamate.

## <a name="next-steps"></a>Passaggi successivi

La paginazione è fondamentale per l'esperienza di ricerca. Dopo aver esaminato la paginazione, il passaggio successivo consiste nel migliorare ulteriormente l'esperienza utente aggiungendo ricerche con completamento automatico.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Aggiungere completamento automatico e suggerimenti - Ricerca cognitiva di Azure](tutorial-csharp-type-ahead-and-suggestions.md)