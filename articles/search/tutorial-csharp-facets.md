---
title: Esercitazione per C# sull'uso di facet per semplificare l'esplorazione
titleSuffix: Azure Cognitive Search
description: Continua da "Paginazione dei risultati" per l'aggiunta dell'esplorazione in base a facet. Come usare i facet per limitare facilmente una ricerca.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ab15af07c5f63d375d8fdb4fc38e0853e207a0be
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667283"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Esercitazione: Aggiungere l'esplorazione in base a facet con .NET SDK

I facet consentono l'esplorazione autonoma fornendo un set di collegamenti per filtrare i risultati. In questa esercitazione una struttura di esplorazione basata su facet viene posizionata sul lato sinistro della pagina, con etichette e testo selezionabile per tagliare i risultati.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Impostare le proprietà del modello come _IsFacetable_
> * Aggiungere l'esplorazione basata su facet all'app

## <a name="overview"></a>Panoramica

I facet sono basati sui campi dell'indice di ricerca. Una richiesta di query che include facet=[stringa] fornisce il campo su cui basare il facet. In genere si includono più facet, ad esempio `&facet=category&facet=amenities`, separati da un carattere di e commerciale (&). Per implementare una struttura di esplorazione basata su facet, è necessario specificare sia i facet che i filtri. Il filtro viene usato negli eventi clic per restringere i risultati. Se ad esempio si fa clic su "budget", i risultati vengono filtrati in base a tale criterio.

Questa esercitazione estende il progetto di paginazione creato nell'esercitazione [Aggiungere la paginazione ai risultati della ricerca](tutorial-csharp-paging.md).

Una versione completa del codice di questa esercitazione si trova nel progetto seguente:

* [4-add-facet-navigation (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Prerequisiti

* Soluzione [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Questo progetto può essere una versione personalizzata, completata nell'esercitazione precedente, oppure una copia di GitHub.

L'esercitazione è stata aggiornata per usare il pacchetto [Azure.Search.Documents (versione 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Per una versione precedente di .NET SDK, vedere l'[esempio di codice Microsoft.Azure.Search (versione 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="set-model-properties-as-isfacetable"></a>Impostare le proprietà del modello come IsFacetable

Affinché una proprietà del modello possa essere individuata nella ricerca di facet, deve essere contrassegnata con **IsFacetable**.

1. Esaminare la classe **Hotel**. **Category** e **Tags**, ad esempio, sono contrassegnate come **IsFacetable**, ma **HotelName** e **Description** non lo sono. 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. In questa esercitazione non verrà modificato alcun tag e di conseguenza è possibile chiudere il file hotel.cs lasciandolo inalterato.

    > [!Note]
    > Una ricerca di facet genererà un errore se un campo richiesto nella ricerca non è contrassegnato correttamente.

## <a name="add-facet-navigation-to-your-app"></a>Aggiungere l'esplorazione basata su facet all'app

Per questo esempio, si permetterà all'utente di selezionare una categoria di hotel o un servizio dagli elenchi di collegamenti mostrati a sinistra dei risultati. L'utente inizia immettendo un testo di ricerca, quindi restringe progressivamente i risultati della ricerca selezionando una categoria o un servizio.

È compito del controller passare gli elenchi di facet alla visualizzazione. Per mantenere le selezioni dell'utente durante l'avanzamento della ricerca, viene usata l'archiviazione temporanea come meccanismo per la conservazione dei dati.

![Uso dell'esplorazione basata su facet per limitare una ricerca della parola "pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Aggiungere stringhe di filtro al modello SearchData

1. Aprire il file SearchData.cs e aggiungere le proprietà della stringa per la classe **SearchData** perché contenga le stringhe di filtro dei facet.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Aggiungere il metodo di azione Facet

Il controller home richiede ora una nuova azione, **Facet**, nonché gli aggiornamenti per le azioni **Index** e **Page** esistenti e per il metodo **RunQueryAsync**.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Sostituire il metodo di azione **Index(SearchData model)** .

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
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Sostituire il metodo di azione **PageAsync(SearchData model)** .

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
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

1. Aggiungere un metodo di azione **FacetAsync(SearchData model)** da attivare quando l'utente fa clic sul collegamento di un facet. Il modello conterrà un filtro di ricerca di categorie o di servizi. Aggiungerlo dopo l'azione **PageAsync**.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
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
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
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

1. Sostituire il metodo **RunQueryAsync** con il codice seguente. In primo luogo, accetta una stringa di filtro di categorie e una stringa di filtro di servizi e imposta il parametro **Filter** di **SearchOptions**.

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

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

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

    Si noti che sono state aggiunte le proprietà **Category** e **Tags** all'elenco degli elementi **Select** da restituire. Questa aggiunta non è un requisito per il funzionamento dell'esplorazione basata su facet, ma le informazioni vengono usate per verificare la corretta applicazione dei filtri.

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

1. Per la visualizzazione, organizzare l'output in una tabella, in modo da allineare l'elenco di facet a sinistra e i risultati a destra. Aprire il file index.cshtml. Sostituire l'intero contenuto dei tag HTML &lt;body&gt; con il codice seguente.

    ```html
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
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
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
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
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
                                        <td class="tdPage">
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

                                    <td class="tdPage">
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

                                    <td class="tdPage">
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

1. Fare clic sulla categoria **Resort and Spa**. Verificare che tutti i risultati appartengano a questa categoria.

    ![Limitazione della ricerca a "Resort and Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Fare clic sul servizio **continental breakfast**. Verificare che tutti i risultati siano ancora inclusi nella categoria "Resort and Spa", con il servizio selezionato.

    ![Limitazione della ricerca a "continental breakfast"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Provare a selezionare qualsiasi altra categoria, quindi un servizio e visualizzare i risultati limitati. Provare in ordine contrario, prima un servizio e quindi una categoria. Inviare una ricerca vuota per reimpostare la pagina.

    >[!Note]
    > Quando viene effettuata una selezione in un elenco di facet (ad esempio la categoria), questa sostituisce qualsiasi selezione precedente all'interno dell'elenco di categorie.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* È necessario contrassegnare ogni campo impostabile come facet con la proprietà **IsFacetable** per l'inclusione nell'esplorazione basata su facet.
* I facet vengono combinati con i filtri per ridurre i risultati.
* I facet sono cumulativi, con ogni selezione che si basa su quella procedente per restringere ulteriormente i risultati.

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione verrà esaminato l'ordinamento dei risultati. Fino a questo punto, i risultati vengono ordinati semplicemente in base alla posizione nel database.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Ordinare i risultati - Ricerca cognitiva di Azure](tutorial-csharp-orders.md)
