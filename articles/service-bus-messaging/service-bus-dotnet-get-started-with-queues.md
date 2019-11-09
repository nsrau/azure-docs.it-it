---
title: Introduzione alle code del bus di servizio di Azure | Microsoft Docs
description: Scrivere un'applicazione console C# che usa le code di messaggistica del bus di servizio.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/04/2019
ms.author: aschhab
ms.openlocfilehash: cecd37c16d34c0cd6cf7a4d98732762d16073864
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884137"
---
# <a name="get-started-with-service-bus-queues"></a>Introduzione alle code del bus di servizio
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
In questa esercitazione vengono create applicazioni console .NET Core per l'invio e la ricezione di messaggi da una coda del bus di servizio.

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire i passaggi nell'articolo [usare portale di Azure per creare una](service-bus-quickstart-portal.md) coda del bus di servizio per creare una coda.

  - Leggere la breve panoramica delle code del bus di servizio.
  - Creare uno spazio dei nomi del bus di servizio.
  - Ottenere la stringa di connessione.
  - Creare una coda del bus di servizio.

## <a name="send-messages-to-the-queue"></a>Inviare messaggi alla coda

Per inviare messaggi alla coda, scrivere un'applicazione console C# in Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio e creare un nuovo progetto di **app console (.NET Core)** per C#. Questo esempio assegna un nome all'app *CoreSenderApp*.

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
1. Selezionare **Esplora**. Cercare e selezionare **[Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** .
1. Selezionare **Installa** per completare l'installazione e quindi chiudere Gestione pacchetti NuGet.

    ![Selezionare un pacchetto NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Scrivere il codice per inviare messaggi alla coda

1. In *Program.cs*aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione di classe:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Nella classe `Program` dichiarare le variabili seguenti:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Immettere la stringa di connessione per lo spazio dei nomi come variabile `ServiceBusConnectionString`. Immettere il nome della coda.

1. Sostituire il contenuto predefinito di `Main()` con la riga di codice seguente:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

1. Subito dopo `Main()` aggiungere il metodo asincrono `MainAsync()` seguente, che chiama il metodo per l'invio dei messaggi:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Direttamente dopo il metodo `MainAsync()` aggiungere il seguente metodo `SendMessagesAsync()` che esegue le operazioni di invio del numero di messaggi specificato da `numberOfMessagesToSend` (attualmente impostato su 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

Il file *Program.cs* dovrebbe essere simile al seguente.

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");

            // Send Messages
            await SendMessagesAsync(numberOfMessages);

            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
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

Eseguire il programma e controllare il portale di Azure.

Selezionare il nome della coda nella finestra **Panoramica** dello spazio dei nomi per visualizzare gli **elementi**di base della coda.

![Messaggi ricevuti con conteggio e dimensioni][queue-message]

Il valore del **conteggio messaggi attivo** per la coda è ora **10**. Ogni volta che si esegue questa app mittente senza recuperare i messaggi, questo valore aumenta di 10.

Le dimensioni correnti della coda incrementano il valore **corrente** in **Essentials** ogni volta che l'app aggiunge messaggi alla coda.

Nella sezione successiva viene descritto come recuperare questi messaggi.

## <a name="receive-messages-from-the-queue"></a>Ricezione di messaggi dalla coda

Per ricevere i messaggi inviati, creare un'altra applicazione **Console (.NET Core)** . Installare il pacchetto NuGet **Microsoft. Azure. ServiceBus** come per l'applicazione mittente.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Scrivere il codice per ricevere messaggi dalla coda

1. In *Program.cs*aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione di classe:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Nella classe `Program` dichiarare le variabili seguenti:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Immettere la stringa di connessione per lo spazio dei nomi come variabile `ServiceBusConnectionString`. Immettere il nome della coda.

1. Sostituire il contenuto predefinito di `Main()` con la riga di codice seguente:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

1. Subito dopo `Main()` aggiungere il metodo asincrono `MainAsync()` seguente, che chiama il metodo `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Direttamente dopo il metodo `MainAsync()` aggiungere il metodo seguente, che registra il gestore di messaggi e riceve i messaggi inviati dall'applicazione mittente:

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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Subito dopo il metodo precedente aggiungere il metodo `ProcessMessagesAsync()` seguente per elaborare i messaggi ricevuti:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Aggiungere infine il metodo seguente per gestire le eccezioni che potrebbero verificarsi:

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

Il file *Program.cs* dovrebbe essere simile al seguente:

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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

Eseguire il programma e verificare di nuovo il portale. Il **numero di messaggi attivi** e i valori **correnti** sono ora **0**.

![Coda dopo la ricezione dei messaggi][queue-message-receive]

Congratulazioni. A questo punto è stata creata una coda, è stato inviato un set di messaggi alla coda e sono stati ricevuti i messaggi dalla stessa coda.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi facilmente a uno spazio dei nomi del bus di servizio e amministrare le entità di messaggistica. Lo strumento offre funzionalità avanzate come la funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [repository GitHub con esempi](https://github.com/Azure/azure-service-bus/tree/master/samples) che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

