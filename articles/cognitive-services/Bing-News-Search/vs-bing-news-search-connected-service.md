---
title: 'Esercitazione: Ricerca notizie Bing, C#'
titleSuffix: Azure Cognitive Services
description: Connettersi a Ricerca notizie Bing da un'applicazione Web ASP.NET Core.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: f1f5c590216975ce6b0813da6d9d98279d591454
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804345"
---
# <a name="tutorial-connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Esercitazione: Connettersi all'API Ricerca notizie Bing tramite Servizi connessi in Visual Studio

Usando la Ricerca notizie Bing, è possibile abilitare le app e i servizi per sfruttare la potenza di un motore di ricerca senza annunci con ambito Web. Ricerca notizie Bing è uno dei servizi di ricerca disponibili con Servizi cognitivi.

Questo articolo fornisce informazioni dettagliate per usare la funzionalità servizio connesso di Visual Studio per Ricerca notizie Bing. La funzionalità è disponibile in Visual Studio 2017 15.7 o versioni successive, con l'estensione Servizi cognitivi installata.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versione 15.7 con il carico di lavoro Sviluppo Web installato. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Aggiungere al progetto il supporto l'API Ricerca notizie Bing

1. Creare un nuovo progetto Web ASP.NET Core denominato MyWebApplication. Usare il modello di progetto **App Web (Model-View-Controller)** con tutte le impostazioni predefinite. È importante denominare il progetto MyWebApplication, in modo che quando si copia il codice nel progetto lo spazio dei nomi corrisponda. 

1. In **Esplora soluzioni** scegliere **Aggiungi** > **Servizio connesso**.
   Verrà visualizzata la pagina Servizio connesso con i servizi che è possibile aggiungere al progetto.

   ![Screenshot della voce di menu Aggiungi servizio connesso](../media/vs-common/Connected-Service-Menu.PNG)

1. Nel menu dei servizi disponibili, scegliere **Bring Intelligent Search To Your Apps** (Aggiungi ricerca intelligente alle applicazioni).

   ![Screenshot dell'elenco dei servizi connessi](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Se è stato effettuato l'accesso a Visual Studio e al proprio account è associata una sottoscrizione di Azure, viene visualizzata una pagina contenente un elenco a discesa con le sottoscrizioni. Selezionare la sottoscrizione che si intende usare e quindi scegliere un nome per l'API Ricerca notizie Bing. È anche possibile scegliere **Modifica** per modificare il nome generato automaticamente.

   ![Screenshot dei campi della sottoscrizione e dei nomi](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Scegliere il gruppo di risorse e il piano tariffario.

   ![Screenshot dei campi del gruppo di risorse e del piano tariffario](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Se si intende visualizzare ulteriori dettagli sui piani tariffari, selezionare **Review pricing** (Esamina prezzi).

1. Fare clic su **Aggiungi** per aggiungere supporto per il servizio connesso.
   Visual Studio modifica il progetto per aggiungere i pacchetti NuGet, le voci del file di configurazione e altre modifiche per supportare una connessione all'API Ricerca notizie Bing. L'output mostra il log delle operazioni eseguite nel progetto. Verrà visualizzata una schermata simile alla seguente:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Il file appsettings.json contiene ora le nuove impostazioni seguenti:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Usare l'API Ricerca notizie Bing per aggiungere una funzionalità di ricerca a una pagina Web

Ora che è stato aggiunto il supporto per l'API Ricerca notizie Bing al progetto, di seguito viene illustrato come usare l'API per aggiungere la funzionalità di ricerca intelligente a una pagina Web.

1.  In *Startup.cs*, nel metodo `ConfigureServices` aggiungere una chiamata a `IServiceCollection.AddSingleton`. In questo modo l'oggetto di configurazione che contiene le impostazioni delle chiavi diventa disponibile per il codice nel progetto.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Aggiungere un nuovo file di classe nella cartella **Modelli** denominato *BingNewsModel.cs*. Se il progetto ha un nome diverso, usare lo spazio dei nomi del progetto, anziché MyWebApplication. Sostituire il contenuto con il codice seguente:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Questo modello viene usato per archiviare i risultati di una chiamata al servizio Ricerca notizie Bing.
 
1. Nella cartella **Controller** aggiungere un nuovo file di classe denominato *IntelligentSearchController.cs*. Sostituire il contenuto con il codice seguente:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   In questo codice, il costruttore imposta l'oggetto di configurazione che contiene le chiavi. Il metodo per la route `Search` è semplicemente un reindirizzamento alla funzione `BingSearchResult`. Viene chiamato il metodo `GetNewsSearchClient` per ottenere l'oggetto client `NewsSearchAPI`.  L'oggetto client `NewsSearchAPI` contiene il metodo `SearchAsync` che chiama il servizio e restituisce i risultati nel modello `SearchResult` appena creato. 

1. Aggiungere una classe, `MyHandler`, a cui è stato fatto riferimento nel codice precedente. Questo delega la chiamata asincrona al servizio di ricerca alla classe di base, `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Per aggiungere il supporto per l'invio di ricerche e la visualizzazione dei risultati, nella cartella **Visualizzazioni** creare una nuova cartella denominata **IntelligentSearch**. In questa nuova cartella, aggiungere una visualizzazione *BingSearchResult.cshtml*. Copiare il codice seguente:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Avviare l'applicazione Web in locale, immettere l'URL della pagina appena creata (/IntelligentSearch/BingSearchResult) e inviare una richiesta di ricerca con il pulsante di ricerca.

   ![Screenshot dei risultati di Ricerca notizie Bing](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse non è più necessario, è possibile eliminarlo. In questo modo vengono eliminati il servizio cognitivo e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Scegliere il gruppo di risorse da eliminare.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **Digitare il nome del gruppo di risorse** immettere il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API ricerca notizie Bing, vedere le [informazioni relative a Ricerca notizie Bing](index.yml).
