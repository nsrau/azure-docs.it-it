---
title: Esercitazione per C# sulla paginazione dei risultati della ricerca - Ricerca di Azure
description: Questa esercitazione si basa sul progetto "Creare la prima app - Ricerca di Azure" e consente di scegliere tra due sistemi di paginazione. Il primo usa un intervallo di pulsanti di numeri di pagina, nonché i pulsanti della prima, della successiva, della precedente e dell'ultima pagina. Il secondo sistema di paginazione usa lo scorrimento infinito, attivato dallo spostamento di una barra di scorrimento verticale fino al limite inferiore.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 7e6c433168b73c6b58d13d4698bed55d7c18ec58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434644"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca di Azure

Informazioni su come implementare due diversi sistemi di paginazione, il basato sui numeri di pagina e il secondo sullo scorrimento infinito. Entrambi i sistemi di paginazione sono ampiamente diffusi. Per selezionare quello più adatto, è necessario considerare l'esperienza utente che si intende ottenere con i risultati. Questa esercitazione consente di creare i sistemi di paginazione nel progetto creato in [Esercitazione per C#: Creare la prima app - Ricerca di Azure](tutorial-csharp-create-first-app.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Estendere l'app con la paginazione numerata
> * Estendere l'app con lo scorrimento infinito

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

Disporre del progetto [Esercitazione per C#: Creare la prima app - Ricerca di Azure](tutorial-csharp-create-first-app.md) attivo e in esecuzione. Questo progetto può essere una versione personalizzata oppure può essere installato da GitHub: [Creare la prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Estendere l'app con la paginazione numerata

La paginazione numerata è il sistema di paginazione scelto dai principali motori di ricerca Internet e dalla maggior parte degli altri siti di ricerca. La paginazione numerata include in genere le opzioni "successiva" e "precedente", oltre a un intervallo di numeri di pagina effettivi. Possono essere disponibili anche le opzioni "prima pagina" e "ultima pagina". Si tratta di opzioni che certamente consentono all'utente di controllare l'esplorazione dei risultati basati su pagine.

Nell'esercitazione verrà aggiunto un sistema che include le opzioni per prima, precedente, successiva e ultima pagina, oltre a numeri di pagina che non iniziano da 1, ma si riferiscono a pagine circostanti a quella attiva. Ad esempio, se l'utente sta esaminando la pagina 10, vengono visualizzati i numeri di pagina 8, 9, 10, 11 e 12.

Il sistema sarà abbastanza flessibile per consentire l'impostazione del numero di numeri di pagina visibili in una variabile globale.

Il sistema considererà speciali i pulsanti dei numeri di pagina all'estrema sinistra e all'estrema destra, in quanto attivano la modifica dell'intervallo di numeri di pagina visualizzati. Se, ad esempio, vengono visualizzati i numeri di pagina 8, 9, 10, 11 e 12 e l'utente fa clic su 8, l'intervallo di numeri di pagina visualizzati cambia in 6, 7, 8, 9 e 10. Se si seleziona 12, verrà applicato uno spostamento analogo verso destra.

### <a name="add-paging-fields-to-the-model"></a>Aggiungere campi di paginazione al modello

Aprire la soluzione della pagina di ricerca di base.

1. Aprire il file di modello SearchData.cs.

2. Aggiungere innanzitutto alcune variabili globali. In MVC le variabili globali sono dichiarate nella relativa classe statica. **ResultsPerPage** consente di impostare il numero di risultati per pagina. **MaxPageRange** determina il numero di numeri di pagina visibili nella visualizzazione. **PageRangeDelta** determina il numero di pagine per lo spostamento verso sinistra o verso destra dell'intervallo di pagine, quando viene selezionato il numero di pagina all'estrema sinistra o all'estrema destra. Quest'ultimo numero si trova in genere a metà di **MaxPageRange**. Aggiungere il codice seguente nello spazio dei nomi.

    ```cs
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

3. Aggiungere le proprietà di paginazione alla classe **SearchData**, ad esempio dopo la proprietà **searchText**.

    ```cs
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

    ```cs
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
                        @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                            @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                        @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

2. Aggiungere alcune classi di paginazione all'elenco di stili HTML nel file hotels.css. La classe **pageSelected** consente di identificare la pagina attualmente visualizzata dall'utente (convertendo il numero in grassetto) nell'elenco dei numeri di pagina.

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

1. Aprire il file HomeController.cs e aggiungere l'azione **Page**. Questa azione risponde a una qualsiasi delle opzioni di pagina selezionate.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
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
    > Le chiamate a **TempData** archiviano un valore (un **oggetto**) nell'archivio temporaneo, anche se questo tipo di archivio viene mantenuto _solo_ per una chiamata. Se si archivia un elemento nei dati temporanei, risulterà disponibile per la chiamata successiva a un'azione del controller, ma non lo sarà più per le chiamate seguenti. In considerazione della durata ridotta le proprietà di paginazione e il testo da cercare vengono salvati nuovamente nell'archivio temporaneo a ogni chiamata a **Page**.

2. L'azione **Index(model)** deve essere aggiornata per archiviare le variabili temporanee e per aggiungere il parametro della pagina all'estrema sinistra alla chiamata a **RunQueryAsync**.

    ```cs
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

3. Il metodo **RunQueryAsync** deve essere aggiornato in modo significativo. Si usano i campi **Skip**, **Top** e **IncludeTotalResultCount** della classe **SearchParameters** per richiedere una sola pagina di risultati, a partire dall'impostazione **Skip**. È inoltre necessario calcolare le variabili di paginazione per la visualizzazione. Sostituire l'intero metodo con il codice seguente.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
                   SearchMode = SearchMode.All,

                   // Skip past results that have already been returned.
                   Skip = page * GlobalVariables.ResultsPerPage,

                   // Take only the next page worth of results.
                   Top = GlobalVariables.ResultsPerPage,

                   // Include the total number of results.
                   IncludeTotalResultCount = true,
               };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

