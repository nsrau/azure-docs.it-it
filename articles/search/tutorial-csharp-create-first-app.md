---
title: Esercitazione per C# su come creare la prima app
titleSuffix: Azure Cognitive Search
description: Informazioni dettagliate su come creare la prima app di ricerca in C#. L'esercitazione fornisce sia un collegamento per il download di un'app funzionante in GitHub che la procedura completa per creare l'app da zero.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2414570a1d483cd7630e628b13c92dbdc331370d
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759136"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Esercitazione: Creare la prima app di ricerca con .NET SDK

Questa esercitazione illustra come creare un'app Web che esegue query e restituisce risultati da un indice di ricerca usando Ricerca cognitiva di Azure e Visual Studio.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di sviluppo
> * Modellare le strutture di dati
> * Creare un'app Web per raccogliere gli input delle query e visualizzare i risultati
> * Definire un metodo di ricerca
> * Testare l'app

Verrà illustrato inoltre quanto sia semplice una chiamata di ricerca. Le istruzioni principali nel codice che verrà sviluppato sono incapsulate nelle poche righe riportate di seguito.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Una sola chiamata esegue una query sull'indice e restituisce i risultati.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Ricerca di *pool*" border="true":::

## <a name="overview"></a>Panoramica

Questa esercitazione usa un indice di esempio ospitato esistente per illustrare la creazione di una pagina di ricerca che raccoglie una stringa di query per la richiesta e restituisce i risultati. L'indice contiene dati di hotel fittizi. Dopo aver creato una pagina di base, è possibile migliorarla nelle lezioni successive e includere l'impaginazione, i facet e un'esperienza di completamento automatico.

Una versione completa del codice di questa esercitazione si trova nel progetto seguente:

