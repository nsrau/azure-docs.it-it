---
title: Come usare le code di Azure e del bus di servizio in node. js
description: Informazioni su come usare le code del bus di servizio in Azure da un'app node. js usando il pacchetto Azure/Service-Bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790317"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Come usare le code del bus di servizio con node. js e il pacchetto Azure/Service-Bus
> [!div class="op_multi_selector" title1="Linguaggio di programmazione" title2="Pacchetto node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Questa esercitazione illustra come scrivere un programma NodeJS per inviare e ricevere messaggi da una coda del bus di servizio usando il nuovo pacchetto di [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Questo pacchetto usa il [protocollo AMQP 1,0](service-bus-amqp-overview.md) più veloce, mentre il pacchetto [Azure-SB](https://www.npmjs.com/package/azure-sb) precedente usa le [API di runtime REST del bus di servizio](/rest/api/servicebus/service-bus-runtime-rest). Gli esempi sono scritti in JavaScript.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire i passaggi nell'articolo [usare portale di Azure per creare una](service-bus-quickstart-portal.md) coda del bus di servizio per creare una coda. Annotare la stringa di connessione per l'istanza del bus di servizio e il nome della coda creata. Questi valori verranno utilizzati negli esempi.

> [!NOTE]
> - Questa esercitazione funziona con esempi che è possibile copiare ed eseguire usando [NodeJS](https://nodejs.org/). Per istruzioni su come creare un'applicazione Node. js, vedere [creare e distribuire un'applicazione Node. js in un sito Web di Azure](../app-service/app-service-web-get-started-nodejs.md)o un [servizio cloud node. js usando Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Il nuovo pacchetto di [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) non supporta ancora la creazione di code. Usare il pacchetto [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se si vuole crearli a livello di codice.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usare Node Package Manager (NPM) per installare il pacchetto
Per installare il pacchetto NPM per il bus di servizio, aprire un prompt dei comandi con `npm` nel percorso, impostare la directory sulla cartella in cui si desidera ottenere gli esempi, quindi eseguire il comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
L'interazione con una coda del bus di servizio inizia con la creazione di un'istanza della classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e la usa per creare un'istanza della classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Una volta ottenuto il client della coda, è possibile creare un mittente e utilizzare il metodo [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) o [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) su di esso per inviare messaggi.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
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
3. Immettere la stringa di connessione e il nome della coda nel codice riportato sopra.
4. Eseguire quindi il comando `node send.js` in un prompt dei comandi per eseguire il file.

Congratulazioni. Sono stati appena inviati messaggi a una coda del bus di servizio.

I messaggi hanno alcune proprietà standard come `label` e `messageId` che è possibile impostare durante l'invio. Se si desidera impostare proprietà personalizzate, utilizzare il `userProperties`, ovvero un oggetto JSON che può includere coppie chiave-valore dei dati personalizzati.

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e pari a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Non è previsto alcun limite per il numero di messaggi contenuti in una coda, ma è previsto un limite per le dimensioni totali dei messaggi contenuti in una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
L'interazione con una coda del bus di servizio inizia con la creazione di un'istanza della classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e la usa per creare un'istanza della classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Una volta ottenuto il client della coda, è possibile creare un ricevitore e utilizzare il metodo [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) o [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) su di esso per ricevere i messaggi.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare un file denominato `recieve.js` e incollarvi il codice riportato di seguito. Questo codice tenterà di ricevere 10 messaggi dalla coda. Il conteggio effettivo ricevuto dipende dal numero di messaggi nella coda e nella latenza di rete.

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
3. Immettere la stringa di connessione e il nome della coda nel codice riportato sopra.
4. Eseguire quindi il comando `node receiveMessages.js` in un prompt dei comandi per eseguire il file.

Congratulazioni. Sono stati appena ricevuti messaggi da una coda del bus di servizio.

Il metodo [CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) accetta un `ReceiveMode` che è un'enumerazione con i valori [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Tenere presente che i [messaggi vengono risolti](message-transfers-locks-settlement.md#settling-receive-operations) se si usa la modalità `PeekLock` usando uno dei metodi `complete()`, `abandon()`, `defer()`o `deadletter()` del messaggio.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le risorse seguenti.
- [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
- Estrai altri [esempi di NodeJS per il bus di servizio su GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro per sviluppatori di Node.js](https://azure.microsoft.com/develop/nodejs/)

