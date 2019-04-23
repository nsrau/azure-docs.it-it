---
title: 'Esercitazione: Connettersi al servizio Analisi del testo con Servizi connessi in Visual Studio'
titleSuffix: Azure Cognitive Services
description: Informazioni su come connettersi ad Analisi del testo da un'applicazione Web ASP.NET Core.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4e1c03085d6b1d0099ac66dd3d1dadd981a561aa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004245"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Esercitazione: Connettersi al servizio Analisi del testo con Servizi connessi in Visual Studio

Grazie al servizio Analisi del testo è possibile estrarre informazioni complete per categorizzare ed elaborare i dati visuali ed eseguire la moderazione delle immagini basata su Machine Learning per aiutare a gestire i servizi.

Questo articolo e i relativi articoli correlati descrivono in dettaglio l'uso della funzionalità Servizio connesso di Visual Studio per il servizio Analisi del testo. La funzionalità è disponibile in Visual Studio 2017 15.7 o versioni successive, con l'estensione Servizi cognitivi installata.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versione 15.7 con il carico di lavoro Sviluppo Web installato. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Aggiungere il supporto al progetto per il servizio Analisi del testo

1. Creare un nuovo progetto Web ASP.NET Core denominato TextAnalyticsDemo. Usare il modello di progetto App Web (Model-View-Controller) con tutte le impostazioni predefinite. È importante denominare il progetto MyWebApplication, in modo che quando si copia il codice nel progetto lo spazio dei nomi corrisponda.  L'esempio in questo articolo usa MVC, ma è possibile usare il Servizio connesso Analisi del testo con qualsiasi tipo di progetto ASP.NET.

1. In **Esplora soluzioni** fare doppio clic sulla voce **Servizio connesso**.
   Verrà visualizzata la pagina Servizio connesso con i servizi che è possibile aggiungere al progetto.

   ![Screenshot di Servizio connesso in Esplora soluzioni](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Nel menu dei servizi disponibili scegliere **Evaluate Sentiment with Text Analytics** (Valutazione dei sentimenti con Analisi del testo).

   ![Screenshot della schermata Servizi connessi](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Se è stato effettuato l'accesso a Visual Studio e al proprio account è associata una sottoscrizione di Azure, viene visualizzata una pagina contenente un elenco a discesa con le sottoscrizioni.

   ![Screenshot della schermata Servizio connesso Analisi del testo](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Selezionare la sottoscrizione da usare e quindi scegliere un nome per il servizio Analisi del testo oppure scegliere il collegamento **Modifica** per cambiare il nome generato automaticamente, scegliere il gruppo di risorse e il piano tariffario.

   ![Screenshot dei campi del gruppo di risorse e del piano tariffario](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Seguire il collegamento per informazioni dettagliate sui piani tariffari.

1. Fare clic su **Aggiungi** per aggiungere supporto per il servizio connesso.
   Visual Studio modifica il progetto per aggiungere i pacchetti NuGet, le voci del file di configurazione e altre modifiche per supportare una connessione al servizio Analisi del testo. La **Finestra di output** visualizza il log che mostra le operazioni che vengono svolte nel progetto. Verrà visualizzata una schermata simile alla seguente:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Usare il servizio Analisi del testo per rilevare la lingua per un esempio di testo.

1. Aggiungere le istruzioni using seguenti nel file Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Aggiungere un campo di configurazione e successivamente un costruttore che inizializza il campo di configurazione nella classe Startup per abilitare la configurazione nel programma.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Aggiungere un file di classe nella cartella Controllers denominata DemoTextAnalyzeController e sostituirne il contenuto con il codice seguente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Il codice include GetTextAnalyzeClient per ottenere l'oggetto client che è possibile usare per richiamare l'API Analisi del testo e un gestore di richieste che richiama DetectLanguage su un testo specifico.

1. Aggiungere la classe helper MyHandler che viene usata dal codice precedente.

    ```csharp
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

1. Nella cartella Models aggiungere una classe per il modello.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Aggiungere una Vista per visualizzare il testo analizzato, la lingua determinata e il punteggio che rappresenta il livello di attendibilità nell'analisi. A tale scopo fare clic con il pulsante destro del mouse sulla cartella **Viste**, scegliere **Aggiungi**, quindi **Vista**. Nella finestra di dialogo che viene visualizzata specificare un nome _TextAnalyzeResult_, accettare le impostazioni predefinite per aggiungere un nuovo file denominato _TextAnalyzeResult.cshtml_ nella cartella **Viste** e copiarvi il contenuto seguente:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Compilare ed eseguire l'esempio in locale. Immettere testo e verificare quale lingua viene rilevata da Analisi del testo.
   
## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati il servizio cognitivo e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Selezionare il gruppo di risorse che viene visualizzato nei risultati della ricerca di questa esercitazione.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio Analisi del testo, vedere la [documentazione del servizio Analisi del testo](index.yml).
