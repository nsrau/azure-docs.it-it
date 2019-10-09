---
title: Usare argomenti e sottoscrizioni del bus di servizio di Azure con Node.js | Microsoft Docs
description: Informazioni su come usare le sottoscrizioni e gli argomenti del bus di servizio in Azure da un'app Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: f927274e1e866a9cba72330280316cc5ee7d8047
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178063"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Come usare gli argomenti e le sottoscrizioni del bus di servizio con node. js e il pacchetto Azure/Service-Bus
> [!div class="op_multi_selector" title1="Linguaggio di programmazione" title2="Pacchetto node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Questa esercitazione illustra come scrivere un programma node. js per inviare messaggi a un argomento del bus di servizio e ricevere messaggi da una sottoscrizione del bus di servizio usando il nuovo pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Questo pacchetto usa il [protocollo AMQP 1,0](service-bus-amqp-overview.md) più veloce, mentre il pacchetto [Azure-SB](https://www.npmjs.com/package/azure-sb) precedente usa le [API di runtime REST del bus di servizio](/rest/api/servicebus/service-bus-runtime-rest). Gli esempi sono scritti in JavaScript.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha un argomento e una sottoscrizione da usare, seguire i passaggi descritti nell'articolo [usare portale di Azure per creare argomenti e sottoscrizioni del bus di servizio](service-bus-quickstart-topics-subscriptions-portal.md) per crearli. Annotare la stringa di connessione per l'istanza del bus di servizio e i nomi dell'argomento e della sottoscrizione creati. Questi valori verranno utilizzati negli esempi.

> [!NOTE]
> - Questa esercitazione funziona con esempi che è possibile copiare ed eseguire usando [NodeJS](https://nodejs.org/). Per istruzioni su come creare un'applicazione Node. js, vedere [creare e distribuire un'applicazione Node. js in un sito Web di Azure](../app-service/app-service-web-get-started-nodejs.md)o un [servizio cloud node. js usando Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Il nuovo pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) non supporta ancora la creazione di sottoscrizioni e topcis. Usare il pacchetto [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) per crearli a livello di codice.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usare Node Package Manager (NPM) per installare il pacchetto
Per installare il pacchetto NPM per il bus di servizio, aprire un prompt dei comandi con `npm` nel percorso, passare alla cartella in cui si desidera ottenere gli esempi, quindi eseguire il comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
L'interazione con un argomento del bus di servizio inizia con la creazione di un'istanza della classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e la usa per creare un'istanza della classe [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) . Una volta ottenuto il client dell'argomento, è possibile creare un mittente e utilizzare il metodo [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) o [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) su di esso per inviare messaggi.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare un file denominato `send.js` e incollarvi il codice riportato di seguito. Questo codice invierà 10 messaggi all'argomento.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome dell'argomento nel codice riportato sopra.
4. Eseguire quindi il comando `node send.js` in un prompt dei comandi per eseguire il file. 

La procedura è stata completata. Sono stati appena inviati messaggi a una coda del bus di servizio.

I messaggi hanno alcune proprietà standard come `label` e `messageId` che è possibile impostare durante l'invio. Se si desidera impostare proprietà personalizzate, utilizzare il `userProperties`, che è un oggetto JSON che può includere coppie chiave-valore dei dati personalizzati.

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Non esiste alcun limite al numero di messaggi contenuti in un argomento, ma è previsto un limite per le dimensioni totali dei messaggi contenuti in un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
L'interazione con una sottoscrizione del bus di servizio inizia con la creazione di un'istanza della classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e l'uso per creare un'istanza della classe [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) . Una volta ottenuto il client di sottoscrizione, è possibile creare un ricevitore e usare il metodo [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) o [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) su di esso per ricevere i messaggi.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare un file denominato `recieve.js` e incollarvi il codice riportato di seguito. Questo codice tenterà di ricevere 10 messaggi dalla sottoscrizione. Il conteggio effettivo ricevuto dipende dal numero di messaggi nella sottoscrizione e nella latenza di rete.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e i nomi dell'argomento e della sottoscrizione nel codice riportato sopra.
4. Eseguire quindi il comando `node receiveMessages.js` in un prompt dei comandi per eseguire il file.

La procedura è stata completata. Sono stati appena ricevuti messaggi da una sottoscrizione del bus di servizio.

Il metodo [CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) accetta un `ReceiveMode` che è un'enumerazione con i valori [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ricordarsi di [risolvere i messaggi](message-transfers-locks-settlement.md#settling-receive-operations) se si usa la modalità `PeekLock` usando uno dei metodi `complete()`, `abandon()`, `defer()` o `deadletter()` del messaggio.

## <a name="subscription-filters-and-actions"></a>Azioni e filtri di sottoscrizione
Il bus di servizio supporta [filtri e azioni sulle sottoscrizioni](topic-filters.md), che consente di filtrare i messaggi in ingresso in una sottoscrizione e di modificarne le proprietà.

Quando si dispone di un'istanza di un `SubscriptionClient` è possibile utilizzare i metodi seguenti per ottenere, aggiungere e rimuovere regole nella sottoscrizione per controllare i filtri e le azioni.

- getRules
- addRule
- removeRule

Ogni sottoscrizione dispone di una regola predefinita che utilizza il filtro true per consentire tutti i messaggi in arrivo. Quando si aggiunge una nuova regola, ricordarsi di rimuovere il filtro predefinito per consentire il funzionamento del filtro nella nuova regola. Se per una sottoscrizione non sono presenti regole, non verrà visualizzato alcun messaggio.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le risorse seguenti.

- [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
- Estrai altri [esempi di NodeJS per il bus di servizio su GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro per sviluppatori di Node. js](https://azure.microsoft.com/develop/nodejs/)


