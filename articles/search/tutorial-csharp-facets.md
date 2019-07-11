---
title: Esercitazione per C# sull'uso di facet per semplificare l'esplorazione - Ricerca di Azure
description: Questa esercitazione si basa sul progetto "Paginazione dei risultati della ricerca - Ricerca di Azure", per aggiungere l'esplorazione basata su facet. I facet possono essere usati per limitare facilmente una ricerca.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443790"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>Esercitazione per C#: Usare facet per semplificare l'esplorazione - Ricerca di Azure

I facet vengono usati per semplificare l'esplorazione e offrono all'utente un set di collegamenti da usare per contestualizzare la ricerca. I facet sono attributi dei dati, ad esempio la categoria, o una caratteristica specifica, di un hotel nei dati di esempio.

Questa esercitazione si basa sul progetto di paginazione creato in [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca di Azure](tutorial-csharp-paging.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Impostare le proprietà del modello come _IsFacetable_
> * Aggiungere l'esplorazione basata su facet all'app

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

Predisporre il progetto [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca di Azure](tutorial-csharp-paging.md) perché sia attivo e in esecuzione. Questo progetto può essere una versione personalizzata o può essere installato da GitHub: [Creare la prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Impostare le proprietà del modello come IsFacetable

Affinché una proprietà del modello possa essere individuata nella ricerca di facet, deve essere contrassegnata con **IsFacetable**.

1. Esaminare la classe **Hotel**. **Category** e **Tags**, ad esempio, sono contrassegnate come **IsFacetable**, ma **HotelName** e **Description** non lo sono. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. In questa esercitazione non verrà modificato alcun tag e di conseguenza è possibile chiudere il file hotel.cs lasciandolo inalterato.

    > [!Note]
    > Una ricerca di facet genererà un errore se un campo richiesto nella ricerca non è contrassegnato correttamente.


## <a name="add-facet-navigation-to-your-app"></a>Aggiungere l'esplorazione basata su facet all'app

Per questo esempio, si permetterà all'utente di selezionare una categoria di hotel o un servizio dagli elenchi di collegamenti mostrati a sinistra dei risultati. L'utente inizia immettendo il testo della ricerca, quindi può limitare i risultati della ricerca selezionando una categoria, per poi limitarli ulteriormente selezionando un servizio oppure può selezionare prima il servizio (l'ordine non è importante).

È necessario che il controller passi gli elenchi di facet alla visualizzazione. È necessario mantenere le selezioni dell'utente durante l'avanzamento della ricerca e, anche in questo caso, verrà usata l'archiviazione temporanea come meccanismo per la conservazione dei dati.

![Uso dell'esplorazione basata su facet per limitare una ricerca della parola "pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Aggiungere stringhe di filtro al modello SearchData

1. Aprire il file SearchData.cs e aggiungere le proprietà della stringa per la classe **SearchData** perché contenga le stringhe di filtro dei facet.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Aggiungere il metodo di azione Facet

Il controller di domotica richiede una nuova azione, **Facet**, ed esegue l'aggiornamento alle azioni **Index** e **Page** esistenti, nonché al metodo **RunQueryAsync**.

1. Aprire il file del controller di domotica e aggiungere l'istruzione **using** per abilitare il costrutto **List&lt;stringa&gt;** .

    ```cs
    using System.Collections.Generic;
    ```

2. Sostituire il metodo di azione **Index(SearchData model)** .

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Sostituire il metodo di azione **Page(SearchData model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
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

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Aggiungere un metodo di azione **Facet(SearchData model)** perché venga attivato quando l'utente fa clic sul collegamento di un facet. Il modello conterrà un filtro di ricerca di categorie e un filtro di ricerca di servizi. Questo può essere aggiunto dopo l'azione **Page**.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Configurare il filtro di ricerca

Quando un utente seleziona un determinato facet, ad esempio se fa clic sulla categoria **Resort and Spa**, nei risultati vengono restituiti solo gli hotel specificati come appartenenti a questa categoria. Per limitare una ricerca in questo modo, è necessario configurare un _filtro_.

1. Sostituire il metodo **RunQueryAsync** con il codice seguente. In primo luogo, accetta una stringa di filtro di categorie e una stringa di filtro di servizi e imposta il parametro **Filter** di **SearchParameters**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

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
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Sono state aggiunte le proprietà **Category** e **Tags** all'elenco degli elementi **Select** da restituire. Questa aggiunta non è un requisito per il funzionamento dell'esplorazione basata su facet, ma le informazioni vengono usate per verificare la corretta applicazione del filtro.

### <a name="add-lists-of-facet-links-to-the-view"></a>Aggiungere elenchi di collegamenti di facet alla visualizzazione

La visualizzazione richiederà alcune modifiche significative. 

1. Per iniziare, aprire il file hotels.css (nella cartella wwwroot/css) e aggiungere le classi seguenti.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. Per la visualizzazione, l'output viene organizzato in una tabella in modo da allineare accuratamente l'elenco di facet a sinistra e i risultati a destra. Aprire il file index.cshtml. Sostituire l'intero contenuto dei tag HTML &lt;body&gt; con il codice seguente.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
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

                                <td class="tdPage">
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
                                    <td class="tdPage">
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

                                <td class="tdPage">
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

                                <td class="tdPage">
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
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Notare l'uso della chiamata **HTml.ActionLink**. Questa chiamata comunica le stringhe di filtro valide al controller, quando l'utente fa clic sul collegamento di un facet. 

### <a name="run-and-test-the-app"></a>Eseguire e testare l'app

Il vantaggio dell'esplorazione basata su facet per l'utente consiste nella possibilità di limitare le ricerche con un solo clic. Questo verrà mostrato nella sequenza seguente.

1. Eseguire l'app e digitare "airport" come testo di ricerca. Verificare che l'elenco dei facet venga visualizzato correttamente a sinistra. Questi facet si applicano tutti agli hotel che contengono la parola "airport" all'interno dei dati di testo, con il numero di occorrenze della parola.

    ![Uso dell'esplorazione basata su facet per limitare una ricerca della parola "airport"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Fare clic sulla categoria **Resort and Spa**. Verificare che tutti i risultati appartengano a questa categoria.

    ![Limitazione della ricerca a "Resort and Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Fare clic sul servizio **continental breakfast**. Verificare che tutti i risultati siano ancora inclusi nella categoria "Resort and Spa", con il servizio selezionato.

    ![Limitazione della ricerca a "continental breakfast"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Provare a selezionare qualsiasi altra categoria, quindi un servizio e visualizzare i risultati limitati. Provare in ordine contrario, prima un servizio e quindi una categoria.

    >[!Note]
    > Quando viene effettuata una selezione in un elenco di facet (ad esempio la categoria), questa sostituisce qualsiasi selezione precedente all'interno dell'elenco di categorie.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* È fondamentale contrassegnare come **IsFacetable** tutte le proprietà che devono essere incluse nell'esplorazione basata su facet.
* L'esplorazione basata su facet offre all'utente un metodo semplice e intuitivo di limitare i risultati di una ricerca.
* L'esplorazione basata su facet è più efficace se divisa in sezioni (categorie di hotel, servizi di un hotel, fasce di prezzo, intervalli di classificazione e così via), ognuna delle quali con un'intestazione appropriata.

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione verrà esaminato l'ordinamento dei risultati. Fino a questo punto, i risultati vengono ordinati semplicemente in base alla posizione nel database.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Ordinare i risultati - Ricerca di Azure](tutorial-csharp-orders.md)
