---
title: Chiamare o avviare le App per la logica con funzioni di Azure e Bus di servizio di Azure
description: Creare funzioni di Azure che chiamano o attivano App per la logica usando il Bus di servizio di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494895"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Chiamare o avviare le App per la logica con funzioni di Azure e Bus di servizio di Azure

È possibile usare [funzioni di Azure](../azure-functions/functions-overview.md) per attivare un'app per la logica quando è necessario distribuire un listener con esecuzione prolungata o attività. Ad esempio, è possibile creare una funzione di Azure che è in ascolto su un [Bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) della coda e viene attivato immediatamente un'app per la logica come trigger di push.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Uno spazio dei nomi del Bus di servizio di Azure. Se non si dispone di uno spazio dei nomi [creare prima di tutto lo spazio dei nomi](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Un'app funzioni di Azure, che è un contenitore per le funzioni di Azure. Se non si dispone di un'app per le funzioni, [creare prima l'app per le funzioni](../azure-functions/functions-create-first-azure-function.md)e assicurarsi di selezionare .NET come lo stack di runtime.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Creare l'app per la logica

Per questo scenario, disponibile una funzione che esegue ogni app per la logica che si desidera attivare. Innanzitutto, creare un'app per la logica che inizia con un trigger di richiesta HTTP. La funzione chiamerà tale endpoint ogniqualvolta venga ricevuto un messaggio in coda.  

1. Accedere al [portale di Azure](https://portal.azure.com) e creare un'app per la logica vuota.

   Se si ha familiarità con App per la logica, esaminare [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella casella di ricerca immettere "richiesta http". Nell'elenco di trigger selezionare questo trigger: **Quando viene ricevuta una richiesta HTTP**

   ![Selezionare il trigger](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Con il trigger di richiesta, è possibile immettere uno schema JSON da usare con il messaggio della coda. Gli schemi JSON consentono di progettazione di App per la logica di comprendere la struttura per i dati di input e rendere più facile da usare nel flusso di lavoro gli output.

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

1. Aggiungere tutte le altre azioni da eseguire dopo aver ricevuto il messaggio della coda.

   È ad esempio possibile inviare un messaggio di posta elettronica con il connettore Office 365 Outlook.

1. Salvare l'app per la logica in modo da generare l'URL di callback per il trigger nell'app per la logica. In un secondo momento, usare questo URL di callback nel codice per il trigger di coda del Bus di servizio Azure.

   Il callback URL verrà visualizzato il **URL POST HTTP** proprietà.

   ![URL di callback generato per il trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Creare la funzione di Azure

A questo punto creare la funzione che agisce come trigger e rimane in ascolto sulla coda.

1. Nel portale di Azure aprire ed espandere l'app per le funzioni, se non già aperta. 

1. Sotto il nome dell'app espandere **Funzioni**. Nel riquadro **Funzioni** scegliere **Nuova funzione**.

   ![Espandere "Funzioni" e scegliere "Nuova funzione"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Selezionare questo modello basato su se è stato creato una nuovo app per le funzioni in cui .NET è stato selezionato come lo stack di runtime, o si usa un'app per le funzioni esistente.

   * Per la nuova App per le funzioni, selezionare questo modello: **Trigger della coda del Bus di servizio**

     ![Selezionare il modello per la nuova app per le funzioni](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Per un'app per le funzioni esistente, selezionare questo modello: **Trigger di coda del Bus di servizio:C#**

     ![Selezionare il modello per app per le funzioni esistente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Nel **trigger coda Bus di servizio di Azure** riquadro, specificare un nome per il trigger e impostare il **connessione del Bus di servizio** per la coda, che usa il SDK del Bus di servizio di Azure `OnMessageReceive()` listener e scegliere **Creare**.

1. Scrivere una semplice funzione per chiamare l'endpoint dell'app per la logica creata in precedenza usando il messaggio della coda come trigger. Nell'esempio viene usato il tipo di contenuto di messaggio `application/json`, ma è possibile modificare questo tipo in base alle proprie esigenze. Se possibile, riusare l'istanza di client HTTP. Per altre informazioni, vedere [gestire le connessioni in funzioni di Azure](../azure-functions/manage-connections.md).

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