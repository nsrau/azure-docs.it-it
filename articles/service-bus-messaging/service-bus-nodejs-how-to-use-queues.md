---
title: Come usare le code di azure/service-bus in JavaScript
description: Informazioni su come scrivere un programma JavaScript che usa l'ultima versione di anteprima del pacchetto @azure/service-bus per inviare e ricevere messaggi da una coda del bus di servizio.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 3f37fcc6d58eb1c206aef6db15c7826cfdcda274
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489429"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Inviare e ricevere messaggi dalle code del bus di servizio di Azure (JavaScript)
Questa esercitazione illustra come usare il pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) in un programma JavaScript per inviare e ricevere messaggi da una coda del bus di servizio.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) per crearne una. Prendere nota della **stringa di connessione** per lo spazio dei nomi del bus di servizio e del nome della **coda** creata.

> [!NOTE]
> - Questa esercitazione usa esempi che è possibile copiare ed eseguire usando [NodeJS](https://nodejs.org/). Per istruzioni su come creare un'applicazione Node.js, vedere [Creare e distribuire un'applicazione Node.js in un sito Web di Azure](../app-service/quickstart-nodejs.md) oppure [Servizio cloud Node.js tramite PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usare Node Package Manager (NPM) per installare il pacchetto
Per installare il pacchetto npm per il bus di servizio, aprire un prompt dei comandi il cui percorso comprenda `npm`, passare alla cartella in cui si vogliono archiviare gli esempi, quindi eseguire questo comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
Il codice di esempio seguente illustra come inviare un messaggio a una coda.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/).
2. Creare un file denominato `send.js` e incollarvi il codice riportato di seguito. Questo codice invierà un messaggio alla coda. Il messaggio include un'etichetta (Scientist) e un corpo (Einstein).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Sostituire `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` con la stringa di connessione allo spazio dei nomi del bus di servizio.
1. Sostituire `<QUEUE NAME>` con il nome della coda. 
1. Quindi eseguire il comando in un prompt dei comandi per eseguire questo file.

    ```console
    node send.js 
    ```
1. Viene visualizzato l'output seguente.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/).
2. Creare un file denominato `receive.js` e incollarvi il codice seguente.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Sostituire `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` con la stringa di connessione allo spazio dei nomi del bus di servizio.
1. Sostituire `<QUEUE NAME>` con il nome della coda. 
1. Quindi eseguire il comando in un prompt dei comandi per eseguire questo file.

    ```console
    node receive.js
    ```
1. Viene visualizzato l'output seguente.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Nella pagina **Panoramica** dello spazio dei nomi del bus di servizio nel portale di Azure è possibile visualizzare il numero di messaggi **in ingresso** e **in uscita**. Può essere necessario aspettare circa un minuto e quindi aggiornare la pagina per vedere i valori più recenti. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Numero di messaggi in ingresso e in uscita":::

Selezionare la coda in questa pagina **Panoramica** per passare alla pagina **Coda del bus di servizio**. Anche in questa pagina viene visualizzato il numero di messaggi **in ingresso** e **in uscita**. Vengono visualizzate anche altre informazioni, come le **dimensioni correnti** della coda, le **dimensioni massime**, il **numero di messaggi attivi** e così via. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Dettagli della coda":::
## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti: 

- [Libreria client del bus di servizio di Azure per Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). La cartella **javascript** contiene esempi in JavaScript e la cartella **typescript** esempi in TypeScript. 
- [Documentazione di riferimento di azure-servicebus](/javascript/api/overview/azure/service-bus)