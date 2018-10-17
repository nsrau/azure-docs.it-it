---
title: 'Esercitazione: Visione artificiale in C#'
titleSuffix: Azure Cognitive Services
description: Connettersi all'API Visione artificiale da un'applicazione Web ASP.NET Core.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: Tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: ebfcabdea1e83a83af5eea8025ba5a411c3f9880
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077951"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Usare Servizi connessi in Visual Studio per connettersi all'API Visione artificiale

Grazie all'API Visione artificiale di Servizi cognitivi è possibile estrarre informazioni complete per categorizzare ed elaborare i dati visuali ed eseguire la moderazione delle immagini basata su Machine Learning per aiutare a gestire i servizi.

Questo articolo e i relativi articoli correlati descrivono in dettaglio l'uso di Servizi connessi di Visual Studio per l'API Visione artificiale di Servizi cognitivi. La funzionalità è disponibile in Visual Studio 2017 15.7 o versioni successive, con l'estensione Servizi cognitivi installata.

## <a name="prerequisites"></a>Prerequisiti

- **Una sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 versione 15.7** con il carico di lavoro **Sviluppo Web** installato. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Aggiungere il supporto al progetto per l'API Visione artificiale di Servizi cognitivi

1. Creare un nuovo progetto Web ASP.NET Core. Usare il modello di progetto Vuoto. 

1. In **Esplora soluzioni** scegliere **Aggiungi** > **Servizio connesso**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.

   ![Aggiungere la voce di menu Servizio connesso](../media/vs-common/Connected-Service-Menu.PNG)

1. Nel menu dei servizi disponibili scegliere **API Visione artificiale di Servizi cognitivi**.

   ![Scegliere il servizio a cui connettersi](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Se è stato effettuato l'accesso a Visual Studio e al proprio account è associata una sottoscrizione di Azure, viene visualizzata una pagina contenente un elenco a discesa con le sottoscrizioni.

   ![Selezionare la propria sottoscrizione](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Selezionare la sottoscrizione da usare e quindi scegliere un nome per l'API Visione artificiale oppure scegliere il collegamento di modifica per cambiare il nome generato automaticamente, scegliere il gruppo di risorse e il piano tariffario.

   ![Modificare i dettagli del servizio connesso](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Seguire il collegamento per informazioni dettagliate sui piani tariffari.

1. Fare clic su Aggiungi per aggiungere supporto per il servizio connesso.
   Visual Studio modifica il progetto per aggiungere i pacchetti NuGet, le voci del file di configurazione e altre modifiche per supportare una connessione all'API Visione artificiale. La Finestra di output visualizza il log che mostra le operazioni che vengono svolte nel progetto. Verrà visualizzata una schermata simile alla seguente:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Usare l'API Visione artificiale per rilevare gli attributi di un'immagine

1. Aggiungere le istruzioni using seguenti nel file Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Aggiungere un campo di configurazione e successivamente un costruttore che inizializza il campo di configurazione nella classe `Startup` per abilitare la configurazione nel programma.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Nella cartella wwwroot del progetto aggiungere una cartella immagini e salvarvi un file di immagine. Si può ad esempio usare una delle immagini in questa [pagina dell'API Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Fare clic con il pulsante destro del mouse su una delle immagini, salvarla nel disco rigido locale, quindi in Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella delle immagini e scegliere **Aggiungi** > **Elemento esistente** per aggiungerla al progetto. Il progetto in Esplora soluzioni avrà un aspetto simile al seguente: 
  
   ![Cartella delle immagini con file di immagine](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Fare clic sul file di immagine, scegliere Proprietà e quindi scegliere **Copia se più recente**. 

   ![Copia se più recente](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Sostituire il metodo Configure con il codice seguente per accedere all'API Visione artificiale e testare un'immagine.

   ```csharp
        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site. 
            string imagePath = @"images/subway.png";

            // Enable static files such as image files. 
            app.UseStaticFiles();

            string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
            string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

            // Assemble the URI for the REST API Call.
            string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts an image you've added to your site's images folder. 
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentString = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentString = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
                await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentString));
                await context.Response.WriteAsync("<p>");
            });
        }

   ```
    Qui il codice crea una richiesta HTTP con l'URI e l'immagine come contenuto binario per una chiamata a all'API REST di Visione artificiale.

1. Aggiungere le funzioni helper GetImageAsByteArray e JsonPrettyPrint.

   ```csharp
        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            string INDENT_STRING = "    ";
            var indent = 0;
            var quoted = false;
            var sb = new StringBuilder();
            for (var i = 0; i < json.Length; i++)
            {
                var ch = json[i];
                switch (ch)
                {
                    case '{':
                    case '[':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case '}':
                    case ']':
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        sb.Append(ch);
                        break;
                    case '"':
                        sb.Append(ch);
                        bool escaped = false;
                        var index = i;
                        while (index > 0 && json[--index] == '\\')
                            escaped = !escaped;
                        if (!escaped)
                            quoted = !quoted;
                        break;
                    case ',':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case ':':
                        sb.Append(ch);
                        if (!quoted)
                            sb.Append(" ");
                        break;
                    default:
                        sb.Append(ch);
                        break;
                }
            }
            return sb.ToString();
        }
   ```

1. Eseguire l'applicazione Web e vedere cosa ha rilevato l'API Visione artificiale nell'immagine.

   ![Immagine di API Visione artificiale e risultati formattati](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati il servizio cognitivo e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questa guida introduttiva, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API Visione artificiale, vedere la [documentazione dell'API Visione artificiale](Home.md).
