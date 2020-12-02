---
title: Inviare messaggi agli argomenti del bus di servizio con azure-messaging-servicebus
description: Questa guida di avvio rapido illustra come inviare messaggi agli argomenti del bus di servizio di Azure usando il pacchetto azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ddfca1186025f4118013815af7bbf7f500951ce
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95809271"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Inviare messaggi a un argomento del bus di servizio di Azure e ricevere messaggi dalle sottoscrizioni dell'argomento (.NET)
Questa esercitazione illustra come creare un'app console .NET Core che invia messaggi a un argomento del bus di servizio e riceve messaggi da una sottoscrizione dell'argomento. 

> [!Important]
> Questa guida di avvio rapido usa il nuovo pacchetto **Azure.Messaging.ServiceBus**. Per una guida di avvio rapido che usa il pacchetto Microsoft.Azure.ServiceBus precedente, vedere [Inviare e ricevere messaggi con il pacchetto Microsoft.Azure.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Seguire la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni dell'argomento](service-bus-quickstart-topics-subscriptions-portal.md). Prendere nota dei valori di stringa di connessione, nome dell'argomento e nome della sottoscrizione. Per questa guida di avvio rapido verrà usata una sola sottoscrizione. 

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
In questa sezione verrà creata un'applicazione console .NET Core in Visual Studio, quindi verrà aggiunto codice per inviare messaggi all'argomento creato. 

### <a name="create-a-console-application"></a>Creare un'applicazione console
Avviare Visual Studio e creare un nuovo progetto **App console (.NET Core)** per C#. 

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia**. Cercare e selezionare **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selezionare **Installa** per completare l'installazione, quindi chiudere Gestione pacchetti NuGet.

### <a name="add-code-to-send-messages-to-the-topic"></a>Aggiungere il codice per inviare messaggi all'argomento 

1. In Program.cs aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione della classe:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Nella classe `Program` dichiarare le variabili seguenti:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Sostituire i valori seguenti:
    - `<NAMESPACE CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio
    - `<TOPIC NAME>` con il nome dell'argomento
    - `<SUBSCRIPTION NAME>` con il nome della sottoscrizione
2. Aggiungere un metodo denominato `SendMessageToTopicAsync` che invia un messaggio all'argomento. 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
            }
        }
    ```
1. Aggiungere un metodo denominato `CreateMessages` per creare una coda (.NET) di messaggi nella classe `Program`. In genere, questi messaggi provengono da parti diverse dell'applicazione. In questo caso verrà creata una coda di messaggi di esempio.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Aggiungere un metodo denominato `SendMessageBatchAsync` alla classe `Program` e includere il codice seguente. Questo metodo prepara uno o più batch di una coda di messaggi da inviare all'argomento del bus di servizio. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. Sostituire il metodo `Main()` con il metodo `Main` **asincrono** seguente. Questo metodo chiama entrambi i metodi per inviare un singolo messaggio e un batch di messaggi all'argomento.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Eseguire l'applicazione. Dovrebbe venire visualizzato l'output seguente:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Nel portale di Azure seguire questa procedura:
    1. Passare allo spazio dei nomi del bus di servizio. 
    1. Nella pagina **Panoramica** passare alla scheda **Argomenti** nel riquadro centrale inferiore e selezionare l'argomento del bus di servizio. Nell'esempio seguente è `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Selezionare l'argomento":::
    1. Nella pagina **Argomento del bus di servizio**, nel grafico **Messaggi** della sezione **Metriche** in basso, sono presenti quattro messaggi in ingresso per l'argomento. Se il valore non viene visualizzato, attendere alcuni minuti e aggiornare la pagina per vedere il grafico aggiornato. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Messaggi inviati all'argomento" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Selezionare la sottoscrizione nel riquadro in basso. Nell'esempio seguente è **S1**. Nella pagina **Sottoscrizione del bus di servizio** il valore visualizzato per **Numero di messaggi attivi** è **4**. La sottoscrizione ha ricevuto i quattro messaggi inviati all'argomento, ma non sono stati ancora prelevati da nessun destinatario. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Messaggi ricevuti nella sottoscrizione" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione

1. Aggiungere i metodi seguenti alla classe `Program` per gestire i messaggi ed eventuali errori. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Aggiungere il metodo `ReceiveMessagesFromSubscriptionAsync` seguente alla classe `Program`.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Aggiungere una chiamata al metodo `ReceiveMessagesFromSubscriptionAsync` nel metodo `Main`. Impostare come commento il metodo `SendMessagesToTopicAsync` se si vuole testare solo la ricezione di messaggi. In caso contrario, vengono visualizzati altri quattro messaggi inviati all'argomento. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Eseguire l'app
Eseguire l'applicazione. Attendere un minuto e quindi premere un tasto qualsiasi per interrompere la ricezione di messaggi. Verrà visualizzato l'output seguente (barra spaziatrice come tasto). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Controllare di nuovo il portale. 

- Nella pagina **Argomento del bus di servizio** il grafico **Messaggi** mostra otto messaggi in ingresso e otto in uscita. Se questi valori non vengono visualizzati, attendere alcuni minuti e aggiornare la pagina per vedere il grafico aggiornato. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Messaggi inviati e ricevuti" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Nella pagina **Sottoscrizione del bus di servizio** il valore visualizzato per **Numero di messaggi attivi** è zero. Il motivo è che il destinatario ha ricevuto i messaggi inviati da questa sottoscrizione e li ha completati. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Numero di messaggi attivi nella sottoscrizione alla fine" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti:

- [Libreria client del bus di servizio di Azure per .NET - Leggimi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Esempi in GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Riferimento alle API .NET](https://docs.microsoft.com/dotnet/api/azure.messaging.servicebus?view=azure-dotnet-preview&preserve-view=true)

