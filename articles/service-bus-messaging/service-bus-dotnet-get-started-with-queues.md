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
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: abef7815effcf420c8a0065ed46ce3c16c19ebe0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991777"
---
# <a name="get-started-with-service-bus-queues"></a>Introduzione alle code del bus di servizio
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
In questa esercitazione creare applicazioni console .NET Core per inviare e ricevere messaggi da una coda del Bus di servizio. 

## <a name="prerequisites"></a>Prerequisiti

1. [Visual Studio 2017 Update 3 (versione 15.3, 26730.01)](https://www.visualstudio.com/vs) o versioni successive.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.
2. Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici per sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) oppure iscriversi per una [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
3. Se non si dispone di una coda per lavorare con, seguire i passaggi nel [portale di Azure Usa per creare una coda del Bus di servizio](service-bus-quickstart-portal.md) articolo creare una coda.
    1. Leggere la rapida **overview** del Bus di servizio **code**. 
    2. Creare un Bus di servizio **dello spazio dei nomi**. 
    3. Ottenere il **stringa di connessione**. 
    4. Creare un Bus di servizio **coda**. 

## <a name="send-messages-to-the-queue"></a>Inviare messaggi alla coda

Per inviare messaggi alla coda, scrivere un'applicazione console C# in Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio e creare un nuovo progetto **Console App (.NET Core)** (App console - .NET Core).

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Sfoglia**, cercare **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** e quindi selezionare l'elemento **Microsoft.Azure.ServiceBus**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
   
    ![Selezionare un pacchetto NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Scrivere il codice per inviare messaggi alla coda

1. In Program.cs aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione della classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Nella classe `Program` dichiarare le variabili seguenti. Impostare la variabile `ServiceBusConnectionString` sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare `QueueName` sul nome usato durante la creazione della coda:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
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

5. Subito dopo il metodo `MainAsync()` aggiungere il metodo `SendMessagesAsync()` seguente, che esegue l'operazione di invio del numero di messaggi specificato da `numberOfMessagesToSend` (attualmente impostato su 10):

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
   
6. Ecco l'aspetto che avrà il file Program.cs.
   
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

7. Eseguire il programma e nel portale di Azure fare clic sul nome della coda nella finestra **Panoramica** dello spazio dei nomi. Verrà visualizzata la schermata **Informazioni di base** della coda. Si noti che ora il valore **Numero di messaggi attivi** per la coda è **10**. Ogni volta che si esegue l'applicazione mittente senza recuperare i messaggi (come descritto nella sezione successiva), questo valore aumenta di 10. Si noti anche che la dimensione corrente della coda aumenta il valore di **Corrente** nella finestra **Informazioni di base** ogni volta che l'app aggiunge messaggi alla coda.
   
      ![Dimensioni dei messaggi][queue-message]

## <a name="receive-messages-from-the-queue"></a>Ricezione di messaggi dalla coda

Per ricevere i messaggi inviati, creare un'altra applicazione console .NET Core e installare il **Microsoft.Azure.ServiceBus** pacchetto NuGet, come per l'applicazione mittente precedente.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Scrivere il codice per ricevere messaggi dalla coda

1. In Program.cs aggiungere le istruzioni `using` seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione della classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Nella classe `Program` dichiarare le variabili seguenti. Impostare la variabile `ServiceBusConnectionString` sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare `QueueName` sul nome usato durante la creazione della coda:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Sostituire il contenuto predefinito di `Main()` con la riga di codice seguente:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Subito dopo `Main()` aggiungere il metodo asincrono `MainAsync()` seguente, che chiama il metodo `RegisterOnMessageHandlerAndReceiveMessages()`:

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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Subito dopo il metodo precedente aggiungere il metodo `ProcessMessagesAsync()` seguente per elaborare i messaggi ricevuti:
 
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
   
8. Ecco l'aspetto che avrà il file Program.cs:
   
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
4. Eseguire il programma e verificare di nuovo il portale. Si noti che ora i valori di **Numero di messaggi attivi** e di **Corrente** sono pari a **0**.
   
    ![Lunghezza coda][queue-message-receive]

Congratulazioni! È stata creata una coda, è stato inviato un set di messaggi alla coda e sono stati ricevuti tali messaggi dalla stessa coda.

> [!NOTE]
> È possibile gestire le risorse del Bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del Bus di servizio e amministrarne le entità di messaggistica in modo semplificato. Lo strumento offre funzionalità avanzate come la funzionalità di importazione/esportazione o la possibilità di testare l'argomento, code, sottoscrizioni, servizi di inoltro, hub di notifica e gli hub eventi. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [repository GitHub con esempi](https://github.com/Azure/azure-service-bus/tree/master/samples) che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

