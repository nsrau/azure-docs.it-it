---
title: Introduzione alle code del bus di servizio di Azure (Azure.Messaging.ServiceBus)
description: In questa esercitazione si crea un'applicazione C# .NET Core per l'invio e la ricezione di messaggi da una coda del bus di servizio.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 15e5d257259bb4dfc98528cb726dbd2cc1f9a903
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498728"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Inviare e ricevere messaggi dalle code del bus di servizio di Azure (.NET)
In questa esercitazione si crea un'applicazione console .NET Core per l'invio e la ricezione di messaggi da una coda del bus di servizio usando il pacchetto **Azure.Messaging.ServiceBus**. 

> [!Important]
> Questa guida di avvio rapido usa il nuovo pacchetto Azure.Messaging.ServiceBus. Per una guida di avvio rapido che usa il pacchetto Microsoft.Azure.ServiceBus precedente, vedere [Inviare e ricevere eventi con il pacchetto Microsoft.Azure.ServiceBus](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) per crearne una. Prendere nota della **stringa di connessione** per lo spazio dei nomi del bus di servizio e del nome della **coda** creata.

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
In questa guida di avvio rapido si crea un'applicazione console C# .NET Core per l'invio di messaggi alla coda.

### <a name="create-a-console-application"></a>Creare un'applicazione console
Avviare Visual Studio e creare un nuovo progetto **App console (.NET Core)** per C#. 

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia**. Cercare e selezionare **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selezionare **Installa** per completare l'installazione, quindi chiudere Gestione pacchetti NuGet.

### <a name="add-code-to-send-messages-to-the-queue"></a>Aggiungere il codice per inviare messaggi alla coda

1. In *Program.cs* aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione della classe:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. Nella classe `Program` dichiarare le variabili seguenti:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Immettere la stringa di connessione per lo spazio dei nomi come variabile `ServiceBusConnectionString`. Immettere il nome della coda.

1. Sostituire il metodo `Main()` con il metodo `Main` **asincrono** seguente. Questo metodo chiama il metodo `SendMessagesAsync()` che verrà aggiunto nel passaggio successivo per inviare messaggi alla coda. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        
        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();
    }
    ```
1. Subito dopo il metodo `Main()` aggiungere il metodo `SendMessagesAsync()` seguente, che esegue l'operazione di invio del numero di messaggi specificato da `numberOfMessagesToSend` (attualmente impostato su 10):

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
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
1. Aggiungere un metodo denominato `SendMessageBatchAsync` alla classe `Program` e includere il codice seguente. Questo metodo prepara uno o più batch di una coda di messaggi da inviare alla coda del bus di servizio. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Sostituire il metodo `Main()` con il metodo `Main` **asincrono** seguente. Questo metodo chiama entrambi i metodi per inviare un singolo messaggio e un batch di messaggi alla coda. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Eseguire l'applicazione. Verranno visualizzati i messaggi seguenti. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. Nel portale di Azure seguire questa procedura:
    1. Passare allo spazio dei nomi del bus di servizio. 
    1. Nella pagina **Panoramica** selezionare la coda nel riquadro centrale inferiore. 
    1. Notare i valori presenti nella sezione **Informazioni di base**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Messaggi ricevuti, con il numero e le dimensioni" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Notare i valori seguenti:
    - Il valore di **Numero di messaggi attivi** per la coda è ora **4**. Ogni volta che si avvia l'app mittente senza recuperare i messaggi, questo valore aumenta di 4.
    - Le dimensioni correnti della coda aumentano il valore di **CORRENTE** nella finestra **Informazioni di base** ogni volta che l'app aggiunge messaggi alla coda.
    - Nel grafico **Messaggi** della sezione **Metriche** in basso sono presenti quattro messaggi in ingresso per la coda. 

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
In questa sezione si aggiunge il codice per recuperare i messaggi dalla coda.

1. Aggiungere i metodi seguenti alla classe `Program` per gestire i messaggi ed eventuali errori. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Aggiungere un metodo denominato `ReceiveMessagesAsync` alla classe `Program` e includere il codice seguente per ricevere i messaggi. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

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
1. Aggiungere una chiamata al metodo `ReceiveMessagesAsync` dal metodo `Main`. Impostare come commento il metodo `SendMessagesAsync` se si vuole testare solo la ricezione di messaggi. In caso contrario, vengono visualizzati altri quattro messaggi inviati alla coda. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Eseguire l'app
Eseguire l'applicazione. Attendere un minuto e quindi premere un tasto qualsiasi per interrompere la ricezione di messaggi. Verrà visualizzato l'output seguente (barra spaziatrice come tasto). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Controllare di nuovo il portale. 

- I valori di **Numero di messaggi attivi** e di **CORRENTE** sono ora **0**.
- Nel grafico **Messaggi** della sezione **Metriche** in basso sono presenti otto messaggi in ingresso e otto in uscita per la coda. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Numero e dimensioni dei messaggi attivi dopo la ricezione" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti:

- [Libreria client del bus di servizio di Azure per .NET - Leggimi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Esempi in GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Riferimento alle API .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true&view=azure-dotnet-preview)