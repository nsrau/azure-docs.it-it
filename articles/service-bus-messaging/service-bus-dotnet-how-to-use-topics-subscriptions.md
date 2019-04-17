---
title: Introduzione agli argomenti e alle sottoscrizioni del bus di servizio di Azure | Microsoft Docs
description: Scrivere un'applicazione console C# .NET Core che usa gli argomenti e le sottoscrizioni di messaggistica del bus di servizio.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 892d485fb5cdaa08107870e9ab5b2b7ad9bcba5b
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608895"
---
# <a name="get-started-with-service-bus-topics"></a>Introduzione agli argomenti del bus di servizio

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questa esercitazione illustra i passaggi seguenti:

1. Scrivere un'applicazione console .NET Core per inviare un set di messaggi all'argomento.
2. Scrivere un'applicazione console .NET Core per ricevere tali messaggi dalla sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

1. Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [i benefici della sottoscrizione MSDN o Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) o iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Seguire i passaggi nel [Guida introduttiva: Usare il portale di Azure per creare un argomento del Bus di servizio e le sottoscrizioni all'argomento](service-bus-quickstart-topics-subscriptions-portal.md) per eseguire le attività seguenti:
    1. Creare un Bus di servizio **dello spazio dei nomi**.
    2. Ottenere il **stringa di connessione**.
    3. Creare un **argomento** nello spazio dei nomi.
    4. Creare **una sottoscrizione** all'argomento nello spazio dei nomi.
3. [Visual Studio 2017 Update 3 (versione 15.3, 26730.01)](https://www.visualstudio.com/vs) o versioni successive.
4. [NET Core SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.
 
## <a name="send-messages-to-the-topic"></a>Inviare messaggi all'argomento

Per inviare messaggi all'argomento, scrivere un'applicazione console C# usando Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio e creare un nuovo progetto **Console App (.NET Core)** (App console - .NET Core).

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Sfoglia**, cercare **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** e quindi selezionare l'elemento **Microsoft.Azure.ServiceBus**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
   
    ![Selezionare un pacchetto NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Scrivere il codice per inviare messaggi all'argomento

1. In Program.cs aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione della classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Nella classe `Program` dichiarare le variabili seguenti. Impostare la variabile `ServiceBusConnectionString` sulla stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e `TopicName` sul nome usato durante la creazione dell'argomento:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Sostituire il contenuto predefinito di `Main()` con la riga di codice seguente:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. Subito dopo `Main()` aggiungere il metodo asincrono `MainAsync()` seguente, che chiama il metodo per l'invio dei messaggi:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. Subito dopo il metodo `MainAsync()` aggiungere il metodo `SendMessagesAsync()` seguente, che esegue l'operazione di invio del numero di messaggi specificato da `numberOfMessagesToSend` (attualmente impostato su 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Il file Program.cs del mittente sarà simile al seguente.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Eseguire il programma e controllare il portale di Azure facendo clic sul nome dell'argomento nella finestra **Panoramica** dello spazio dei nomi. Verrà visualizzata la schermata **Informazioni di base** dell'argomento. Nella sottoscrizione riportata nella parte inferiore della finestra si noti che il valore di **Conteggio messaggi** per la sottoscrizione è ora **10**. Ogni volta che si esegue l'applicazione mittente senza recuperare i messaggi (come descritto nella sezione successiva), questo valore aumenta di 10. Si noti anche che la dimensione corrente dell'argomento aumenta il valore di **Corrente** nella finestra **Informazioni di base** ogni volta che l'app aggiunge messaggi all'argomento.
   
      ![Dimensioni dei messaggi][topic-message]

## <a name="receive-messages-from-the-subscription"></a>Ricevere messaggi dalla sottoscrizione

Per ricevere i messaggi inviati, creare un'altra applicazione console .NET Core e installare il **Microsoft.Azure.ServiceBus** pacchetto NuGet, come per l'applicazione mittente precedente.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Scrivere il codice per ricevere messaggi dalla sottoscrizione

1. In Program.cs aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione della classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Nella classe `Program` dichiarare le variabili seguenti. Impostare la variabile `ServiceBusConnectionString` sulla stringa di connessione ottenuta al momento della creazione dello spazio dei nomi, `TopicName` sul nome usato durante la creazione dell'argomento e `SubscriptionName` sul nome usato durante la creazione della sottoscrizione all'argomento:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Sostituire il contenuto predefinito di `Main()` con la riga di codice seguente:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Subito dopo `Main()` aggiungere il metodo asincrono `MainAsync()` seguente, che chiama il metodo `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. Subito dopo il metodo `MainAsync()` aggiungere il metodo seguente, che registra il gestore di messaggi e riceve i messaggi inviati dall'applicazione mittente:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Subito dopo il metodo precedente aggiungere il metodo `ProcessMessagesAsync()` seguente per elaborare i messaggi ricevuti:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Aggiungere infine il metodo seguente per gestire le eccezioni che potrebbero verificarsi:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Il file Program.cs del ricevitore sarà simile al seguente:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Eseguire il programma e verificare di nuovo il portale. Si noti che ora i valori di **Conteggio messaggi** e di **Corrente** sono **0**.
   
    ![Lunghezza argomento][topic-message-receive]

Congratulazioni. Usando la libreria .NET Standard sono stati creati un argomento e una sottoscrizione e sono stati inviati 10 messaggi che sono quindi stati ricevuti.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [repository GitHub con esempi](https://github.com/Azure/azure-service-bus/tree/master/samples) del bus di servizio che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