* [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

L'esercitazione è stata aggiornata per usare il pacchetto Azure.Search.Documents (versione 11). Per una versione precedente di .NET SDK, vedere l'[esempio di codice Microsoft.Azure.Search (versione 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="prerequisites"></a>Prerequisiti

Poiché si usa un indice di ricerca di esempio pubblico ospitato da Microsoft, non è necessario un servizio di ricerca o un account Azure per questa esercitazione.

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Libreria client di Ricerca cognitiva di Azure (versione 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Installare ed eseguire il progetto da GitHub

Se si vuole procedere direttamente a un'app funzionante, seguire la procedura seguente per scaricare ed eseguire il codice completo.

1. Individuare in GitHub l'esempio per la [Creare la prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples/v11).

1. Nella [cartella radice](https://github.com/Azure-Samples/azure-search-dotnet-samples) selezionare **Code** (Codice), quindi **Clone** (Clona) o **Download ZIP** (Scarica ZIP) per creare una copia locale privata del progetto.

1. In Visual Studio trovare e aprire la soluzione per la pagina di ricerca di base ("1-basic-search-page"), quindi selezionare **Avvia senza eseguire debug** (o premere F5) per creare ed eseguire il programma.

1. Si tratta di un indice di hotel, quindi digitare alcune parole che è possibile usare per cercare gli hotel (ad esempio "wifi", "view", "bar", "parking") ed esaminare i risultati.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Ricerca di *pool*" border="true":::

Il progetto non dovrebbe presentare problemi e l'app Web dovrebbe avviarsi. In questa app sono inclusi molti dei componenti essenziali per ricerche più sofisticate, pertanto è consigliabile esaminarla e ricrearla seguendo la procedura dettagliata. Le sezioni seguenti trattano queste procedure.

## <a name="set-up-a-development-environment"></a>Configurare un ambiente di sviluppo

Per creare il progetto da zero e quindi approfondire i concetti relativi a Ricerca cognitiva di Azure, iniziare con un progetto di Visual Studio.

1. In Visual Studio selezionare **Nuovo** > **Progetto** e quindi **Applicazione Web ASP.NET Core**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Ricerca di *pool*" border="true":::

1. Assegnare un nome al progetto, ad esempio "FirstSearchApp", e impostare la località. Selezionare **Crea**.

1. Scegliere il modello di progetto **Applicazione Web (Model-View-Controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Ricerca di *pool*" border="true":::

1. Installare la libreria client. In **Strumenti** > **Gestione pacchetti NuGet** > **Gestisci i pacchetti NuGet per la soluzione** selezionare **Sfoglia** e quindi cercare "azure.search.documents". Installare **Azure.search.Documents** (versione 11 o successiva), accettando i contratti di licenza e le dipendenze.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Ricerca di *pool*" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Inizializzare Ricerca cognitiva di Azure

Per questo esempio, verranno usati dati di hotel disponibili pubblicamente. Questi dati costituiscono una raccolta arbitraria di 50 nomi di hotel fittizi con relative descrizioni, creati esclusivamente allo scopo di fornire i dati per la demo. Per accedere a questi dati, specificare un nome e una chiave API.

1. Aprire il file **appsettings.json** e sostituire le righe predefinite con il nome e la chiave seguenti. La chiave API mostrata qui non è un esempio di chiave, è *esattamente* la chiave necessaria per accedere ai dati dell'hotel. Il file dovrà risultare simile al seguente.

    ```csharp
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

1. In Esplora soluzioni selezionare il file, quindi in Proprietà impostare l'opzione **Copia nella directory di output** su **Copia se più recente**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Ricerca di *pool*" border="true":::

## <a name="model-data-structures"></a>Modellare le strutture di dati

I modelli (classi C#) vengono usati per comunicare i dati tra il client (la vista), il server (il controller) e anche il cloud di Azure con l'architettura MVC (Model-View-Controller). In genere questi modelli riflettono la struttura dei dati a cui si accede.

In questo passaggio verranno modellate le strutture di dati dell'indice di ricerca, oltre alla stringa di ricerca usata nelle comunicazioni tra visualizzazione e controller. Nell'indice di hotel, ogni hotel ha molte camere e un indirizzo in più parti. Nel complesso, la rappresentazione completa di un hotel è una struttura di dati gerarchici e annidati. Per creare ogni componente sono necessarie tre classi.

Le classi **Hotel**, **Address** e **Room** sono note come [*tipi complessi*](search-howto-complex-data-types.md), una funzionalità importante di Ricerca cognitiva di Azure. I tipi complessi possono includere molti livelli al di sotto di classi e sottoclassi e consentire la rappresentazione di strutture di dati molto più complesse rispetto all'uso dei *tipi semplici* (una classe che contiene solo membri primitivi).

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Modelli** > **Aggiungi** > **Nuovo elemento**.

1. Selezionare **Classe** e assegnare all'elemento il nome Hotel.cs. Sostituire il contenuto del file Hotel.cs con il codice seguente. Osservare i membri **Address** e **Room** della classe. Questi campi sono classi e pertanto saranno necessari modelli anche per loro.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

1. Ripetere la stessa procedura di creazione di un modello per la classe **Address**, assegnando al file il nome Address.cs. Sostituire il contenuto con quanto segue.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. Seguire di nuovo lo stesso processo per creare la classe **Room**, assegnando però al file il nome Room.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. L'ultimo modello che verrà creato in questa esercitazione è una classe denominata **SearchData**, che rappresenta l'input dell'utente (**searchText**) e l'output della ricerca (**resultList**). Il tipo di output, **SearchResults&lt;Hotel&gt;** , è importante poiché corrisponde esattamente ai risultati della ricerca ed è necessario passare questo riferimento alla visualizzazione. Sostituire il modello predefinito con il codice seguente.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Creare una pagina Web

I modelli di progetto includono diverse visualizzazioni client situate nella cartella **Views**. Le visualizzazioni esatte dipendono dalla versione di Core .NET in uso (nell'esempio viene usata la versione 3.1). Per questa esercitazione verrà modificato il file **Index.cshtml** per includere gli elementi di una pagina di ricerca.

Eliminare l'intero contenuto del file Index.cshtml e ricreare il file con questa procedura.

1. L'esercitazione usa due piccole immagini nella visualizzazione: un logo di Azure e l'icona di una lente di ingrandimento che rappresenta la ricerca (azure-logo.png e search.png). Copiare le immagini dal progetto GitHub alla cartella **wwwroot/images** del progetto.

1. La prima riga del file Index.cshtml deve fare riferimento al modello che verrà usato per comunicare i dati tra il client (la visualizzazione) e il server (il controller), ovvero il modello **SearchData** creato in precedenza. Aggiungere questa riga al file Index.cshtml.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. È prassi standard immettere un titolo per la vista, pertanto le righe successive devono essere:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Dopo il titolo, immettere un riferimento a un foglio di stile HTML, che verrà creato tra breve.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. Il corpo della visualizzazione gestisce due casi d'uso. Prima di tutto, deve fornire una pagina vuota al primo utilizzo, prima che venga immesso qualsiasi testo di ricerca. In secondo luogo, deve gestire i risultati, oltre alla casella di testo di ricerca, per le query ripetute.

   Per gestire entrambi i casi, è necessario verificare se il modello fornito alla visualizzazione è Null. Un modello Null indica il primo caso d'uso (l'esecuzione iniziale dell'app). Aggiungere il codice seguente al file Index.cshtml e leggere con attenzione i commenti.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Aggiungere il foglio di stile. In Visual Studio, in **File**> **Nuovo** > **File**, selezionare **Foglio di stile** (con l'opzione **Generale** evidenziata).

   Sostituire il codice predefinito con il codice seguente. Questo file non verrà esaminato in modo più dettagliato, poiché gli stili sono HTML standard.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Salvare il file del foglio di stile con il nome hotels.css nella cartella **wwwroot/css**, insieme al file site.css predefinito.

In questo modo la vista è stata completata. A questo punto, i modelli e le visualizzazioni sono completati. Per concludere manca solo il controller.

## <a name="define-methods"></a>Definire i metodi

In questo passaggio modificare il contenuto del **controller home**.

1. Aprire il file HomeController.cs e sostituire le istruzioni **using** con il codice seguente.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Aggiungere metodi Index

In un'app MVC il metodo **Index()** è un metodo di azione predefinito per qualsiasi controller. Apre la pagina HTML dell'indice. In questa esercitazione il metodo predefinito, che non accetta parametri, viene usato per il caso d'uso di avvio dell'applicazione, ovvero il rendering di una pagina di ricerca vuota.

In questa sezione il metodo viene esteso per supportare un secondo caso d'uso, ovvero il rendering della pagina quando un utente immette il testo di ricerca. Per supportare questo caso, il metodo Index viene esteso in modo da accettare un modello come parametro.

1. Aggiungere il metodo seguente dopo il metodo **Index()** predefinito.

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Osservare la dichiarazione **async** del metodo e la chiamata **await** a **RunQueryAsync**. Queste parole chiave fanno sì che le chiamate siano asincrone, evitando così thread di blocco nel server.

    Il blocco **catch** usa per impostazione predefinita il modello di errore creato.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Notare la gestione degli errori e altri metodi e viste predefiniti

A seconda della versione di .NET Core in uso, viene creato per impostazione predefinita un set di viste predefinite leggermente diverso. Per .NET Core 3.1 le visualizzazioni predefinite sono Indice, Privacy ed Errore. È possibile visualizzare queste pagine predefinite quando si esegue l'app ed esaminare come vengono gestite nel controller.

Più avanti in questa esercitazione verrà eseguito il test della visualizzazione Errore.

Nell'esempio di GitHub sono state eliminate le visualizzazioni inutilizzate e le azioni associate.

### <a name="add-the-runqueryasync-method"></a>Aggiungere il metodo RunQueryAsync

La chiamata di Ricerca cognitiva di Azure è incapsulata nel metodo **RunQueryAsync**.

1. Prima di tutto aggiungere alcune variabili statiche per configurare il servizio di Azure e una chiamata per avviarle.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Aggiungere ora il metodo **RunQueryAsync**.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    In questo metodo si verifica prima di tutto che la configurazione di Azure sia stata avviata e quindi si impostano alcune opzioni di ricerca. L'opzione **Select** specifica quali campi restituire nei risultati per trovare una corrispondenza con i nomi delle proprietà nella classe **hotel**. Se si omette **Select**, vengono restituiti tutti i campi non nascosti, il che può risultare poco efficiente se si è interessati solo a un sottoinsieme di tutti i campi possibili.

    La chiamata asincrona alla ricerca formula la richiesta (modellata come **searchText**) e la risposta (modellata come **searchResult**). Se si esegue il debug di questo codice, la classe **SearchResult** è un candidato valido per l'impostazione di un punto di interruzione, qualora sia necessario esaminare il contenuto di **model.resultList**. Questa soluzione si rivelerà intuitiva e consentirà di ottenere i dati richiesti.

### <a name="test-the-app"></a>Testare l'app

A questo punto, verificare se l'app viene eseguita correttamente.

1. Selezionare **Debug** > **Avvia senza eseguire debug** oppure premere **F5**. Se l'app viene eseguita come previsto, si dovrà ottenere la visualizzazione Indice iniziale.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Ricerca di *pool*" border="true":::

1. Immettere una stringa di query, ad esempio "beach" o un altro testo, quindi fare clic sull'icona di ricerca per inviare la richiesta.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Ricerca di *pool*" border="true":::

1. Provare a immettere "five star". Si noti che questa query non restituisce risultati. Una ricerca più sofisticata potrebbe considerare "five star" come un sinonimo di "luxury" e restituire risultati. Il supporto per i [sinonimi](search-synonyms.md) è disponibile in Ricerca cognitiva di Azure, ma non è trattato in questa serie di esercitazioni.

1. Provare a immettere "hot" come testo di ricerca. _Non_ restituirà voci contenenti la parola "hotel". La ricerca individua solo parole intere, anche se vengono restituiti alcuni risultati.

1. Provare con altre parole: "pool", "sunshine", "view" e così via. Sarà possibile osservare il funzionamento di Ricerca cognitiva di Azure al livello più semplice, ma comunque convincente.

## <a name="test-edge-conditions-and-errors"></a>Testare errori e condizioni limite

È importante verificare che le funzionalità di gestione degli errori abbiano il comportamento previsto, anche quando funziona tutto perfettamente. 

1. Nel metodo **Index**, dopo la chiamata **try {** , immettere la riga **Throw new Exception()** . Questa eccezione forzerà un errore quando si esegue una ricerca nel testo.

2. Eseguire l'app, immettere "bar" come testo di ricerca e fare clic sull'icona di ricerca. L'eccezione dovrebbe mostrare la vista di errore.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Ricerca di *pool*" border="true":::

    > [!Important]
    > Viene considerato un rischio per la sicurezza restituire i numeri di errori interni nelle pagine di errore. Se l'app è destinata a un uso generale, valutare le procedure di sicurezza e quelle consigliate su ciò che è opportuno restituire quando si verifica un errore.

3. Rimuovere **Throw new Exception()** quando si è soddisfatti del funzionamento della gestione degli errori.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* Una chiamata a Ricerca cognitiva di Azure è concisa e consente una facile interpretazione dei risultati.
* Le chiamate asincrone aggiungono una quantità ridotta di complessità al controller, ma sono la procedura consigliata se si prevede di sviluppare app di qualità.
* Questa app ha eseguito una ricerca di testo semplice, secondo quanto configurato in **searchOptions**. Questa classe tuttavia può essere completata con molti membri che aggiungono complessità a una ricerca. Non è necessario molto lavoro in più per potenziare questa app.

## <a name="next-steps"></a>Passaggi successivi

Per migliorare l'esperienza utente, aggiungere più funzionalità, in particolare la paginazione (tramite numeri di pagina o scorrimento infinito) e il completamento automatico o i suggerimenti. È anche opportuno valutare opzioni di ricerca più sofisticate, ad esempio ricerche geografiche basate sugli hotel situati entro un determinato raggio rispetto a un punto specificato e l'ordinamento dei risultati della ricerca.

Questi passaggi successivi sono illustrati nelle esercitazioni rimanenti. Si inizierà con l'impaginazione.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca cognitiva di Azure](tutorial-csharp-paging.md)