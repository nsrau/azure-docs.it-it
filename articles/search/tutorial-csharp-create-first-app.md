---
title: Esercitazione C# per creare la prima app - Ricerca di Azure
description: Questa esercitazione offre una guida dettagliata per la creazione della prima app per Ricerca di Azure. L'esercitazione fornisce sia un collegamento a un'app funzionante su GitHub che il processo completo per creare l'app da zero. Informazioni sui componenti fondamentali di Ricerca di Azure.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d569437a3e6f6f05ddb9c6fa85f62c77ac51f72b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443808"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>Esercitazione per C#: Creare la prima app - Ricerca di Azure

Informazioni su come creare un'interfaccia Web per eseguire query e presentare i risultati della ricerca da un indice con Ricerca di Azure. Questa esercitazione inizia con un indice ospitato già esistente in modo da potersi concentrare sulla creazione di una pagina di ricerca. L'indice contiene dati di hotel fittizi. Dopo aver creato una pagina di base, è possibile migliorarla nelle lezioni successive e includere l'impaginazione, i facet e un'esperienza di completamento automatico.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare un ambiente di sviluppo
> * Modellare le strutture di dati
> * Creare una pagina Web
> * Definire i metodi
> * Testare l'app

Verrà illustrato inoltre quanto sia semplice una chiamata di ricerca. Le istruzioni principali nel codice che verrà sviluppato sono incapsulate nelle poche righe riportate di seguito.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Questa chiamata avvia una ricerca di dati di Azure e restituisce i risultati.

