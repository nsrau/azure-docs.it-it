---
title: Usare l'anteprima di azure/service-bus in JavaScript con argomenti e sottoscrizioni
description: Informazioni su come scrivere un programma JavaScript che usa l'ultima versione di anteprima del pacchetto @azure/service-bus per inviare messaggi a un argomento del bus di servizio e ricevere messaggi da una sottoscrizione dell'argomento.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 56bed63a9030135966a208dd1ad9b4c45cde328d
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95811705"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Avvio rapido: Argomenti e sottoscrizioni del bus di servizio con Node.js e pacchetto azure/service-bus
Questa esercitazione illustra come usare il pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) in un programma JavaScript per inviare messaggi a un argomento del bus di servizi e ricevere messaggi da una sottoscrizione del bus di servizio di tale argomento.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Seguire la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni dell'argomento](service-bus-quickstart-topics-subscriptions-portal.md). Prendere nota dei valori di stringa di connessione, nome dell'argomento e nome della sottoscrizione. Per questa guida di avvio rapido verrà usata una sola sottoscrizione. 

> [!NOTE]
> - Questa esercitazione usa esempi che è possibile copiare ed eseguire usando [NodeJS](https://nodejs.org/). Per istruzioni su come creare un'applicazione Node.js, vedere [Creare e distribuire un'applicazione Node.js in un sito Web di Azure](../app-service/quickstart-nodejs.md) oppure [Servizio cloud Node.js tramite PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usare Node Package Manager (NPM) per installare il pacchetto
Per installare il pacchetto npm per il bus di servizio, aprire un prompt dei comandi il cui percorso comprenda `npm`, passare alla cartella in cui si vogliono archiviare gli esempi, quindi eseguire questo comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
L'esempio di codice seguente illustra come inviare un batch di messaggi all'argomento del bus di servizio. Per i dettagli, vedere i commenti nel codice. 

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/).
2. Creare un file denominato `sendtotopic.js` e incollarvi il codice riportato di seguito. Questo codice invierà un messaggio all'argomento.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. Sostituire `<SERVICE BUS NAMESPACE CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio.
1. Sostituire `<TOPIC NAME>` con il nome dell'argomento. 
1. Quindi eseguire il comando in un prompt dei comandi per eseguire questo file.

    ```console
    node sendtotopic.js 
    ```
1. Viene visualizzato l'output seguente.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com/).
2. Creare un file denominato **receivefromsubscription.js** e incollarvi il codice seguente. Per i dettagli, vedere i commenti nel codice. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. Sostituire `<SERVICE BUS NAMESPACE CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi. 
1. Sostituire `<TOPIC NAME>` con il nome dell'argomento. 
1. Sostituire `<SUBSCRIPTION NAME>` con il nome della sottoscrizione dell'argomento. 
1. Quindi eseguire il comando in un prompt dei comandi per eseguire questo file.

    ```console
    node receivefromsubscription.js
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

Nel portale di Azure passare allo spazio dei nomi del bus di servizio, quindi selezionare l'argomento nel riquadro inferiore per visualizzare la relativa pagina **Argomento del bus di servizio**. In questa pagina verranno visualizzati tre messaggi in ingresso e tre messaggi in uscita nel grafico **Messaggi**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Messaggi in ingresso e in uscita":::

Se la prossima volta si esegue solo l'app di invio, nella pagina **Argomento del bus di servizio** verranno visualizzati sei messaggi in ingresso (3 nuovi) ma tre messaggi in uscita. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Pagina dell'argomento aggiornata":::

Se in questa pagina si seleziona una sottoscrizione si passa alla pagina **Sottoscrizione del bus di servizio**. In questa pagina è possibile visualizzare il numero di messaggi attivi, il numero di messaggi non recapitabili e altro ancora. In questo esempio sono presenti tre messaggi attivi che non sono stati ancora ricevuti dal destinatario. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Numero di messaggi attivi":::

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti: 

- [Libreria client del bus di servizio di Azure per Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). La cartella **javascript** contiene esempi in JavaScript e la cartella **typescript** esempi in TypeScript. 
- [Documentazione di riferimento di azure-servicebus](https://docs.microsoft.com/javascript/api/overview/azure/service-bus)
