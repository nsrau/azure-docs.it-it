---
title: Creare una definizione OpenAPI per una funzione | Microsoft Docs
description: Creare una definizione OpenAPI che consente ad altri servizi e app di chiamare la funzione in Azure.
services: functions
keywords: OpenAPI, Swagger, app cloud, servizi cloud
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2017
ms.author: mblythe; glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 5aed57e69924f03221fc0d9909889358d80df9a4
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---

# <a name="create-an-openapi-definition-for-a-function"></a>Creare una definizione OpenAPI per una funzione
Le API REST vengono spesso descritte usando una definizione OpenAPI (detta in precedenza file [Swagger](http://swagger.io/)). Questa definizione contiene informazioni sulle operazioni disponibili in un'API e su come devono essere strutturati i dati della richiesta e della risposta per l'API.

In questa esercitazione si crea una funzione che determina se una riparazione urgente di una turbina eolica è conveniente. Si crea quindi una definizione OpenAPI per l'app per le funzioni, in modo che sia possibile chiamare la funzione da altri servizi e app.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una funzione in Azure
> * Generare una definizione OpenAPI usando gli strumenti OpenAPI
> * Modificare la definizione per fornire metadati aggiuntivi
> * Testare la definizione chiamando la funzione

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]


## <a name="create-the-function"></a>Creare la funzione

Questa esercitazione usa una funzione attivata tramite HTTP che accetta due parametri: il tempo stimato per la riparazione della turbina (in ore) e la capacità della turbina (in chilowatt). La funzione calcola quindi il costo della riparazione e i ricavi che la turbina potrebbe consentire in un periodo di 24 ore.

1. Espandere l'app per le funzioni, fare clic sul pulsante **+** accanto a **Funzioni** e fare clic sul modello **HTTPTrigger**. Immettere `TurbineRepair` come valore di **Nome** per la funzione e fare clic su **Crea**.

    ![Pannello App per le funzioni, Funzioni +](media/functions-openapi-definition/add-function.png)

1. Sostituire il contenuto del file run.csx con il codice seguente e quindi fare clic su **Salva**:

    ```c#
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
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

A questo punto si ha una funzione che determina la convenienza delle riparazioni urgenti. Si genera e si modifica quindi una definizione OpenAPI per l'app per le funzioni.

## <a name="generate-the-openapi-definition"></a>Generare la definizione OpenAPI

A questo punto si è pronti per generare la definizione OpenAPI. Questa definizione può essere usata da altre tecnologie Microsoft, ad esempio App per le API, [PowerApps](functions-powerapps-scenario.md) e [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), oltre che da strumenti di sviluppo di terze parti, come [Postman](https://www.getpostman.com/docs/importing_swagger) e [molti altri pacchetti](http://swagger.io/tools/).

1. Selezionare solo i *verbi* supportati dall'API (in questo caso POST). In questo modo, la definizione API generata sarà più chiara.

    1. Nella scheda **Integrazione** della nuova funzione trigger HTTP modificare **Metodi HTTP consentiti** in **Metodi selezionati**

    1. In **Metodi HTTP selezionati** deselezionare tutte le opzioni ad eccezione di **POST**.

        ![Metodi HTTP selezionati](media/functions-openapi-definition/selected-http-methods.png)
        
1. Fare clic sul nome dell'app per le funzioni (ad esempio **function-demo-energy**) > **Funzionalità della piattaforma** > **Definizione API**.

    ![Definizione API](media/functions-openapi-definition/api-definition.png)

1. Nella scheda **Definizione API** fare clic su **Funzione**.

    ![Origine della definizione API](media/functions-openapi-definition/api-definition-source.png)

    Questo passaggio abilita una suite di opzioni OpenAPI per l'app per le funzioni, tra cui un endpoint per ospitare un file OpenAPI dal dominio dell'app per le funzioni, una copia inline dell'[editor OpenAPI](http://editor.swagger.io) e un generatore di modelli di definizioni API.

1. Fare clic su **Genera modello di definizione dell'API** > **Salva**.

    ![Generare il modello di definizione dell'API](media/functions-openapi-definition/generate-template.png)

    Azure analizza l'app per le funzioni alla ricerca delle funzioni trigger HTTP e usa le informazioni contenute in functions.json per generare una definizione OpenAPI. Ecco la definizione generata:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Questa definizione è descritta come _modello_ perché richiede più metadati per essere una definizione OpenAPI completa. Nel passaggio successivo si modificherà la definizione.

## <a name="modify-the-openapi-definition"></a>Modificare la definizione OpenAPI
Ora che si ha una definizione modello, si apportano modifiche per fornire metadati aggiuntivi sulle operazioni e sulle strutture dei dati dell'API. In **Definizione API** eliminare la definizione generata da `post` fino alla parte inferiore della definizione, incollare il contenuto seguente e quindi fare clic su **Salva**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

In questo caso è sufficiente incollare metadati aggiornati, ma è importante comprendere i tipi di modifiche apportate al modello predefinito::

+ Si specifica che l'API produce e utilizza dati in un formato JSON.

+ Si specificano i parametri obbligatori, con i relativi nomi e tipi di dati.

+ Si specificano i valori restituiti per una risposta con esito positivo, con i relativi nomi e tipi di dati.

+ Si forniscono descrizioni e riepiloghi descrittivi per l'API, con i relativi parametri e operazioni. Questo aspetto è importante per le persone che useranno questa funzione.

+ Si aggiungono le proprietà x-ms-summary e x-ms-visibility, usate nell'interfaccia utente per Microsoft Flow e App per la logica. Per altre informazioni, vedere [Estensioni OpenAPI per API personalizzate in Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> La definizione di sicurezza è stata lasciata con il metodo di autenticazione predefinito della chiave API. Se si usasse un tipo diverso di autenticazione, sarebbe necessario modificare questa sezione della definizione.

Per altre informazioni sulla definizione delle operazioni dell'API, vedere la [specifica OpenAPI](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Testare la definizione OpenAPI
Prima di usare la definizione API, è consigliabile testarla nell'interfaccia utente di Funzioni di Azure.

1. Nella scheda **Gestisci** della funzione, in **Chiavi host**, copiare la chiave **predefinita**.

    ![Copiare la chiave API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Questa chiave viene usata a scopo di test e la si usa anche quando si chiama l'API da un'app o un servizio.

1. Tornare alla definizione API: **function-demo-energy** > **Funzionalità della piattaforma** > **Definizione API**.

1. Nel riquadro destro fare clic su **Modifica autenticazione**, immettere la chiave API copiata e fare clic su **Esegui autenticazione**.

    ![Eseguire l'autenticazione con la chiave API](media/functions-openapi-definition/authenticate-api-key.png)

1. Scorrere verso il basso e fare clic su **Try this operation** (Prova operazione).

    ![Provare l'operazione](media/functions-openapi-definition/try-operation.png)

1. Immettere i valori per **ore** e **capacità**.

    ![Immettere i parametri](media/functions-openapi-definition/parameters.png)

    Si noti il modo in cui l'interfaccia utente usa le descrizioni dalla definizione API.

1. Fare clic su **Send a request** (Invia una richiesta) e quindi sulla scheda **Pretty** (Formattato) per visualizzare l'output.

    ![Inviare una richiesta](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una funzione in Azure
> * Generare una definizione OpenAPI usando gli strumenti OpenAPI
> * Modificare la definizione per fornire metadati aggiuntivi
> * Testare la definizione chiamando la funzione

Passare all'argomento successivo per imparare a creare un'app di PowerApps che usa la definizione OpenAPI creata.
> [!div class="nextstepaction"]
> [Chiamare una funzione da PowerApps](functions-powerapps-scenario.md)

