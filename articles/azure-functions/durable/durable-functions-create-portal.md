---
title: Creare funzioni permanenti con il portale di Azure
description: Informazioni su come installare l'estensione Durable Functions per Funzioni di Azure per lo sviluppo nel portale.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637006"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Creare funzioni permanenti con il portale di Azure

L'estensione [Funzioni permanenti](durable-functions-overview.md) di Funzioni di Azure è inclusa nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Questa estensione deve essere installata nell'app per le funzioni. Questo articolo illustra come installare questo pacchetto in modo da poter sviluppare funzioni permanenti nel portale di Azure.

>[!NOTE]
>
>* Se si sviluppano funzioni permanenti in C#, è consigliabile usare invece lo [sviluppo di Visual Studio 2017](durable-functions-create-first-csharp.md).
* Se si sviluppano funzioni permanenti in JavaScript, è consigliabile usare invece lo **sviluppo di Visual Studio Code**.
>
>La creazione di funzioni permanenti in JavaScript non è ancora supportata nel portale. Usare invece Visual Studio Code.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione di qualsiasi funzione è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. È necessario creare un'app per le funzioni in C#, poiché i modelli di JavaScript non sono ancora supportati per Durable Functions.  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Per impostazione predefinita, l'app per le funzioni creata usa la versione 2.x del runtime di Funzioni di Azure. L'estensione Durable Functions funziona in entrambe le versioni 1.x e 2.x del runtime di Funzioni di Azure. Tuttavia, i modelli sono disponibili solo se destinati alla versione 2.x del runtime.

## <a name="create-an-orchestrator-function"></a>Creare una funzione di agente di orchestrazione

1. Espandere l'app per le funzioni e fare clic sul pulsante **+** accanto a **Funzioni**. Se questa è la prima funzione nell'app per le funzioni, selezionare **Nel portale** e quindi **Continua**. In caso contrario, andare direttamente al passaggio 3.

   ![Pagina della guida introduttiva di Funzioni nel portale di Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Scegliere **Altri modelli** e quindi **Termina e visualizza i modelli**.

    ![Selezione di altri modelli nella guida introduttiva di Funzioni](./media/durable-functions-create-portal/add-first-function.png)

1. Nel campo di ricerca, digitare `durable` e scegliere il modello **Durable Functions HTTP starter**.

1. Se richiesto, selezionare **Installa** per installare l'estensione Azure DurableTask e le relative dipendenze nell'app per le funzioni. È sufficiente installare l'estensione una volta per una determinata app per le funzioni. Al termine dell'installazione, selezionare **Continua**.

    ![Installare le estensioni di binding](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Al termine dell'installazione, denominare la nuova funzione `HttpStart` e scegliere **Create**. La funzione creata viene usata per avviare l'orchestrazione.

1. Creare un'altra funzione nell'app per le funzioni, questa volta usando il modello **Durable Functions Orchestrator**. Denominare la nuova funzione di orchestrazione `HelloSequence`.

1. Creare una terza funzione denominata `Hello` usando il modello **Durable Functions Activity**.

## <a name="test-the-durable-function-orchestration"></a>Testare l'orchestrazione della funzione permanente

1. Tornare alla funzione **HttpStart**, scegliere **</> Get function URL** e **Copiare** l'URL. Usare questo URL per avviare la funzione **HelloSequence**.

1. Usare uno strumento HTTP come Postman o cURL per inviare una richiesta POST all'URL copiato. L'esempio seguente è un comando cURL che invia una richiesta POST alla funzione permanente:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    In questo esempio, `{your-function-app-name}` corrisponde al dominio che equivale al nome dell'app per le funzioni. Il messaggio di risposta contiene un set di endpoint URI che è possibile usare per monitorare e gestire l'esecuzione, con un aspetto simile al seguente:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Chiamando l'URI dell'endpoint `statusQueryGetUri` si visualizza lo stato corrente della funzione permanente, che potrebbe essere simile al seguente esempio:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continuare a chiamare l'endpoint `statusQueryGetUri` fino a quando lo stato diventa **Completed** per visualizzare una risposta simile all'esempio seguente: 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

La prima funzione permanente è ora attiva e in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sui criteri di funzione permanente comuni](durable-functions-overview.md)