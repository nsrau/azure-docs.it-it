---
title: Scenario - Attivare app per la logica con Funzioni di Azure e il bus di servizio di Azure | Documentazione Microsoft
description: Creare funzioni per attivare app per la logica usando Funzioni di Azure e il bus di servizio di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 69a4e4c59038599a7375466c46878bdd017582fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231611"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Scenario: Attivare app per la logica con Funzioni di Azure e il bus di servizio di Azure

È possibile utilizzare Funzioni di Azure per creare un trigger per un'app per la logica quando è necessario distribuire un listener o un'attività con esecuzione prolungata. Ad esempio, è possibile creare una funzione che sia in ascolto su una coda e attivi immediatamente un'app per la logica come trigger di push.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Prima di creare una funzione di Azure, [creare un'app per le funzioni](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Creare l'app per la logica

In questo esempio si ha una funzione in esecuzione per ogni app per la logica da attivare. Per prima cosa, creare un'app per la logica con un trigger di richiesta HTTP. La funzione chiamerà tale endpoint ogniqualvolta venga ricevuto un messaggio in coda.  

1. Accedere al [portale di Azure](https://portal.azure.com) e creare un'app per la logica vuota. 

   Se non si ha familiarità con le app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella casella di ricerca immettere "richiesta http". Nell'elenco dei trigger selezionare questo trigger: **Alla ricezione di una richiesta HTTP**.

   ![Selezionare il trigger](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Per il trigger **richiesta** è possibile immettere uno schema JSON da usare con il messaggio della coda. Gli schemi JSON consentono a Progettazione app per la logica di comprendere la struttura dei dati di input e permettono all'utente di selezionare più facilmente le voci di output durante il flusso di lavoro. 

   Per specificare uno schema, immetterlo nella casella **Schema JSON del corpo della richiesta**, ad esempio: 

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

1. Aggiungere le altre azioni che devono essere eseguite dopo la ricezione del messaggio della coda. 

   È ad esempio possibile inviare un messaggio di posta elettronica con il connettore Office 365 Outlook.

1. Salvare l'app per la logica in modo da generare l'URL di callback per il trigger nell'app per la logica. L'URL viene visualizzato nella proprietà **URL POST HTTP**.

   ![URL di callback generato per il trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Creare la funzione di Azure

A questo punto creare la funzione che agisce come trigger e rimane in ascolto sulla coda. 

1. Nel portale di Azure aprire ed espandere l'app per le funzioni, se non già aperta. 

1. Sotto il nome dell'app espandere **Funzioni**. Nel riquadro **Funzioni** scegliere **Nuova funzione**. Selezionare questo modello: **Service Bus Queue trigger - C#** (Trigger Coda del bus di servizio - C#)
   
   ![Selezionare il portale Funzioni di Azure](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Specificare un nome per il trigger, configurare la connessione alla coda del bus di servizio, che userà il listener `OnMessageReceive()` dell'SDK del bus di servizio di Azure.

1. Scrivere una semplice funzione per chiamare l'endpoint dell'app per la logica creata in precedenza usando il messaggio della coda come trigger, ad esempio: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

   Nell'esempio viene usato il tipo di contenuto di messaggio `application/json`, ma è possibile modificare questo tipo in base alle proprie esigenze.

1. Per testare la funzione, aggiungere un messaggio della coda usando uno strumento, ad esempio [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). 

   L'app per la logica viene attivata subito dopo che la funzione ha ricevuto il messaggio.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

