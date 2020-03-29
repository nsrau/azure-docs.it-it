---
title: Chiamare le app per la logica con Funzioni di Azure
description: Creare funzioni di Azure che chiamano o attivano app per la logica ascoltando il bus di servizio di AzureCreate Azure functions that call or trigger logic apps by listening to Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428715"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Chiamare o attivare app di logica usando Funzioni di Azure e il bus di servizio di AzureCall or trigger logic apps by using Azure Functions and Azure Service Bus

È possibile usare Funzioni di [Azure](../azure-functions/functions-overview.md) per attivare un'app per la logica quando è necessario distribuire un listener o un'attività a esecuzione prolungata. Ad esempio, è possibile creare una funzione di Azure che rimane in ascolto in una coda [del bus](../service-bus-messaging/service-bus-messaging-overview.md) di servizio di Azure e avvia immediatamente un'app per la logica come trigger push.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Uno spazio dei nomi del bus di servizio di Azure.An Azure Service Bus namespace. Se non si dispone di uno spazio dei nomi, [creare prima lo spazio dei nomi](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Un'app per le funzioni di Azure, che è un contenitore per le funzioni di Azure.An Azure function app, which is a container for Azure functions. Se non si dispone di un'app per le funzioni, [creare prima l'app](../azure-functions/functions-create-first-azure-function.md)per le funzioni e assicurarsi di selezionare .NET come stack di runtime.

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Creare l'app per la logica

Per questo scenario, si dispone di una funzione che esegue ogni app per la logica che si desidera attivare. Creare innanzitutto un'app per la logica che inizia con un trigger di richiesta HTTP. La funzione chiamerà tale endpoint ogniqualvolta venga ricevuto un messaggio in coda.

1. Accedere al [portale di Azure](https://portal.azure.com) e creare un'app per la logica vuota.

   Se non si ha familiarità con le app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella casella di ricerca immettere `http request`. Nell'elenco dei trigger selezionare il trigger **Quando viene ricevuta una richiesta HTTP.**

   ![Selezionare il trigger](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Con il trigger di richiesta, è possibile immettere facoltativamente uno schema JSON da usare con il messaggio della coda. Gli schemi JSON consentono a Progettazione app per la logica di comprendere la struttura dei dati di input e di semplificare l'uso degli output nel flusso di lavoro.

1. Per specificare uno schema, immetterlo nella casella **Schema JSON del corpo della richiesta**, ad esempio:

   ![Specificare lo schema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se non si ha uno schema, ma si ha un payload di esempio in formato JSON, è possibile generare uno schema da tale payload.

   1. Nel trigger di richiesta selezionare **Usare il payload di esempio per generare lo schema**.

   1. In **Immettere o incollare un payload JSON di esempio,** immettere il payload di esempio e quindi selezionare **Fine**.

      ![Immettere il payload di esempio](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Il payload di esempio genera questo schema che viene visualizzato nel trigger:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Aggiungere eventuali altre azioni che si desidera eseguire dopo aver ricevuto il messaggio della coda.

   È ad esempio possibile inviare un messaggio di posta elettronica con il connettore Office 365 Outlook.

1. Salvare l'app per la logica in modo da generare l'URL di callback per il trigger nell'app per la logica. Successivamente, si usa questo URL di callback nel codice per il trigger della coda del bus di servizio di Azure.Later, you use this callback URL in the code for the Azure Service Bus Queue trigger.

   L'URL di callback viene visualizzato nella proprietà **URL HTTP POST.**

   ![URL di callback generato per il trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Creare la funzione di Azure

A questo punto creare la funzione che agisce come trigger e rimane in ascolto sulla coda.

1. Nel portale di Azure aprire ed espandere l'app per le funzioni, se non già aperta. 

1. Sotto il nome dell'app espandere **Funzioni**. Nel riquadro **Funzioni** selezionare **Nuova funzione**.

   ![Espandere "Funzioni" e selezionare "Nuova funzione"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selezionare questo modello in base al fatto che sia stata creata una nuova app per le funzioni in cui è stato selezionato .NET come stack di runtime o che si stia usando un'app per le funzioni esistente.

   * Per le nuove app per le funzioni, selezionare questo modello: **Trigger coda bus di servizioFor new function** apps, select this template: Service Bus Queue trigger

     ![Selezionare il modello per la nuova app per le funzioni](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Per un'app per le funzioni esistente, selezionare il modello seguente: **Trigger della coda del bus** di servizio -

     ![Selezionare il modello per l'app per le funzioni esistente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Nel riquadro **trigger della coda** del bus di servizio di Azure specificare un nome per il trigger e configurare la connessione del bus di **servizio** per la coda, che usa il listener SDK `OnMessageReceive()` del bus di servizio di Azure, quindi selezionare **Crea**.

1. Scrivere una funzione di base per chiamare l'endpoint dell'app per la logica creato in precedenza usando il messaggio della coda come trigger. Prima di scrivere la funzione, esaminare le considerazioni seguenti:Before you write your function, review these considerations:

   * Nell'esempio viene usato il tipo di contenuto di messaggio `application/json`, ma è possibile modificare questo tipo in base alle proprie esigenze.
   
   * A causa di possibili funzioni in esecuzione contemporanea, volumi elevati o carichi pesanti, evitare di creare un'istanza della [classe HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) con l'istruzione `using` e creare direttamente istanze HTTPClient per ogni richiesta. Per ulteriori informazioni, vedere [Usare HttpClientFactory per implementare richieste HTTP resilienti.](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)
   
   * Se possibile, riutilizzare l'istanza dei client HTTP. Per altre informazioni, vedere Gestire le connessioni in Funzioni di Azure.For more information, see [Manage connections in Azure Functions.](../azure-functions/manage-connections.md)

   In questo [ `Task.Run` ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) esempio viene utilizzato il metodo in modalità [asincrona.](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) Per ulteriori informazioni, vedere [Programmazione asincrona con async e await](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Per testare la funzione, aggiungere un messaggio della coda usando uno strumento, ad esempio [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   L'app per la logica viene attivata subito dopo che la funzione ha ricevuto il messaggio.

## <a name="next-steps"></a>Passaggi successivi

* [Chiamare, attivare o annidare flussi di lavoro utilizzando endpoint HTTPCall, trigger, or nest workflows by using HTTP endpoints](../logic-apps/logic-apps-http-endpoint.md)
