---
title: Chiamare app per la logica con funzioni di Azure-app per la logica di Azure
description: Creare funzioni di Azure che chiamano o attivano app per la logica ascoltando il bus di servizio di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 2ab6ace7c30c3dd385928b6b0ae8000485d5f495
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680154"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Chiamare o attivare app per la logica usando funzioni di Azure e il bus di servizio di Azure

È possibile usare [funzioni di Azure](../azure-functions/functions-overview.md) per attivare un'app per la logica quando è necessario distribuire un'attività o un listener con esecuzione prolungata. Ad esempio, è possibile creare una funzione di Azure che rimane in ascolto in una coda del [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) e genera immediatamente un'app per la logica come trigger di push.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Uno spazio dei nomi del bus di servizio di Azure. Se non si dispone di uno spazio dei nomi, [creare prima lo spazio dei nomi](../service-bus-messaging/service-bus-create-namespace-portal.md).

* App per le funzioni di Azure, un contenitore per funzioni di Azure. Se non si dispone di un'app per [le funzioni, creare prima l'app per le funzioni](../azure-functions/functions-create-first-azure-function.md)e assicurarsi di selezionare .NET come stack di Runtime.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Creare l'app per la logica

Per questo scenario, è presente una funzione che esegue ogni app per la logica che si vuole attivare. Prima di tutto, creare un'app per la logica che inizia con un trigger di richiesta HTTP. La funzione chiamerà tale endpoint ogniqualvolta venga ricevuto un messaggio in coda.  

1. Accedere al [portale di Azure](https://portal.azure.com) e creare un'app per la logica vuota.

   Se non si ha familiarità con le app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella casella di ricerca immettere "richiesta http". Nell'elenco dei trigger selezionare questo trigger: **Alla ricezione di una richiesta HTTP**

   ![Selezionare il trigger](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Con il trigger request è possibile immettere facoltativamente uno schema JSON da usare con il messaggio della coda. Gli schemi JSON consentono alla finestra di progettazione di app per la logica di comprendere la struttura per i dati di input e di semplificare l'uso di output nel flusso di lavoro.

1. Per specificare uno schema, immetterlo nella casella **Schema JSON del corpo della richiesta**, ad esempio:

   ![Specificare lo schema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se non si ha uno schema, ma si ha un payload di esempio in formato JSON, è possibile generare uno schema da tale payload.

   1. Nel trigger Richiesta selezionare **Usare il payload di esempio per generare lo schema**.

   1. In **Immettere o incollare un payload JSON di esempio** specificare il payload di esempio e quindi scegliere **Fine**.

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

1. Aggiungere eventuali altre azioni che si desidera eseguire dopo la ricezione del messaggio in coda.

   È ad esempio possibile inviare un messaggio di posta elettronica con il connettore Office 365 Outlook.

1. Salvare l'app per la logica in modo da generare l'URL di callback per il trigger nell'app per la logica. In seguito, questo URL di callback verrà usato nel codice per il trigger della coda del bus di servizio di Azure.

   L'URL di callback viene visualizzato nella proprietà **http post URL** .

   ![URL di callback generato per il trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Creare la funzione di Azure

A questo punto creare la funzione che agisce come trigger e rimane in ascolto sulla coda.

1. Nel portale di Azure aprire ed espandere l'app per le funzioni, se non già aperta. 

1. Sotto il nome dell'app espandere **Funzioni**. Nel riquadro **Funzioni** scegliere **Nuova funzione**.

   ![Espandere "funzioni" e scegliere "nuova funzione".](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Selezionare questo modello a seconda che sia stata creata una nuova app per le funzioni in cui è stato selezionato .NET come stack di runtime oppure si sta usando un'app per le funzioni esistente.

   * Per le nuove app per le funzioni, selezionare questo modello: **trigger della coda del bus di servizio**

     ![Selezionare il modello per la nuova app per le funzioni](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Per un'app per le funzioni esistente, selezionare questo modello: **trigger della coda C# del bus di servizio-**

     ![Selezionare il modello per l'app per le funzioni esistente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Nel riquadro **trigger della coda del bus di servizio di Azure** specificare un nome per il trigger e configurare la **connessione del bus di servizio** per la coda, che usa il listener del bus di servizio di Azure `OnMessageReceive()` e scegliere **Crea**.

1. Scrivere una funzione di base per chiamare l'endpoint dell'app per la logica creato in precedenza usando il messaggio della coda come trigger. Nell'esempio viene usato il tipo di contenuto di messaggio `application/json`, ma è possibile modificare questo tipo in base alle proprie esigenze. Se possibile, riutilizzare l'istanza di client HTTP. Per altre informazioni, vedere [Manage Connections in funzioni di Azure](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Per testare la funzione, aggiungere un messaggio della coda usando uno strumento, ad esempio [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   L'app per la logica viene attivata subito dopo che la funzione ha ricevuto il messaggio.

## <a name="next-steps"></a>Passaggi successivi

[Chiamare, attivare o annidare flussi di lavoro tramite endpoint HTTP](../logic-apps/logic-apps-http-endpoint.md)