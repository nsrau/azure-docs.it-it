---
title: Creare funzioni permanenti con il portale di Azure
description: Informazioni su come installare l'estensione Durable Functions per Funzioni di Azure per lo sviluppo nel portale.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081917"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Creare funzioni permanenti con il portale di Azure

L'estensione [Funzioni permanenti](durable-functions-overview.md) di Funzioni di Azure è inclusa nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Questa estensione deve essere installata nell'app per le funzioni. Questo articolo illustra come installare questo pacchetto in modo da poter sviluppare funzioni permanenti nel portale di Azure.

> [!NOTE]
> 
> * Se si sviluppano funzioni Durable Functions in C#, considerare lo [sviluppo di Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Se si sviluppano funzioni permanenti in JavaScript, è consigliabile usare invece lo [sviluppo di Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione di qualsiasi funzione è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse. È possibile creare un'app .NET o JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Per impostazione predefinita, l'app per le funzioni creata usa la versione 2.x del runtime di Funzioni di Azure. L'estensione Durable Functions funziona in entrambe le versioni 1.x e 2.x del runtime di Funzioni di Azure in C# e nella versione 2.x in JavaScript. Tuttavia, i modelli sono disponibili solo se destinati alla versione 2.x del runtime, indipendentemente dal linguaggio scelto.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installare il pacchetto npm di Durable Functions (solo JavaScript)

Se si stanno creando funzioni Durable Functions in JavaScript, è necessario installare il [`durable-functions`pacchetto npm](https://www.npmjs.com/package/durable-functions):

1. Dalla pagina dell'app per le funzioni selezionare **Strumenti avanzati** in **Strumenti di sviluppo** nel riquadro a sinistra.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Funzionalità della piattaforma Funzioni, scegliere Kudu":::

2. Nella pagina **Strumenti avanzati** selezionare **Vai**.

3. Nella console Kudu selezionare **Debug console** (Console di debug) e quindi **CMD**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Funzionalità della piattaforma Funzioni, scegliere Kudu"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Funzionalità della piattaforma Funzioni, scegliere Kudu":::

4. Dopo aver caricato il file `package.json`, eseguire il comando `npm install` nella console di esecuzione remota Kudu.

   ![Esecuzione npm install in Kudu](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Creare una funzione di agente di orchestrazione

1. Nell'app per le funzioni selezionare **Funzioni** nel riquadro a sinistra e quindi **Aggiungi** nel menu superiore. 

1. Nel campo di ricerca della pagina **Nuova funzione** immettere `durable` e quindi scegliere il modello **Durable Functions Http Starter**.

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Funzionalità della piattaforma Funzioni, scegliere Kudu":::

1. Come nome di **Nuova funzione** immettere `HttpStart` e quindi selezionare **Crea funzione**.

   La funzione creata viene usata per avviare l'orchestrazione.

1. Creare un'altra funzione nell'app per le funzioni, questa volta usando il modello **Durable Functions Orchestrator**. Denominare la nuova funzione di orchestrazione `HelloSequence`.

1. Creare una terza funzione denominata `Hello` usando il modello **Durable Functions Activity**.

## <a name="test-the-durable-function-orchestration"></a>Testare l'orchestrazione della funzione permanente

1. Tornare alla funzione **HttpStart**, scegliere **Recupera URL della funzione** e selezionare l'icona **Copia negli Appunti** per copiare l'URL. Usare questo URL per avviare la funzione **HelloSequence**.

1. Usare uno strumento HTTP come Postman o cURL per inviare una richiesta POST all'URL copiato. L'esempio seguente è un comando cURL che invia una richiesta POST alla funzione permanente:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
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
> [Informazioni sui criteri di funzione permanente comuni](durable-functions-overview.md#application-patterns)
