---
title: 'Logic app scenario: Creare un trigger del bus di servizio di Funzioni di Azure | Microsoft Docs'
description: Utilizzare Funzioni di Azure un trigger del bus di servizio per un&quot;app per la logica
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3f8031d31d1291972026430e67005863579ae911


---
# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Scenario dell'app per la logica: Creare un trigger del bus di servizio utilizzando Funzioni di Azure
È possibile utilizzare Funzioni di Azure per creare un trigger per un'app per la logica quando è necessario distribuire un listener o un'attività con esecuzione prolungata. Ad esempio, è possibile creare una funzione che sia in ascolto su una coda e attivi immediatamente un'app per la logica come trigger di push.

## <a name="build-the-logic-app"></a>Compilare l'app per la logica
In questo esempio si ha una funzione in esecuzione per ogni app per la logica da attivare. Per prima cosa, creare un'app per la logica con un trigger di richiesta HTTP. La funzione chiamerà tale endpoint ogniqualvolta venga ricevuto un messaggio in coda.  

1. Creare una nuova app per la logica e selezionare il trigger **Manuale - Alla ricezione di una richiesta HTTP** .  
   Facoltativamente, è possibile specificare uno schema JSON da utilizzare con il messaggio della coda utilizzando uno strumento come [jsonschema.net](http://jsonschema.net). Incollare lo schema nel trigger. In questo modo la finestra di progettazione potrà riconoscere la forma dei dati e trasferire più facilmente le proprietà nel flusso di lavoro.
2. Aggiungere eventuali ulteriori passaggi che si desidera vengano eseguiti dopo la ricezione di un messaggio in coda. Ad esempio, inviare un messaggio di posta elettronica tramite Office 365.  
3. Salvare l'app per la logica per generare l'URL di callback per il trigger di questa app per la logica. L'URL è visualizzato nella scheda del trigger.

![L'URL callback è visualizzato nella scheda del trigger][1]

## <a name="build-the-function"></a>Compilare la funzione
A questo punto, è necessario creare una funzione che fungerà da trigger e sarà in ascolto sulla coda.

1. Nel [portale di Funzioni di Azure](https://functions.azure.com/signin) selezionare **Nuova funzione** e quindi il modello **ServiceBusQueueTrigger - C#**.
   
    ![portale di Funzioni di Azure][2]
2. Configurare la connessione alla coda del bus di servizio, che utilizzerà il listener `OnMessageReceive()` dell'SDK del bus di servizio di Azure.
3. Scrivere una funzione semplice per chiamare l'endpoint dell'app per la logica utilizzando il messaggio nella coda come trigger. Quello che segue è l'esempio completo di una funzione. Nell'esempio viene utilizzato un tipo di contenuto di messaggio `application/json` , ma se necessario è possibile modificare questo elemento.
   
   ```
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

Per effettuare una prova, aggiungere un messaggio in coda tramite uno strumento quale [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). L'app per la logica verrà attivata subito dopo che la funzione riceverà il messaggio.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG



<!--HONumber=Nov16_HO3-->


