<properties
   pageTitle="Scenario di app per la logica: Funzioni di Azure come trigger del bus di servizio | Microsoft Azure"
   description="Informazioni su come usare Funzioni di Azure come trigger del bus di servizio per le app per la logica"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# Scenario di app per la logica: Funzioni di Azure come trigger del bus di servizio

È possibile usare Funzioni di Azure come trigger per le app per la logica ogni volta che occorre distribuire listener o attività a esecuzione prolungata. Ad esempio, è possibile creare una funzione di Azure che sia in ascolto su una coda e attivi immediatamente un'app per la logica come trigger di push.

## Compilazione dell'app per la logica

In questo esempio si avrà una funzione in esecuzione per ogni app per la logica che deve essere attivata. Prima di tutto, è necessario creare un'app per la logica con un trigger di richiesta HTTP. La funzione di Azure chiamerà tale endpoint ogni volta che viene ricevuto un messaggio nella coda.

1. Aprire una nuova app per la logica e selezionare il trigger **Manuale - Alla ricezione di una richiesta HTTP**.  
    * È possibile specificare facoltativamente uno schema JSON per il messaggio nella coda usando uno strumento come [jsonschema.net](http://jsonschema.net) e incollarlo nel trigger. In questo modo la finestra di progettazione potrà riconoscere la forma dei dati e trasferire facilmente le proprietà attraverso il flusso di lavoro.
1. Aggiungere tutti i passaggi da eseguire dopo la ricezione di un messaggio nella coda. Ad esempio, è possibile inviare un messaggio di posta elettronica tramite Office 365.  
1. Salvare l'app per la logica per generare l'URL di callback per il trigger di questa app per la logica. L'URL verrà visualizzato nella scheda del trigger.

![][1]

## Compilazione della funzione di Azure

A questo punto, è necessario creare una funzione di Azure che fungerà da trigger e sarà in ascolto sulla coda.

1. Aprire il [portale di Funzioni di Azure](https://functions.azure.com/signin) e selezionare **Nuova funzione** con un modello **ServiceBusQueueTrigger - C#**.

    ![][2]

2. Configurare la connessione alla coda del bus di servizio, che userà il listener `OnMessageReceive()` di Service Bus SDK
3. Scrivere una funzione semplice per chiamare l'endpoint dell'app per la logica, indicato sopra, con il messaggio nella coda. Ecco un esempio completo di una funzione con un content-type del messaggio `application/json`, che tuttavia può essere modificato secondo le esigenze.

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

È possibile eseguire il test aggiungendo un messaggio nella coda tramite uno strumento come [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) e vedere che l'app per la logica viene attivata immediatamente dopo la ricezione del messaggio da parte della funzione.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->