![Ricerca di "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

[Installare Visual Studio](https://visualstudio.microsoft.com/) da usare come IDE.

### <a name="install-and-run-the-project-from-github"></a>Installare ed eseguire il progetto da GitHub

1. Individuare in GitHub l'esempio per la [creazione della prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selezionare **Clone or download** (Clona o scarica) e creare una copia privata locale del progetto.
1. Usando Visual Studio, selezionare e aprire la soluzione per la pagina di ricerca di base e selezionare **Avvia senza eseguire debug** (o premere F5).
1. Digitare alcune parole, ad esempio "wifi", "view", "bar", "parking" ed esaminare i risultati.

    ![Ricerca di "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Il progetto non dovrebbe presentare problemi e l'app di Azure dovrebbe avviarsi. In questa app sono inclusi molti dei componenti essenziali per ricerche più sofisticate, pertanto è consigliabile esaminarla e ricrearla seguendo la procedura dettagliata.

Per creare il progetto da zero e quindi potenziare i componenti di Ricerca di Azure in base alle proprie esigenze, seguire queste procedure.

## <a name="set-up-a-development-environment"></a>Configurare un ambiente di sviluppo

1. In Visual Studio 2017 o versioni successive selezionare **Nuovo/Progetto** e quindi **Applicazione Web ASP.NET Core**. Assegnare un nome al progetto, ad esempio "FirstAzureSearchApp".

    ![Creazione di un progetto cloud](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Dopo aver fatto clic su **OK** per questo tipo di progetto, verrà visualizzato un secondo set di opzioni che si applicano a questo progetto. Selezionare **Applicazione Web (MVC)** .

    ![Creazione di un progetto MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Scegliere quindi **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi **Gestisci pacchetti NuGet per la soluzione**. Deve essere installato un pacchetto. Selezionare la scheda **Sfoglia** e quindi digitare "Ricerca di Azure" nella casella di ricerca. Installare **Microsoft.Azure.Search** quando è visibile nell'elenco (versione 9.0.1 o successive). Sarà necessario completare qualche altra finestra di dialogo per terminare l'installazione.

    ![Uso di NuGet per aggiungere librerie di Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Inizializzare Ricerca di Azure

Per questo esempio verranno usati dati di hotel disponibili pubblicamente. Questi dati costituiscono una raccolta arbitraria di 50 nomi di hotel fittizi con relative descrizioni, creati esclusivamente allo scopo di fornire i dati per la demo. Per accedere a questi dati, è necessario specificare un nome e la relativa chiave.

1. Aprire il file appsettings.json nel nuovo progetto e sostituire le righe predefinite con il nome e la chiave seguenti. La chiave API mostrata qui non è un esempio di chiave, è _esattamente_ la chiave necessaria per accedere ai dati dell'hotel. Il file appsettings.json ora dovrebbe essere simile al seguente.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Le operazioni con questo file non sono ancora terminate. Selezionare le proprietà per questo file e modificare l'impostazione **Copia nella directory di Output** impostando **Copia se più recente**.

    ![Copia delle impostazioni dell'app nell'output](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modellare le strutture di dati

I modelli (classi C#) vengono usati per comunicare i dati tra il client (la vista), il server (il controller) e anche il cloud di Azure con l'architettura MVC (Model-View-Controller). In genere questi modelli riflettono la struttura dei dati a cui si accede. È inoltre necessario un modello per gestire le comunicazioni vista/controller.

1. Aprire la cartella **Modelli** del progetto con Esplora soluzioni. Nella cartella è presente un modello predefinito: **ErrorViewModel.cs**.

2. Fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi **Nuovo elemento**. Nella finestra di dialogo visualizzata selezionare quindi **ASP.NET Core** e infine la prima opzione **Classe**. Rinominare il file con estensione cs in Hotel.cs e fare clic su **Aggiungi**. Sostituire il contenuto del file Hotel.cs con il codice seguente. Osservare i membri **Address** e **Room** della classe. Questi campi sono classi e pertanto saranno necessari modelli anche per essi.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Seguire lo stesso processo di creazione di un modello per la classe **Address**, ma assegnare al file il nome Address.cs. Sostituire il contenuto con quanto segue.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. Seguire di nuovo lo stesso processo per creare la classe **Room**, assegnando però al file il nome Room.cs. Anche in questo caso, sostituire il contenuto con quanto segue.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. Il set di classi **Hotel**, **Address** e **Room** costituisce ciò che in Azure è noto come [_tipi complessi_](search-howto-complex-data-types.md), un'importante funzionalità di Ricerca di Azure. I tipi complessi possono includere molti livelli al di sotto di classi e sottoclassi e consentire la rappresentazione di strutture di dati molto più complesse rispetto all'uso dei _tipi semplici_ (una classe che contiene solo membri primitivi). Poiché è necessario ancora un modello, seguire di nuovo la procedura di creazione di una nuova classe di modello, assegnando però questa volta alla classe il nome SearchData.cs e sostituendo il codice predefinito con il codice seguente.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    La classe contiene l'input dell'utente (**searchText**) e l'output della ricerca (**resultList**). Il tipo di output, **DocumentSearchResult&lt;Hotel&gt;** , è importante poiché questo tipo corrisponde esattamente ai risultati della ricerca ed è necessario passare questo riferimento alla vista.



## <a name="create-a-web-page"></a>Creare una pagina Web

Il progetto definito creerà per impostazione predefinita un numero di viste client. Il numero esatto di viste dipende dalla versione di Core .NET in uso (nell'esempio viene usata la versione 2.1). Sono tutte presenti nella cartella **Viste** del progetto. Sarà necessario solo modificare il file Index.cshtml (nella cartella **Viste/Home**).

Eliminare l'intero contenuto del file Index.cshtml e ricreare il file con questa procedura.

1. Vengono usate due immagini di piccole dimensioni nella vista. È possibile usare le proprie immagini o copiarle dal progetto GitHub: azure-logo.png e search.png. Queste due immagini devono essere posizionate nella cartella **wwwroot/images**.

2. La prima riga del file Index.cshtml deve fare riferimento al modello che verrà usato per comunicare i dati tra il client (la vista) e il server (il controller), ovvero il modello **SearchData** che è stato creato. Aggiungere questa riga al file Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. È prassi standard immettere un titolo per la vista, pertanto le righe successive devono essere:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Dopo il titolo, immettere un riferimento a un foglio di stile HTML, che verrà creato a breve.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Si passerà ora alla parte sostanziale della vista. Un fattore chiave da ricordare è che la vista deve gestire due situazioni. In primo luogo, deve gestire la visualizzazione quando l'app viene avviata per la prima volta e l'utente non ha ancora immesso alcun testo di ricerca. In secondo luogo, deve gestire la visualizzazione dei risultati, oltre alla casella di testo di ricerca, per consentire l'uso ripetuto da parte dell'utente. Per gestire queste due situazioni, è necessario controllare se il modello fornito alla vista è Null. Un modello Null indica la prima delle due situazioni, ovvero l'esecuzione iniziale dell'app. Aggiungere il codice seguente al file Index.cshtml e leggere con attenzione i commenti.

    ```cs
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
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Viene infine aggiunto il foglio di stile. In Visual Studio scegliere **Nuovo/File** dal menu **File** e quindi **Foglio di stile** (con la voce **Generale** evidenziata). Sostituire il codice predefinito con il codice seguente. Questo file non verrà esaminato in modo più dettagliato, poiché gli stili sono HTML standard.

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

7. Salvare il file del foglio di stile come hotels.css nella cartella wwwroot/css, insieme al file site.css predefinito.

In questo modo la vista è stata completata. La procedura è quasi ultimata. I modelli e le viste sono stati completati. Per concludere manca solo il controller.

## <a name="define-methods"></a>Definire i metodi

È necessario modificare il contenuto del controller (**Home Controller**), che viene creato per impostazione predefinita.

1. Aprire il file HomeController.cs e sostituire le istruzioni **using** con il codice seguente.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Aggiungere metodi Index

Sono necessari due metodi **Index**, uno senza parametri (per lo scenario in cui l'app viene aperta per la prima volta) e uno che usa un modello come parametro (per lo scenario in cui l'utente ha immesso testo di ricerca). Il primo di questi metodi viene creato per impostazione predefinita. 

1. Aggiungere il metodo seguente dopo il metodo **Index()** predefinito.

    ```cs
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

                // Make the Azure Search call.
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

    Il blocco **catch** usa per impostazione predefinita il modello di errore che è stato creato automaticamente.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Notare la gestione degli errori e altri metodi e viste predefiniti

A seconda della versione di .NET Core in uso, viene creato per impostazione predefinita un set di viste predefinite leggermente diverso. Per .NET Core 2.1, le viste predefinite sono Indice, Informazioni, Contatto, Privacy ed Errore. Per .NET Core 2.2 invece le viste predefinite sono ad esempio Indice, Privacy ed Errore. In entrambi i casi è possibile visualizzare queste pagine predefinite quando si esegue l'app ed esaminare come vengono gestite nel controller.

Più avanti in questa esercitazione verrà eseguito il test della vista Errore.

Nell'esempio di GitHub sono state eliminate le viste non usate e le azioni associate.

### <a name="add-the-runqueryasync-method"></a>Aggiungere il metodo RunQueryAsync

La chiamata di Ricerca di Azure è incapsulata nel metodo **RunQueryAsync**.

1. Prima di tutto aggiungere alcune variabili statiche per configurare il servizio di Azure e una chiamata per avviarle.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Aggiungere ora il metodo **RunQueryAsync**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    In questo metodo si verificherà prima di tutto che la configurazione di Azure sia avviata e quindi si imposteranno alcuni parametri di ricerca. I nomi dei campi nel parametro **Select** corrispondono esattamente ai nomi di proprietà nella classe **hotel**. È possibile tralasciare il parametro **Select** e in questo caso verranno restituite tutte le proprietà. Tuttavia, tralasciare il parametro **Select** non è una soluzione efficiente se si è interessati solo a un subset dei dati. Specificando le proprietà a cui si è interessati, verranno restituite solo tali proprietà.

    Questa esercitazione e l'app sono incentrate esclusivamente sulla chiamata asincrona per la ricerca (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** ). La classe **DocumentSearchResult** è interessante ed è una buona idea impostare qui un punto di interruzione quando l'app è in esecuzione e usare un debugger per esaminare il contenuto di **model.resultList**. Questa soluzione si rivelerà intuitiva e consentirà di ottenere i dati richiesti.

Ora è il momento cruciale.

### <a name="test-the-app"></a>Testare l'app

A questo punto è possibile verificare se l'app viene eseguita correttamente.

1. Selezionare **Debug/Avvia senza eseguire debug** o premere F5. Se il codice è stato scritto correttamente, verrà visualizzata la vista iniziale Indice.

     ![Apertura dell'app](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Immettere il testo di ricerca, ad esempio "beach" o un altro testo, quindi fare clic sull'icona di ricerca. Dovrebbero essere visualizzati alcuni risultati.

     ![Ricerca di "beach"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Provare a immettere "five star". Non si otterrà alcun risultato. Una ricerca più sofisticata potrebbe considerare "five star" come un sinonimo di "luxury" e restituire risultati. L'uso dei sinonimi è disponibile in Ricerca di Azure, anche se non verrà trattato nelle esercitazioni iniziali.
 
4. Provare a immettere "hot" come testo di ricerca. _Non_ restituirà voci contenenti la parola "hotel". La ricerca individua solo parole intere, anche se vengono restituiti alcuni risultati.

5. Provare con altre parole: "pool", "sunshine", "view" e così via. Sarà possibile osservare il funzionamento di Ricerca di Azure al livello più semplice, ma comunque convincente.

## <a name="test-edge-conditions-and-errors"></a>Testare errori e condizioni limite

È importante verificare che le funzionalità di gestione degli errori abbiano il comportamento previsto, anche quando funziona tutto perfettamente. 

1. Nel metodo **Index**, dopo la chiamata **try {** , immettere la riga **Throw new Exception()** . Questa eccezione forzerà un errore quando si esegue una ricerca nel testo.

2. Eseguire l'app, immettere "bar" come testo di ricerca e fare clic sull'icona di ricerca. L'eccezione dovrebbe mostrare la vista di errore.

     ![Forzare un errore](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Viene considerato un rischio per la sicurezza restituire i numeri di errori interni nelle pagine di errore. Se l'app è destinata a un uso generale, valutare le procedure di sicurezza e quelle consigliate su ciò che è opportuno restituire quando si verifica un errore.

3. Rimuovere **Throw new Exception()** quando si è soddisfatti del funzionamento della gestione degli errori.

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti estrapolate da questo progetto:

* Una chiamata a Ricerca di Azure è concisa e consente una facile interpretazione dei risultati.
* Le chiamate asincrone aggiungono una quantità ridotta di complessità al controller, ma sono la procedura consigliata se si prevede di sviluppare app di qualità.
* Questa app ha eseguito una ricerca di testo semplice, secondo quanto configurato in **searchParameters**. Questa classe tuttavia può essere completata con molti membri che aggiungono complessità a una ricerca. Non è necessario molto lavoro in più per potenziare questa app.

## <a name="next-steps"></a>Passaggi successivi

Per garantire la migliore esperienza utente tramite Ricerca di Azure, è necessario aggiungere altre funzionalità, in particolare l'impaginazione (usando numeri di pagina o lo scorrimento infinito) e il completamento automatico o i suggerimenti. È opportuno inoltre prendere in considerazione parametri di ricerca più sofisticati, ad esempio ricerche geografiche basate sugli alberghi entro un determinato raggio rispetto a un punto specificato e l'ordinamento dei risultati della ricerca.

Questi passaggi successivi sono illustrati in una serie di esercitazioni. Si inizierà con l'impaginazione.

> [!div class="nextstepaction"]
> [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca di Azure](tutorial-csharp-paging.md)


