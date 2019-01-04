---
title: 'Esempio: creare una competenza personalizzata nella pipeline di ricerca cognitiva - Ricerca di Azure'
description: Illustra come usare l'API Traduzione testuale in una competenza personalizzata mappata a una pipeline di indicizzazione di ricerca cognitiva in Ricerca di Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: d5bbdac74b0afa745993dd848ef73352d996e8b6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315061"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Esempio: creare una competenza personalizzata usando l'API Traduzione testuale

Questo esempio illustra come creare una competenza personalizzata in formato API Web che accetta il testo in qualsiasi lingua e lo traduce in inglese. L'esempio usa una [funzione di Azure](https://azure.microsoft.com/services/functions/) per eseguire il wrapping dell'[API Traduzione testuale](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) in modo che implementi l'interfaccia della competenza personalizzata.

## <a name="prerequisites"></a>Prerequisiti

+ Leggere l'articolo sull'[interfaccia per le competenze personalizzate](cognitive-search-custom-skill-interface.md) se non si ha familiarità con l'interfaccia di input/output che una competenza personalizzata deve implementare.

+ [Iscriversi per l'API Traduzione testuale](../cognitive-services/translator/translator-text-how-to-signup.md) e ottenere una chiave API per usarla.

+ [Installare Visual Studio 2017 versione 15.5 o successiva](https://www.visualstudio.com/vs/) con il carico di lavoro Sviluppo di Azure.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Questo esempio usa una funzione di Azure per ospitare un'API Web, tuttavia non si tratta di un requisito obbligatorio.  Purché si rispettino i [requisiti di interfaccia per una competenza cognitiva](cognitive-search-custom-skill-interface.md), l'approccio adottato è irrilevante. Funzioni di Azure, tuttavia, rende la creazione di una competenza personalizzata estremamente semplice.

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

1. In Visual Studio selezionare **Nuovo** > **Progetto** dal menu File.

1. Nella finestra di dialogo Nuovo progetto selezionare **Installato**, espandere **Visual C#** > **Cloud**, selezionare **Funzioni di Azure**, digitare un nome per il progetto e selezionare **OK**. Il nome dell'app per le funzioni deve essere valido come spazio dei nomi C#, quindi non usare caratteri di sottolineatura, trattini o altri caratteri non alfanumerici.

1. Selezionare **Funzioni di Azure v2 (.Net Core)**. È anche possibile usare la versione 1, ma il codice riportato più avanti si basa sul modello della versione 2.

1. Selezionare il tipo **Trigger HTTP**

1. Per Account di archiviazione è possibile selezionare **Nessuno**, dato che per questa funzione non sarà necessario spazio di archiviazione.

1. Selezionare **OK** per creare il progetto di funzione e la funzione attivata da HTTP.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Modificare il codice in modo da chiamare il servizio cognitivo di traduzione

Visual Studio crea un progetto e all'interno di esso una classe che contiene il codice boilerplate per il tipo di funzione scelto. L'attributo *FunctionName* nel metodo imposta il nome della funzione. L'attributo *HttpTrigger* specifica che la funzione è attivata da una richiesta HTTP.

A questo punto, sostituire tutto il contenuto del file *Function1.cs* con il codice seguente:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Assicurarsi di immettere nel metodo *TranslateText* il valore della *chiave* ottenuto in fase di iscrizione per l'API Traduzione testuale.

Questo esempio è un enricher semplice che funziona su un solo record per volta. Questo aspetto diventerà importante più avanti, al momento dell'impostazione della dimensione del batch per il set di competenze.

## <a name="test-the-function-from-visual-studio"></a>Testare la funzione da Visual Studio

Premere **F5** per eseguire il programma e testare i comportamenti della funzione. In questo caso viene usata la funzione seguente per tradurre un testo spagnolo in inglese. Usare Postman o Fiddler per eseguire una chiamata simile alla seguente:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Corpo della richiesta
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Risposta
La risposta dovrebbe essere simile all'esempio seguente:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Pubblicare la funzione in Azure

Quando si è soddisfatti del comportamento della funzione, è possibile pubblicarla.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Scegliere **Crea nuovo** > **Pubblica**.

1. Se non si è ancora connesso Visual Studio al proprio account di Azure, selezionare **Aggiungi un account**.

1. Seguire le istruzioni visualizzate sullo schermo. Viene chiesto di specificare l'account di Azure, il gruppo di risorse, il piano di hosting e l'account di archiviazione da usare. Se non sono già disponibili, è possibile creare un nuovo gruppo di risorse, un nuovo piano di hosting e un account di archiviazione. Al termine selezionare **Crea**

1. Al termine della distribuzione prendere nota dell'URL del sito. Questo è l'indirizzo dell'app per le funzioni in Azure. 

1. Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse e cercare la funzione di traduzione pubblicata. Nella sezione **Gestisci** dovrebbe essere presente un elenco Chiavi host. Selezionare l'icona **Copia** icona per la chiave host *predefinita*.  

## <a name="test-the-function-in-azure"></a>Testare la funzione in Azure

Ora che si dispone della chiave host predefinita, testare la funzione come segue:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Request Body
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Questo dovrebbe produrre un risultato simile a quello visualizzato in precedenza durante l'esecuzione della funzione nell'ambiente locale.

## <a name="connect-to-your-pipeline"></a>Connettersi alla pipeline
A questo punto è possibile aggiungere la competenza personalizzata al proprio set di competenze. L'esempio seguente mostra come chiamare la competenza. Poiché la competenza non gestisce i batch, aggiungere un'istruzione che indichi una dimensione massima dei batch pari a ```1```, per inviare i documenti uno alla volta.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! La creazione del primo enricher personalizzato è stata completata. Ora è possibile seguire lo stesso schema per aggiungere funzionalità personalizzate. 

+ [Aggiungere una competenza personalizzata a una pipeline di ricerca cognitiva](cognitive-search-custom-skill-interface.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
