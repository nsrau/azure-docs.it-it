---
title: Usare gli argomenti e le sottoscrizioni del bus di servizio di Azure con Java (azure-messaging-servicebus)
description: In questa guida di avvio rapido viene scritto codice Java con il pacchetto azure-messaging-servicebus per inviare messaggi a un argomento del bus di servizio di Azure e quindi ricevere messaggi dalle sottoscrizioni di tale argomento.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 6a63e907e01174042f679c57e1ef0994ad2a11b9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909578"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Inviare messaggi a un argomento del bus di servizio di Azure e ricevere messaggi dalle sottoscrizioni dell'argomento (Java)
In questa guida di avvio rapido viene scritto codice Java con il pacchetto azure-messaging-servicebus per inviare messaggi a un argomento del bus di servizio di Azure e quindi ricevere messaggi dalle sottoscrizioni di tale argomento.

> [!IMPORTANT]
> Questa guida di avvio rapido usa il nuovo pacchetto azure-messaging-servicebus, che è disponibile in **anteprima**. Per una guida di avvio rapido che usa il pacchetto azure-servicebus attualmente disponibile a livello generale, vedere [Inviare e ricevere messaggi con azure-servicebus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Seguire la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni dell'argomento](service-bus-quickstart-topics-subscriptions-portal.md). Prendere nota dei valori di stringa di connessione, nome dell'argomento e nome della sottoscrizione. Per questa guida di avvio rapido verrà usata una sola sottoscrizione. 
- Installare [Azure SDK per Java][Azure SDK for Java]. Se si usa Eclipse, è possibile installare [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] che include Azure SDK per Java. È quindi possibile aggiungere le **librerie di Microsoft Azure per Java** al progetto. Se si usa IntelliJ, vedere [Installare Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
In questa sezione verrà creato un progetto di console Java, quindi verrà aggiunto codice per inviare messaggi all'argomento creato. 

### <a name="create-a-java-console-project"></a>Creare un progetto di console Java
Creare un progetto Java usando Eclipse o un altro strumento a scelta. 

### <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Aggiungere un riferimento alla libreria del bus di servizio di Azure. La libreria client Java per il bus di servizio è disponibile nel [repository centrale di Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Per fare riferimento a questa libreria è possibile usare questa dichiarazione di dipendenza all'interno del file di progetto di Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0-beta.7</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Aggiungere il codice per inviare messaggi all'argomento
1. Aggiungere le istruzioni `import` seguenti nell'argomento del file Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Nella classe definire le variabili in cui contenere la stringa di connessione e il nome dell'argomento, come illustrato di seguito: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Sostituire `<NAMESPACE CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio. Sostituire `<TOPIC NAME>` con il nome dell'argomento.
3. Aggiungere un metodo denominato `sendMessage` nella classe per inviare un messaggio all'argomento. 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. Aggiungere un metodo denominato `createMessages` nella classe per creare un elenco di messaggi. In genere, questi messaggi provengono da parti diverse dell'applicazione. In questo caso verrà creato un elenco di messaggi di esempio.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Aggiungere un metodo denominato `sendMessageBatch` per inviare messaggi all'argomento creato. Questo metodo crea un oggetto `ServiceBusSenderClient` per l'argomento, richiama il metodo `createMessages` per ottenere l'elenco di messaggi, prepara uno o più batch e li invia all'argomento. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
In questa sezione si aggiunge il codice per recuperare i messaggi dalla sottoscrizione dell'argomento. 

1. Aggiungere un metodo denominato `receiveMessages` per ricevere messaggi dalla sottoscrizione. Questo metodo crea un oggetto `ServiceBusProcessorClient` per la sottoscrizione specificando un gestore per l'elaborazione di messaggi e un altro per la gestione degli errori. Quindi avvia il processore, aspetta alcuni secondi, stampa i messaggi ricevuti e infine arresta e chiude il processore.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }
    ```
2. Aggiornare il metodo `main` per richiamare i metodi `sendMessage`, `sendMessageBatch` e `receiveMessages` e per generare `InterruptedException`.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Eseguire l'app
Eseguire il programma per visualizzare l'output simile all'output seguente:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

Nella pagina **Panoramica** dello spazio dei nomi del bus di servizio nel portale di Azure è possibile visualizzare il numero di messaggi **in ingresso** e **in uscita**. Può essere necessario aspettare circa un minuto e quindi aggiornare la pagina per vedere i valori più recenti. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Numero di messaggi in ingresso e in uscita" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Passare alla scheda **Argomenti** nel riquadro centrale inferiore, quindi selezionare l'argomento per visualizzare la relativa pagina **Argomento del bus di servizio**. In questa pagina verranno visualizzati quattro messaggi in ingresso e quattro messaggi in uscita nel grafico **Messaggi**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Messaggi in ingresso e in uscita" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Se si imposta come commento la chiamata `receiveMessages` nel metodo `main` e si esegue di nuovo l'app, nella pagina **Argomento del bus di servizio** verranno visualizzati 8 messaggi in ingresso (4 nuovi) ma solo quattro messaggi in uscita. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Pagina dell'argomento aggiornata" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Se in questa pagina si seleziona una sottoscrizione si passa alla pagina **Sottoscrizione del bus di servizio**. In questa pagina è possibile visualizzare il numero di messaggi attivi, il numero di messaggi non recapitabili e altro ancora. In questo esempio sono presenti quattro messaggi attivi che non sono stati ancora ricevuti dal destinatario. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Numero di messaggi attivi" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti:

- [Libreria client del bus di servizio di Azure per Java - Leggimi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Esempi in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Informazioni di riferimento sulle API Java](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-preview&preserve-view=true)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

