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
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992124"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Come usare le sottoscrizioni e gli argomenti del Bus di servizio con Node. js e il pacchetto/service-bus di azure
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node. js | Service bus di azure)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

In questa esercitazione descrive come scrivere un programma di Node. js per inviare messaggi a un argomento del Bus di servizio e ricevere messaggi da una sottoscrizione del Bus di servizio usando le nuove [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pacchetto. Questo pacchetto utilizza più veloci [protocollo AMQP 1.0](service-bus-amqp-overview.md) mentre il precedente [azure-sb](https://www.npmjs.com/package/azure-sb) pacchetto utilizzato [API di runtime REST del Bus di servizio](/rest/api/servicebus/service-bus-runtime-rest). Gli esempi sono scritti in JavaScript.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici per sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) oppure iscriversi per una [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si dispone di un argomento e sottoscrizione usare, seguire i passaggi nel [portale di Azure Usa per creare un Bus di servizio di argomenti e sottoscrizioni](service-bus-quickstart-topics-subscriptions-portal.md) articolo per crearli. Annotare la stringa di connessione per l'istanza del Bus di servizio e i nomi di argomento e la stessa sottoscrizione che è stato creato. Si userà questi valori negli esempi.

> [!NOTE]
> - Questa esercitazione si presuppone con esempi che è possibile copiare ed eseguire tramite [Nodejs](https://nodejs.org/). Per istruzioni su come creare un'applicazione Node. js, vedere [creare e distribuire un'applicazione Node. js in un sito Web di Azure](../app-service/app-service-web-get-started-nodejs.md), o [servizio Cloud Node. js usando Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Il nuovo [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pacchetto non supporta la creazione di sottoscrizioni e topcis ancora. Usare la [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) del pacchetto se si desidera crearle a livello di codice.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usare Node Package Manager (NPM) per installare il pacchetto
Per installare il pacchetto npm per il Bus di servizio, aprire un prompt dei comandi con `npm` nel relativo percorso, cambiare la directory alla cartella in cui si desidera avere esempi e quindi eseguire questo comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
L'interazione con un Bus di servizio argomento inizia con creazione di un'istanza di [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe e usandolo per creare un'istanza di [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) classe. Dopo aver creato il client dell'argomento, è possibile creare un mittente e utilizzare [inviare](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) oppure [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metodo su di esso per inviare messaggi.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare un file denominato `send.js` e incollare il seguente codice al suo interno. Questo codice invierà 10 messaggi all'argomento.

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
3. Immettere la stringa di connessione e il nome dell'argomento nel codice precedente.
4. Quindi eseguire il comando `node send.js` in un prompt dei comandi eseguire questo file. 

Congratulazioni! Sono inviati solo messaggi a una coda del Bus di servizio.

I messaggi hanno alcune proprietà standard, ad esempio `label` e `messageId` che è possibile impostare durante l'invio. Se si desidera impostare qualsiasi proprietà personalizzata, usare il `userProperties`, ovvero un oggetto json che può contenere coppie chiave-valore dei dati personalizzati.

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Non sono previsti limiti al numero di messaggi mantenuti in un argomento, ma è previsto un limite alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
L'interazione con un Bus di servizio sottoscrizione inizia con creazione di un'istanza di [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe e usandolo per creare un'istanza di [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) classe. Dopo aver creato il client di sottoscrizione, è possibile creare un ricevitore e utilizzare [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) oppure [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metodo su di esso per ricevere messaggi.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
2. Creare un file denominato `recieve.js` e incollare il seguente codice al suo interno. Questo codice tenterà di ricezione 10 messaggi dalla sottoscrizione. Il conteggio effettivo che viene visualizzato dipende dal numero di messaggi nella sottoscrizione e la latenza di rete.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
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
3. Nel codice precedente, immettere la stringa di connessione e nomi di argomento e sottoscrizione.
4. Quindi eseguire il comando `node receiveMessages.js` in un prompt dei comandi eseguire questo file.

Congratulazioni! Appena ricevuti dei messaggi da una sottoscrizione del Bus di servizio.

Il [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) metodo accetta un `ReceiveMode` che è un'enumerazione con i valori [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ricordarsi di [finalizzazione dei messaggi](message-transfers-locks-settlement.md#settling-receive-operations) se si usa la `PeekLock` utilizzando una delle modalità `complete()`, `abandon()`, `defer()`, o `deadletter()` metodi sul messaggio.

## <a name="subscription-filters-and-actions"></a>Azioni e i filtri della sottoscrizione
Il Bus di servizio supporta [filtri e azioni sulle sottoscrizioni](topic-filters.md), che consente di filtrare i messaggi in ingresso a una sottoscrizione e per modificare le relative proprietà.

Dopo aver ottenuto un'istanza di un `SubscriptionClient` è possibile usare il seguito metodi su di essa per ottenere, aggiungere e rimuovere le regole per la sottoscrizione per controllare i filtri e azioni.

- getRules
- addRule
- removeRule

Ogni sottoscrizione dispone di una regola predefinita che utilizza il filtro true per consentire tutti i messaggi in ingresso. Quando si aggiunge una nuova regola, ricordarsi di rimuovere il filtro predefinito nell'ordine del filtro nella nuova regola a funzionare. Se una sottoscrizione non dispone di alcuna regola, non riceverà alcun messaggio.

> [!NOTE]
> È possibile gestire le risorse del Bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del Bus di servizio e amministrarne le entità di messaggistica in modo semplificato. Lo strumento offre funzionalità avanzate come la funzionalità di importazione/esportazione o la possibilità di testare l'argomento, code, sottoscrizioni, servizi di inoltro, hub di notifica e gli hub eventi. 

## <a name="next-steps"></a>Fasi successive
Per altre informazioni, vedere le risorse seguenti.

- [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
- Fare riferimento ad altri [Nodejs esempi del Bus di servizio su GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro per sviluppatori di Node. js](https://azure.microsoft.com/develop/nodejs/)


