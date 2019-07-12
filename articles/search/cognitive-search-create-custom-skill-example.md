---
title: "Esempio: Creazione di una competenza cognitiva personalizzata con l'API ricerca entità Bing - ricerca di Azure"
description: Viene illustrato come utilizzare il servizio di ricerca entità Bing in una competenza personalizzata eseguito il mapping a una pipeline di indicizzazione di ricerca cognitiva in ricerca di Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672152"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Esempio: Creare una competenza personalizzata usando l'API Bing Entity Search

In questo esempio, informazioni su come creare una competenza personalizzato di API web. Questa abilità verrà accettare posizioni, figure pubblici e organizzazioni e restituire le descrizioni per loro. L'esempio Usa un' [funzione di Azure](https://azure.microsoft.com/services/functions/) per eseguire il wrapping il [API Bing Entity Search](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) in modo che implementi l'interfaccia di competenze personalizzate.

## <a name="prerequisites"></a>Prerequisiti

+ Conoscenza [interfaccia di competenze personalizzate](cognitive-search-custom-skill-interface.md) articolo se non si ha familiarità con l'interfaccia di input/output che deve implementare una competenza personalizzata.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Installare [Visual Studio 2019](https://www.visualstudio.com/vs/) o versione successiva, compreso il carico di lavoro di sviluppo di Azure.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Sebbene questo esempio Usa una funzione di Azure per ospitare un'API web, non è obbligatorio.  Purché si rispettino i [requisiti di interfaccia per una competenza cognitiva](cognitive-search-custom-skill-interface.md), l'approccio adottato è irrilevante. Funzioni di Azure, tuttavia, rende la creazione di una competenza personalizzata estremamente semplice.

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

1. In Visual Studio selezionare **Nuovo** > **Progetto** dal menu File.

1. Nella finestra di dialogo Nuovo progetto selezionare **Installato**, espandere **Visual C#**  > **Cloud**, selezionare **Funzioni di Azure**, digitare un nome per il progetto e selezionare **OK**. Il nome dell'app (funzione) deve essere valido come un C# spazio dei nomi, in modo da non usare caratteri di sottolineatura, trattini o altri caratteri non alfanumerici.

1. Selezionare **funzioni di Azure v2 (.NET Core)** . È anche possibile usare la versione 1, ma il codice riportato più avanti si basa sul modello della versione 2.

1. Selezionare il tipo **Trigger HTTP**

1. Per Account di archiviazione è possibile selezionare **Nessuno**, dato che per questa funzione non sarà necessario spazio di archiviazione.

1. Selezionare **OK** per creare il progetto di funzione e la funzione attivata da HTTP.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modificare il codice per chiamare il servizio di ricerca entità Bing

Visual Studio crea un progetto e all'interno di esso una classe che contiene il codice boilerplate per il tipo di funzione scelto. L'attributo *FunctionName* nel metodo imposta il nome della funzione. L'attributo *HttpTrigger* specifica che la funzione è attivata da una richiesta HTTP.

A questo punto, sostituire tutto il contenuto del file *Function1.cs* con il codice seguente:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Assicurarsi di immettere il proprio *key* valore il `key` costante in base alla chiave è stato ottenuto durante l'iscrizione per l'API Bing entity search.

In questo esempio include tutto il codice necessario in un singolo file per motivi di praticità. È possibile trovare una versione leggermente più strutturata di tale competenze nella stessa [nel repository di competenze power](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Naturalmente, è possibile rinominare il file dalla `Function1.cs` a `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>Testare la funzione da Visual Studio

Premere **F5** per eseguire il programma e testare i comportamenti della funzione. In questo caso, si userà la funzione seguente per cercare le due entità. Usare Postman o Fiddler per eseguire una chiamata simile alla seguente:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Corpo della richiesta
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Pubblicare la funzione in Azure

Quando si è soddisfatti con il comportamento della funzione, è possibile pubblicarlo.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Scegliere **Crea nuovo** > **Pubblica**.

1. Se non si è ancora connesso Visual Studio al proprio account di Azure, selezionare **Aggiungi un account**.

1. Seguire le istruzioni visualizzate sullo schermo. Viene richiesto di specificare un nome univoco per il servizio app, la sottoscrizione di Azure, il gruppo di risorse, il piano di hosting e account di archiviazione da usare. Se non sono già disponibili, è possibile creare un nuovo gruppo di risorse, un nuovo piano di hosting e un account di archiviazione. Al termine selezionare **Crea**

1. Dopo aver completato la distribuzione, si noti che l'URL del sito. Questo è l'indirizzo dell'app per le funzioni in Azure. 

1. Nel [portale di Azure](https://portal.azure.com), passare al gruppo di risorse e cercare il `EntitySearch` funzione è stata pubblicata. Nella sezione **Gestisci** dovrebbe essere presente un elenco Chiavi host. Selezionare l'icona **Copia** icona per la chiave host *predefinita*.  

## <a name="test-the-function-in-azure"></a>Testare la funzione in Azure

Ora che si dispone della chiave host predefinita, testare la funzione come segue:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Request Body
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

In questo esempio deve produrre lo stesso risultato che si è visto in precedenza, quando si esegue la funzione nell'ambiente locale.

## <a name="connect-to-your-pipeline"></a>Connettersi alla pipeline
A questo punto è possibile aggiungere la competenza personalizzata al proprio set di competenze. L'esempio seguente viene illustrato come chiamare le competenze per aggiungere descrizioni per le organizzazioni del documento (ciò può essere estesa per poterle utilizzare anche in posizioni e le persone). Sostituire `[your-entity-search-app-name]` con il nome dell'app.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

In questo caso, abbiamo stiamo il conteggio predefinito [competenza di riconoscimento di entità](cognitive-search-skill-entity-recognition.md) siano presenti dell'insieme di competenze e migliorato il documento con l'elenco delle organizzazioni. Per riferimento, ecco una configurazione di competenza estrazione di entità che sarebbe sufficiente per generare i dati che necessari:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! È stato creato il primo enricher personalizzato. Ora è possibile seguire lo stesso schema per aggiungere funzionalità personalizzate. 

+ [Aggiungere una competenza personalizzata a una pipeline di ricerca cognitiva](cognitive-search-custom-skill-interface.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
