---
title: Come usare le code di azure/service-bus in Node.js
description: Informazioni su come scrivere un programma NodeJS per inviare e ricevere messaggi da una coda del bus di servizio usando il nuovo pacchetto @azure/service-bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: d1d0d2b57ec1ed23082101c9207c4700424dd7cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289753"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Guida introduttiva: Come usare le code del bus di servizio con Node.js e il pacchetto azure/service-bus
Questa esercitazione illustra come scrivere un programma NodeJS per inviare e ricevere messaggi da una coda del bus di servizio usando il nuovo pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Questo pacchetto usa il [protocollo AMQP 1.0 ](service-bus-amqp-overview.md) più veloce, mentre il precedente pacchetto [azure-sb](https://www.npmjs.com/package/azure-sb) usava le [API di runtime REST del bus di servizio](/rest/api/servicebus/service-bus-runtime-rest). Gli esempi sono scritti in JavaScript.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) per crearne una. Prendere nota della stringa di connessione per l'istanza del bus di servizio e del nome della coda creata. Questi valori verranno usati negli esempi.

> [!NOTE]
> - Questa esercitazione usa esempi che è possibile copiare ed eseguire usando [NodeJS](https://nodejs.org/). Per istruzioni su come creare un'applicazione Node.js, vedere [Creare e distribuire un'applicazione Node.js in un sito Web di Azure](../app-service/quickstart-nodejs.md) oppure [Servizio cloud Node.js tramite PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Il nuovo pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) non supporta ancora la creazione di code. Usare il pacchetto [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se si vuole crearle a livello di codice.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usare Node Package Manager (NPM) per installare il pacchetto
Per installare il pacchetto npm per il bus di servizio, aprire un prompt dei comandi il cui percorso comprenda `npm`, passare alla cartella in cui si vogliono archiviare gli esempi, quindi eseguire questo comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
L'interazione con una coda del bus di servizio inizia con la creazione di un'istanza della classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e con l'uso della stessa per creare un'istanza della classe [QueueClient](/javascript/api/@azure/service-bus/queueclient). Dopo aver ottenuto il client della coda, è possibile creare un mittente e usare il metodo [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) o [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) per inviare i messaggi.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/).
2. Creare un file denominato `send.js` e incollarvi il codice riportato di seguito. Questo codice invierà 10 messaggi alla coda.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome della coda nel codice precedente.
4. Eseguire quindi questo file tramite il comando `node send.js` in un prompt dei comandi.

Congratulazioni! I messaggi sono stati inviati a una coda del bus di servizio.

I messaggi hanno alcune proprietà standard, ad esempio `label` e `messageId` che è possibile impostare durante l'invio. Se si vogliono impostare proprietà personalizzate, usare `userProperties`. Si tratta di un oggetto JSON che può includere coppie chiave-valore dei dati personalizzati.

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Non esistono limiti al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alle dimensioni totali dei messaggi in una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
L'interazione con una coda del bus di servizio inizia con la creazione di un'istanza della classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e con l'uso della stessa per creare un'istanza della classe [QueueClient](/javascript/api/@azure/service-bus/queueclient). Dopo aver ottenuto il client della coda, è possibile creare un destinatario e usare il metodo [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) o [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) per ricevere i messaggi.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/).
2. Creare un file denominato `recieve.js` e incollarvi il codice riportato di seguito. Questo codice tenterà di ricevere 10 messaggi dalla coda. Il numero effettivo dei messaggi ricevuti dipende dal numero di messaggi nella coda e dalla latenza di rete.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome della coda nel codice precedente.
4. Eseguire quindi questo file tramite il comando `node receiveMessages.js` in un prompt dei comandi.

Congratulazioni! I messaggi sono stati ricevuti da una coda del bus di servizio.

Il metodo [createReceiver](/javascript/api/@azure/service-bus/queueclient#createreceiver-receivemode-) accetta `ReceiveMode`, vale a dire un'enumerazione con i valori [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ricordarsi di [finalizzare i messaggi](message-transfers-locks-settlement.md#settling-receive-operations) se si usa la modalità `PeekLock` usando uno dei metodi `complete()`, `abandon()`, `defer()`o `deadletter()` nel messaggio.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le risorse riportate di seguito.
- [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
- Estrarre altri [esempi di NodeJS per il bus di servizio in GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro per sviluppatori di Node. js](https://azure.microsoft.com/develop/nodejs/)