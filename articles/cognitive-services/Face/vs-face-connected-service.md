---
title: 'Esercitazione: API Viso, C#'
titleSuffix: Azure Cognitive Services
description: Creare un'app di Windows che usa l'API Viso di Servizi cognitivi per rilevare le caratteristiche dei volti in un'immagine.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: ghogen
ms.openlocfilehash: 4b204b9895a2afea4c78d1d92f2cca68f77ae708
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970296"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Connessione all'API Viso di Servizi cognitivi tramite Servizi connessi in Visual Studio

L'API Viso di Servizi cognitivi consente di rilevare, analizzare, organizzare e taggare i visi nelle foto.

Questo articolo e i relativi articoli correlati descrivono in dettaglio l'uso della funzionalità Servizio connesso di Visual Studio per l'API Viso di Servizi cognitivi. La funzionalità è disponibile in Visual Studio 2017 15.7 o versioni successive, con l'estensione Servizi cognitivi installata.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 15.7 o versioni successive con il carico di lavoro **Sviluppo Web** installato. [Scaricarla qui](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Creare un progetto e aggiungere il supporto per l'API Viso di Servizi cognitivi

1. Creare un nuovo progetto Web ASP.NET Core. Usare il modello di progetto Vuoto. 

1. In **Esplora soluzioni** scegliere **Aggiungi** > **Servizio connesso**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.

   ![Aggiungere la voce di menu Servizio connesso](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Nel menu dei servizi disponibili scegliere **Cognitive Services Face API** (API Viso di Servizi cognitivi).

   ![Scegliere il servizio a cui connettersi](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Se è stato effettuato l'accesso a Visual Studio e al proprio account è associata una sottoscrizione di Azure, viene visualizzata una pagina contenente un elenco a discesa con le sottoscrizioni.

   ![Selezionare la propria sottoscrizione](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Selezionare la sottoscrizione da usare e quindi scegliere un nome per l'API Viso oppure scegliere il collegamento Modifica per cambiare il nome generato automaticamente, scegliere il gruppo di risorse e il piano tariffario.

   ![Modificare i dettagli del servizio connesso](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Seguire il collegamento per informazioni dettagliate sui piani tariffari.

1. Fare clic su Aggiungi per aggiungere supporto per il servizio connesso.
   Visual Studio modifica il progetto per aggiungere i pacchetti NuGet, le voci del file di configurazione e altre modifiche per supportare una connessione all'API Viso.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Usare l'API Viso per rilevare gli attributi dei visi in un'immagine

1. Aggiungere le istruzioni using seguenti nel file Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Aggiungere un campo di configurazione e successivamente un costruttore che inizializza il campo di configurazione nella classe Startup per abilitare la configurazione nel programma.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Nella cartella wwwroot del progetto aggiungere una cartella immagini e salvarvi un file di immagine. Si può ad esempio usare una delle immagini in questa [pagina di API Viso](https://azure.microsoft.com/services/cognitive-services/face/). Fare clic con il pulsante destro del mouse su una delle immagini, salvarla nel disco rigido locale, quindi in Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella delle immagini e scegliere **Aggiungi** > **Elemento esistente** per aggiungerla al progetto. Il progetto in Esplora soluzioni avrà un aspetto simile al seguente:
 
   ![Cartella delle immagini con file di immagine](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Fare clic sul file di immagine, scegliere Proprietà e quindi scegliere **Copia se più recente**.

   ![Copia se più recente](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Sostituire il metodo Configure con il codice seguente per accedere all'API Viso e testare un'immagine. Modificare la stringa imagePath con il percorso e il nome file corretti dell'immagine del viso.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Il codice in questo passaggio crea una richiesta HTTP con una chiamata all'API REST Viso, usando la chiave che è stata aggiunta quando è stato aggiunto il servizio connesso.

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

1. Eseguire l'applicazione Web e vedere i risultati dell'analisi dell'immagine da parte dell'API Viso.
 
   ![Immagine di API Viso e risultati formattati](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati il servizio cognitivo e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
1. Selezionare **Elimina gruppo di risorse**.
1. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API Viso, vedere la [documentazione dell'API Viso](Overview.md).
