---
title: Esporre le funzioni con OpenAPI usando Gestione API di Azure
description: Creare una definizione OpenAPI che consente ad altri servizi e app di chiamare la funzione in Azure.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212905"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Creare una definizione OpenAPI per un'API serverless con Gestione API di Azure

Le API REST vengono spesso descritte usando una definizione OpenAPI. Questa definizione contiene informazioni sulle operazioni disponibili in un'API e su come devono essere strutturati i dati della richiesta e della risposta per l'API.

In questa esercitazione si crea una funzione che determina se una riparazione urgente di una turbina eolica è conveniente. Si crea quindi una definizione OpenAPI per l'app per le funzioni usando [Gestione API di Azure](../api-management/api-management-key-concepts.md), in modo che la funzione possa essere chiamata da altri servizi e app.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una funzione in Azure
> * Generare una definizione OpenAPI usando Gestione API di Azure
> * Testare la definizione chiamando la funzione
> * Scaricare la definizione OpenAPI

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Creare la funzione

Questa esercitazione usa una funzione attivata tramite HTTP che accetta due parametri:

* Il tempo stimato, in ore, per effettuare una riparazione della turbina.
* La capacità della turbina in kilowatt. 

La funzione calcola quindi il costo della riparazione e i ricavi che la turbina potrebbe consentire in un periodo di 24 ore. Per creare la funzione attivata tramite HTTP nel [portale di Azure](https://portal.azure.com):

1. Selezionare **Funzioni** nel menu a sinistra dell'app per le funzioni e quindi **Aggiungi** nel menu in alto.

1. Nella finestra **Nuova funzione** selezionare **Trigger HTTP**.

1. In **Nuova funzione** immettere `TurbineRepair`. 

1. Scegliere **Funzione** nell'elenco a discesa **[Livello di autorizzazione](functions-bindings-http-webhook-trigger.md#http-auth)** e quindi selezionare **Crea funzione**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="Creare la funzione HTTP per OpenAPI&quot;:::

1. Selezionare **Codice e test** e quindi **run.csx** nell'elenco a discesa. Sostituire il contenuto del file di script C# run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r &quot;Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Questo codice di funzione restituisce un messaggio `Yes` o `No` per indicare se una riparazione urgente è economicamente conveniente. Restituisce inoltre l'opportunità di ricavi offerta dalla turbina e il costo della riparazione.

1. Per testare la funzione, selezionare **Test** e quindi la scheda **Input**, immettere l'input seguente in **Corpo** e infine selezionare **Esegui**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Creare la funzione HTTP per OpenAPI&quot;:::

1. Selezionare **Codice e test** e quindi **run.csx** nell'elenco a discesa. Sostituire il contenuto del file di script C# run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r &quot;Newtonsoft.Json":::

    Nella scheda **Output** verrà restituito l'output seguente:

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

A questo punto si ha una funzione che determina la convenienza delle riparazioni urgenti. Si genera quindi una definizione OpenAPI per l'app per le funzioni.

## <a name="generate-the-openapi-definition"></a>Generare la definizione OpenAPI

Per generare la definizione OpenAPI:

1. Selezionare l'app per le funzioni, scegliere **Gestione API** nel menu a sinistra e quindi selezionare **Crea nuova** in **Gestione API**.

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="Creare la funzione HTTP per OpenAPI&quot;:::

1. Selezionare **Codice e test** e quindi **run.csx** nell'elenco a discesa. Sostituire il contenuto del file di script C# run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r &quot;Newtonsoft.Json":::


1. Usare le impostazioni di Gestione API specificate nella tabella seguente:

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente univoco | Viene generato un nome basato sul nome dell'app per le funzioni. |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova risorsa. |  
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Stessa risorsa dell'app per le funzioni, che dovrebbe essere impostata automaticamente. |
    | **Posizione** | Stati Uniti occidentali | Scegliere l'area Stati Uniti occidentali. |
    | **Nome organizzazione** | Contoso | Nome dell'organizzazione usato nel portale per sviluppatori e per le notifiche di posta elettronica. |
    | **Indirizzo di posta elettronica dell'amministratore** | Indirizzo di posta elettronica in uso | Indirizzo di posta elettronica che ha ricevuto le notifiche di sistema da Gestione API. |
    | **Piano tariffario** | Consumo | Il livello Consumo non è disponibile in tutte le aree. Per informazioni dettagliate sui prezzi, vedere la [pagina Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/). |

    ![Creare un nuovo servizio di Gestione API](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Scegliere **Crea** per creare l'istanza di Gestione API. Questa operazione potrebbe richiedere alcuni minuti.

1. Dopo aver creato l'istanza, Azure abilita l'opzione **Abilita Application Insights** nella pagina. Selezionarla per inviare i log alla stessa posizione dell'applicazione per le funzioni e quindi selezionare **Collega API**.

1. Viene visualizzata la schermata **Importa funzioni di Azure**, in cui risulta evidenziata la funzione **TurbineRepair**. Scegliere **Seleziona** per continuare.

    ![Importare funzioni di Azure in Gestione API](media/functions-openapi-definition/import-function-openapi.png)

1. Nella pagina **Crea da app per le funzioni** accettare le impostazioni predefinite e quindi selezionare **Crea**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Creare la funzione HTTP per OpenAPI&quot;:::

1. Selezionare **Codice e test** e quindi **run.csx** nell'elenco a discesa. Sostituire il contenuto del file di script C# run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r &quot;Newtonsoft.Json":::

    Azure creerà l'API per la funzione.

## <a name="test-the-api"></a>Testare l'API

Prima di usare la definizione OpenAPI, è necessario verificare il funzionamento dell'API.

1. Nella pagina dell'app per le funzioni selezionare **Gestione API**, quindi la scheda **Test** e infine **POST TurbineRepair**. 

1. Immettere il codice seguente in **Corpo della richiesta**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Selezionare **Invia** e quindi visualizzare **Risposta HTTP**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Creare la funzione HTTP per OpenAPI&quot;:::

1. Selezionare **Codice e test** e quindi **run.csx** nell'elenco a discesa. Sostituire il contenuto del file di script C# run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r &quot;Newtonsoft.Json":::

## <a name="download-the-openapi-definition"></a>Scaricare la definizione OpenAPI

Se l'API funziona come previsto, è possibile scaricare la definizione OpenAPI.

1. Selezionare **Scarica definizione OpenAPI** nella parte superiore della pagina.
   
   ![Scaricare la definizione OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Salvare il file JSON scaricato e quindi aprirlo. Esaminare la definizione.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Per generare una definizione OpenAPI delle funzioni, è stata usata l'integrazione di Gestione API. È ora possibile modificare la definizione in Gestione API nel portale. Sono anche disponibili [altre informazioni su Gestione API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Modificare la definizione OpenAPI in Gestione API](../api-management/edit-api.md)
