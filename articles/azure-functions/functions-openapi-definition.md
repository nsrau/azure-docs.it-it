---
title: Creare una definizione OpenAPI per un'API serverless con Gestione API di Azure
description: Creare una definizione OpenAPI che consente ad altri servizi e app di chiamare la funzione in Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 659c05b3d31f5673e95cb27f10eaa8bd872e4be6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226812"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Creare una definizione OpenAPI per un'API serverless con Gestione API di Azure

Le API REST vengono spesso descritte usando una definizione OpenAPI. Questa definizione contiene informazioni sulle operazioni disponibili in un'API e su come devono essere strutturati i dati della richiesta e della risposta per l'API.

In questa esercitazione si crea una funzione che determina se una riparazione urgente di una turbina eolica è conveniente. Si crea quindi una definizione OpenAPI per l'app per le funzioni usando [Gestione API di Azure](../api-management/api-management-key-concepts.md), in modo che la funzione possa essere chiamata da altri servizi e app.

In questa esercitazione si apprenderà come:

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

La funzione calcola quindi il costo della riparazione e i ricavi che la turbina potrebbe consentire in un periodo di 24 ore. Per creare la funzione attivata tramite HTTP nel [portale di Azure](https://portal.azure.com), seguire questa procedura.

1. Espandere l'app per le funzioni e selezionare il pulsante **+** accanto a **Funzioni**. Fare clic su **Nel portale** > **Continua**.

1. Scegliere **Altri modelli** e quindi **Termina e visualizza i modelli**.

1. Selezionare Trigger HTTP, digitare `TurbineRepair` nel campo **Nome** della funzione, scegliere `Function` per **[Livello autenticazione](functions-bindings-http-webhook.md#http-auth)** e quindi fare clic su **Crea**.  

    ![Creare la funzione HTTP per OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Sostituire il contenuto del file di script C# run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r "Newtonsoft.Json"
    
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

    Il codice di questa funzione restituisce un messaggio `Yes` o `No` per indicare se una riparazione urgente è conveniente, oltre che l'opportunità di ricavi offerta dalla turbina e il costo per riparare la turbina.

1. Per testare la funzione, fare clic su **Test** a destra per espandere la scheda Test. Immettere il valore seguente per **Corpo della richiesta** e quindi fare clic su **Esegui**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testare la funzione nel portale di Azure](media/functions-openapi-definition/test-function.png)

    Nel corpo della risposta viene restituito il valore seguente.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

A questo punto si ha una funzione che determina la convenienza delle riparazioni urgenti. Si genera quindi una definizione OpenAPI per l'app per le funzioni.

## <a name="generate-the-openapi-definition"></a>Generare la definizione OpenAPI

A questo punto si è pronti per generare la definizione OpenAPI.

1. Selezionare l'app per le funzioni, quindi in **Funzionalità della piattaforma** scegliere **Gestione API** e selezionare **Crea nuovo** in **Gestione API**.

    ![Scegliere Gestione API in Funzionalità della piattaforma](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Usare le impostazioni di Gestione API specificate nella tabella sotto l'immagine.

    ![Creare un nuovo servizio di Gestione API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente univoco | Viene generato un nome basato sul nome dell'app per le funzioni. |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova risorsa. |  
    | **[Gruppo di risorse](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Stessa risorsa dell'app per le funzioni, che dovrebbe essere impostata automaticamente. |
    | **Posizione** | Stati Uniti occidentali | Scegliere l'area Stati Uniti occidentali. |
    | **Nome organizzazione** | Contoso | Nome dell'organizzazione usato nel portale per sviluppatori e per le notifiche di posta elettronica. |
    | **Indirizzo di posta elettronica dell'amministratore** | Indirizzo di posta elettronica in uso | Indirizzo di posta elettronica che ha ricevuto le notifiche di sistema da Gestione API. |
    | **Piano tariffario** | Consumo (anteprima) | Il piano Consumo è in anteprima e non è disponibile in tutte le aree. Per informazioni dettagliate sui prezzi, vedere la [pagina Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/). |

1. Scegliere **Crea** per creare l'istanza di Gestione API. Questa operazione potrebbe richiedere alcuni minuti.

1. Selezionare **Abilita Application Insights** per inviare i log alla stessa posizione dell'applicazione per le funzioni, quindi accettare le impostazioni predefinite rimanenti e fare clic su **Collega API**.

1. Viene visualizzata la schermata **Importa funzioni di Azure**, in cui risulta evidenziata la funzione **TurbineRepair**. Scegliere **Seleziona** per continuare.

    ![Importare funzioni di Azure in Gestione API](media/functions-openapi-definition/import-function-openapi.png)

1. Nella pagina **Crea da app per le funzioni** accettare le impostazioni predefinite e scegliere **Crea**.

    ![Crea da app per le funzioni](media/functions-openapi-definition/create-function-openapi.png)

L'API è stata così creata per la funzione.

## <a name="test-the-api"></a>Testare l'API

Prima di usare la definizione OpenAPI, è necessario verificare il funzionamento dell'API.

1. Nella scheda **Test** della funzione selezionare l'operazione **POST**.

1. Immettere i valori per le ore (**hours**) e la capacità (**capacity**).

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Fare clic su **Invia**e quindi visualizzare la risposta HTTP.

    ![Testare l'API della funzione](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Scaricare la definizione OpenAPI

Se l'API funziona come previsto, è possibile scaricare la definizione OpenAPI.

1. Selezionare **Scarica definizione OpenAPI** nella parte superiore della pagina.
   
   ![Scaricare la definizione OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Aprire il file JSON scaricato ed esaminare la definizione.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Per generare una definizione OpenAPI delle funzioni, è stata usata l'integrazione di Gestione API. È ora possibile modificare la definizione in Gestione API nel portale. Sono anche disponibili [altre informazioni su Gestione API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Modificare la definizione OpenAPI in Gestione API](../api-management/edit-api.md)
