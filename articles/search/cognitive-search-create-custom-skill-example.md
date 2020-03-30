---
title: Esempio di competenze personalizzate con l'API Ricerca entità BingCustom skill example using Bing Entity Search API
titleSuffix: Azure Cognitive Search
description: Viene illustrato l'utilizzo del servizio Ricerca entità Bing in una competenza personalizzata mappata a una pipeline di indicizzazione con arricchimento a livello di io proprietà in Ricerca cognitiva di Azure.Demonstrates using the Bing Entity Search service in a custom skill mapped to an AI-enriched indexing pipeline in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113811"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Esempio: creare una competenza personalizzata usando l'API Ricerca entità BingExample: Create a custom skill using the Bing Entity Search API

In questo esempio imparerai a creare una competenza personalizzata per l'API Web. Questa competenza accetta luoghi, personaggi pubblici e organizzazioni e restituirà le relative descrizioni. L'esempio usa una [funzione di Azure](https://azure.microsoft.com/services/functions/) per eseguire il wrapping dell'API Ricerca entità [Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) in modo che implementi l'interfaccia delle competenze personalizzata.

## <a name="prerequisites"></a>Prerequisiti

+ Leggere l'articolo [sull'interfaccia delle competenze personalizzate](cognitive-search-custom-skill-interface.md) se non si ha familiarità con l'interfaccia di input/output che deve implementare una competenza personalizzata.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Installare Visual Studio 2019 o versione successiva, incluso il carico di lavoro di sviluppo di Azure.Install [Visual Studio 2019](https://www.visualstudio.com/vs/) or later, including the Azure development workload.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Anche se questo esempio usa una funzione di Azure per ospitare un'API Web, non è obbligatorio.  Purché si rispettino i [requisiti di interfaccia per una competenza cognitiva](cognitive-search-custom-skill-interface.md), l'approccio adottato è irrilevante. Funzioni di Azure, tuttavia, rende la creazione di una competenza personalizzata estremamente semplice.

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

1. In Visual Studio selezionare Nuovo progetto dal menu File.In Visual Studio, select **New** > **Project** from the File menu.

1. Nella finestra di dialogo Nuovo progetto selezionare **Installato**, espandere **Visual C#** > **Cloud**, selezionare **Funzioni di Azure**, digitare un nome per il progetto e selezionare **OK**. Il nome dell'app per le funzioni deve essere valido come spazio dei nomi di C, pertanto non usare caratteri di sottolineatura, trattini o altri caratteri non alfanumerici.

1. Selezionare Funzioni di **Azure v2 (.NET Core)**. È anche possibile usare la versione 1, ma il codice riportato più avanti si basa sul modello della versione 2.

1. Selezionare il tipo **Trigger HTTP**

1. Per Account di archiviazione è possibile selezionare **Nessuno**, dato che per questa funzione non sarà necessario spazio di archiviazione.

1. Selezionare **OK** per creare il progetto di funzione e la funzione attivata da HTTP.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modificare il codice per chiamare il servizio ricerca entità BingModify the code to call the Bing Entity Search Service

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
    /// AI enrichment pipeline.
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

Assicurarsi di immettere il `key` proprio valore di chiave nella costante in base alla chiave ottenuta al momento dell'iscrizione all'API di ricerca di entità Bing.Make sure to enter your own *key* value in the constant based on the key you got when sign up for the Bing entity search API.

Questo esempio include tutto il codice necessario in un singolo file per comodità. È possibile trovare una versione leggermente più strutturata di quella stessa abilità nel [repository delle competenze di alimentazione.](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)

Naturalmente, è possibile rinominare `Function1.cs` `BingEntitySearch.cs`il file da a .

## <a name="test-the-function-from-visual-studio"></a>Testare la funzione da Visual Studio

Premere **F5** per eseguire il programma e testare i comportamenti della funzione. In questo caso, useremo la funzione qui sotto per cercare due entità. Usare Postman o Fiddler per eseguire una chiamata simile alla seguente:

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

Quando si è soddisfatti del comportamento della funzione, è possibile pubblicarlo.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Scegliere **Crea nuova** > **pubblicazione**.

1. Se non si è ancora connesso Visual Studio al proprio account di Azure, selezionare **Aggiungi un account**.

1. Seguire le istruzioni visualizzate sullo schermo. Viene chiesto di specificare un nome univoco per il servizio app, la sottoscrizione di Azure, il gruppo di risorse, il piano di hosting e l'account di archiviazione che si vuole usare. Se non sono già disponibili, è possibile creare un nuovo gruppo di risorse, un nuovo piano di hosting e un account di archiviazione. Al termine, selezionare **Crea**

1. Al termine della distribuzione, notare l'URL del sito. Questo è l'indirizzo dell'app per le funzioni in Azure. 

1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di `EntitySearch` risorse e cercare la funzione pubblicata. Nella sezione **Gestisci** dovrebbe essere presente un elenco Chiavi host. Selezionare l'icona **Copia** icona per la chiave host *predefinita*.  

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

Questo esempio dovrebbe produrre lo stesso risultato visto in precedenza durante l'esecuzione della funzione nell'ambiente locale.

## <a name="connect-to-your-pipeline"></a>Connettersi alla pipeline
A questo punto è possibile aggiungere la competenza personalizzata al proprio set di competenze. L'esempio seguente mostra come chiamare la competenza per aggiungere descrizioni alle organizzazioni nel documento (questo potrebbe essere esteso per lavorare anche su posizioni e persone). Sostituire `[your-entity-search-app-name]` con il nome dell'app.

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

Qui, contiamo sulla competenza predefinita di [riconoscimento](cognitive-search-skill-entity-recognition.md) delle entità per essere presenti nel set di competenze e per avere arricchito il documento con l'elenco delle organizzazioni. Per riferimento, ecco una configurazione di competenza per l'estrazione delle entità che sarebbe sufficiente per generare i dati necessari:For reference, here's an entity extraction skill configuration that would be sufficient in generating the data we need:

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
Congratulazioni! Hai creato la tua prima abilità personalizzata. Ora è possibile seguire lo stesso schema per aggiungere funzionalità personalizzate. Per ulteriori informazioni, fare clic sui collegamenti seguenti.

+ [Power Skills: un archivio di competenze personalizzate](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Aggiungere una competenza personalizzata a una pipeline di arricchimento AIAdd a custom skill to an AI enrichment pipeline](cognitive-search-custom-skill-interface.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