4. È infine necessario apportare una piccola modifica alla visualizzazione. La variabile **resultsList.Results.Count** conterrà ora il numero di risultati restituiti in una sola pagina (3 nell'esempio) e non il numero totale. Dal momento che **IncludeTotalResultCount** è impostato su true, la variabile **resultsList.Count** ora contiene il numero totale di risultati. Individuare il punto il cui viene visualizzato il numero di risultati nella visualizzazione e sostituirlo con il codice seguente.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > È possibile notare un calo delle prestazioni, anche se non eccessivo, se si imposta **IncludeTotalResultCount** su true, perché questo totale deve essere calcolato da Ricerca di Azure. Con i set di dati complessi viene visualizzato un avviso in cui si informa che il valore restituito è un'_approssimazione_. Per i dati degli alberghi invece sarà accurato.

### <a name="compile-and-run-the-app"></a>Compilare ed eseguire l'app

A questo punto, selezionare **Avvia senza eseguire debug** (oppure premere F5).

1. Cercare testo per cui verranno restituiti numerosi risultati, ad esempio "wifi". Verificare se la paginazione dei risultati è accurata.

    ![Paginazione numerata nei risultati di "pool"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Provare a fare clic sui numeri di pagina all'estrema destra e all'estrema sinistra. Verificare se i numeri di pagina vengono spostati in modo corretto al centro della pagina corrente.

3. Verificare inoltre l'utilità delle opzioni per la prima e l'ultima pagina. Queste opzioni sono infatti usate solo in alcune delle ricerche Web più diffuse.

4. Passare all'ultima pagina di risultati. L'ultima pagina è l'unica pagina che può contenere un numero di risultati inferiore a quello di **ResultsPerPage**.

    ![Esame dell'ultima pagina relativa a "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Digitare "town" e fare clic sul pulsante di ricerca. Se il numero di risultati è inferiore a quello previsto per una pagina, non verrà visualizzata alcuna opzione di paginazione.

    ![Ricerca di "town"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

A questo punto salvare il progetto per passare a un altro sistema di paginazione.

## <a name="extend-your-app-with-infinite-scrolling"></a>Estendere l'app con lo scorrimento infinito

Lo scorrimento infinito viene attivato quando un utente scorre una barra di scorrimento verticale fino all'ultimo dei risultati visualizzati. In questo evento viene effettuata una chiamata al server per ottenere la pagina successiva di risultati. Se non sono presenti altri risultati, non viene restituito nulla e la barra di scorrimento verticale non cambia. Se sono presenti altri risultati, questi vengono aggiunti alla pagina corrente e la barra di scorrimento cambia per indicare che sono disponibili altri risultati.

Quello che conta in questo caso è che la pagina visualizzata non viene sostituita e i nuovi risultati vengono aggiunti a tale pagina. Un utente può sempre scorrere fino ai primi risultati della ricerca.

Per implementare lo scorrimento infinito, si inizierà con il progetto prima dell'aggiunta di uno qualsiasi degli elementi di scorrimento dei numeri di pagina. Se quindi è necessario, creare un'altra copia della pagina di ricerca di base di GitHub: [Creare la prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Aggiungere campi di paginazione al modello

1. Aggiungere innanzitutto una proprietà **paging** alla classe **SearchData** (nel file del modello SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Questa variabile è una stringa che contiene "next" se deve essere inviata la pagina di risultati successiva oppure deve essere Null per la prima pagina di una ricerca.

2. Nello stesso file e nello spazio dei nomi, aggiungere una classe di variabili globali con un'unica proprietà. In MVC le variabili globali sono dichiarate nella relativa classe statica. **ResultsPerPage** consente di impostare il numero di risultati per pagina. 

    ```cs
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

2. Sostituire la sezione con il codice seguente. La nuova sezione **&lt;div&gt;** racchiude l'area che dovrebbe essere scorrevole e aggiunge un attributo **overflow-y** e una chiamata a una funzione **onscroll** denominata "scrolled()", come illustrato di seguito.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Direttamente sotto il ciclo, dopo il tag &lt;/div&gt;, aggiungere la funzione **scrolled**.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
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

    L'istruzione **if** nello script precedente verifica se l'utente ha effettuato lo scorrimento fino alla fine della barra di scorrimento verticale. In caso affermativo, nel controller **Home** viene effettuata una chiamata a un'azione denominata **Next**. Il controller non ha bisogno di altre informazioni per restituire la pagina di dati successiva. Questi dati vengono quindi formattati usando gli stessi stili HTML della pagina originale. Se viene restituito alcun risultato, non viene aggiunto nulla e tutto rimane invariato.

### <a name="handle-the-next-action"></a>Gestire l'azione Next

Sono solo tre le azioni da inviare al controller: la prima esecuzione dell'app, che chiama **Index()** , la prima ricerca eseguita dall'utente, che chiama **Index(model)** e quindi le chiamate successive per ottenere ulteriori risultati tramite **Next(model)** .

1. Aprire il file del controller home ed eliminare il metodo **RunQueryAsync** dall'esercitazione originale.

2. Sostituire l'azione **Index(model)** con il codice seguente. Ora gestisce il campo **paging** quando è Null o impostato su "next", nonché gestisce la chiamata a Ricerca di Azure.

    ```cs
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
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

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

    Analogamente al metodo di paginazione numerata, si useranno le impostazioni di ricerca **Skip** e **Top** per richiedere solo i dati che devono essere restituiti.

3. Aggiungere l'azione **Next** al controller home. Notare che questa azione restituisce un elenco al quale vengono aggiunti due elementi per ogni albergo, ovvero il nome e la descrizione. Questo formato è impostato in modo da corrispondere all'uso della funzione **scrolled** dei dati restituiti nella visualizzazione.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Se si riceve un errore di sintassi in **List&lt;string&gt;** , aggiungere la direttiva **using** seguente all'inizio del file del controller.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compilare ed eseguire il progetto

A questo punto, selezionare **Avvia senza eseguire debug** (oppure premere F5).

1. Cercare un termine per cui verranno restituiti numerosi risultati, ad esempio "pool" e quindi testare la barra di scorrimento verticale. Verificare se viene attivata una nuova pagina di risultati.

    ![Scorrimento infinito nei risultati di "pool"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Per garantire che nella prima pagina venga visualizzata una barra di scorrimento, la prima pagina di risultati deve superare leggermente l'altezza dell'area in cui viene visualizzata. Nell'esempio **.box1** ha un'altezza di 30 pixel, mentre **.box2** ha un'altezza di 100 pixel _e_ un margine inferiore di 24 pixel. Per ogni voce vengono quindi usati 154 pixel. Tre voci occupano fino a 3 x 154 = 462 pixel. Per garantire la visualizzazione di una barra di scorrimento verticale, l'altezza impostata per l'area di visualizzazione deve essere inferiore a 462 pixel: è sufficiente anche impostarla su 461. Questo problema si verifica solo nella prima pagina, dopo aver verificato che verrà sicuramente visualizzata una barra di scorrimento. La riga da aggiornare è: **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

2. Scorrere fino alla fine dei risultati. Notare che tutte le informazioni sono ora incluse nell'unica pagina della visualizzazione. È possibile scorrere fino all'inizio senza attivare alcuna chiamata al server.

Per attivare il caricamento di una nuova pagina di risultati, sistemi di scorrimento infinito più sofisticato possono prevedere l'uso della rotellina del mouse o di altri meccanismi analoghi. Lo scorrimento infinito non verrà ulteriormente trattato in queste esercitazioni, ma è certo interessante perché consente di evitare clic del mouse aggiuntivi, per cui è probabile che si vogliano valutare le altre opzioni disponibili.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* La paginazione numerata è adatta per le ricerche in cui l'ordine dei risultati è in un certo senso arbitrario, ovvero che i dati a cui gli utenti sono interessati potrebbero trovarsi nelle pagine successive.
* Lo scorrimento infinito è la scelta ideale quando l'ordine dei risultati è particolarmente rilevante, ad esempio se i risultati vengono ordinati in base alla distanza dal centro di una città di destinazione.
* La paginazione numerata offre migliori funzionalità di spostamento: un utente può, ad esempio, ricordare che a pagina 6 era presente un risultato interessante. Con lo scorrimento infinito non esistono invece riferimenti ugualmente semplici.
* Lo scorrimento infinito è certamente basato su un approccio semplice: basta scorrere verso l'alto o verso il basso senza dover fare clic su numeri di pagina.
* Una funzionalità chiave dello scorrimento infinito è che i risultati vengono aggiunti a una pagina esistente, senza sostituirla, garantendo una maggiore efficienza.
* L'archivio temporaneo viene mantenuto per una sola chiamata e deve essere reimpostato se si intende usarlo in altre chiamate.


## <a name="next-steps"></a>Passaggi successivi

La paginazione è fondamentale per le ricerche Internet. Dopo aver esaminato la paginazione, il passaggio successivo consiste nel migliorare ulteriormente l'esperienza utente aggiungendo ricerche con completamento automatico.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Aggiungere completamento automatico e suggerimenti - Ricerca di Azure](tutorial-csharp-type-ahead-and-suggestions.md)
