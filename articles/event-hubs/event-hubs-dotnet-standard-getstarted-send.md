---
title: Inviare e ricevere eventi usando .NET Core - hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione .NET Core in grado di inviare eventi ad Hub eventi di Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: e572a9e19da144be61faefacc370cf512f5079ac
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725401"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core"></a>Inviare eventi a o ricevere eventi da hub eventi di Azure usando .NET Core
Hub eventi è un servizio che consente di elaborare grandi quantità di dati di telemetria sugli eventi da applicazioni e dispositivi connessi. Dopo aver raccolto i dati in Hub eventi, è possibile archiviarli usando un cluster di archiviazione o trasformarli usando un provider di analisi in tempo reale. Questa funzionalità di elaborazione e raccolta di eventi su vasta scala rappresenta un componente chiave delle moderne architetture di applicazioni, tra cui Internet delle cose (IoT). Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione illustra come creare applicazioni .NET Core in C# per inviare eventi a o ricevere eventi da un hub eventi. 

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

- [Microsoft Visual Studio 2015 o 2017](https://www.visualstudio.com). Gli esempi inclusi nell'esercitazione usano Visual Studio 2017, ma è supportato anche Visual Studio 2015.
- [Strumenti di Visual Studio 2015 o 2017 per .NET Core](https://www.microsoft.com/net/core). 
- **Creare uno spazio dei nomi di hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Quindi, ottenere il **stringa di connessione per lo spazio dei nomi dell'hub eventi** seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.

## <a name="send-events"></a>Inviare eventi 
Questa sezione illustra come creare un'applicazione console .NET Core per inviare eventi a un hub eventi. 

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**. Creare un'applicazione console di .NET Core.

![Nuovo progetto](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

Aggiungere il pacchetto NuGet della raccolta .NET Core [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) al progetto eseguendo i passaggi descritti di seguito: 

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Sfoglia**, quindi cercare "Microsoft.Azure.EventHubs" e selezionare il pacchetto **Microsoft.Azure.EventHubs**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

1. Aggiungere le istruzioni `using` seguenti all'inizio del file Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Aggiungere le costanti per la classe `Program` per la stringa di connessione e il percorso dell'entità di Hub eventi (nome dell'hub eventi singolo). Sostituire i segnaposto tra parentesi con i valori specifici ottenuti durante la creazione dell'hub eventi. Assicurarsi che `{Event Hubs connection string}` sia la stringa di connessione a livello di spazio dei nomi e non la stringa dell'hub eventi. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Aggiungere alla classe `Program` un nuovo metodo denominato `MainAsync` come da esempio seguente:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Aggiungere alla classe `Program` un nuovo metodo denominato `SendMessagesToEventHub` come da esempio seguente:

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Aggiungere il codice seguente al metodo `Main` nella classe `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Ecco l'aspetto che avrà il file Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Eseguire il programma e assicurarsi che non siano presenti errori.

## <a name="receive-events"></a>Ricevere eventi
In questa sezione viene illustrato come scrivere un'applicazione console .NET Core che riceve i messaggi da un hub eventi usando il [Host processore di eventi](event-hubs-event-processor-host.md). L'[host processore di eventi](event-hubs-event-processor-host.md) è una classe .NET che semplifica la ricezione di eventi dagli hub eventi gestendo checkpoint persistenti e ricezioni parallele da tali hub. Usando l'host processore di eventi è possibile suddividere gli eventi su più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare l'host processore di eventi per un ricevitore singolo.
> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), sostituire le stringhe `EventHubConnectionString`, `EventHubName`, `StorageAccountName`, `StorageAccountKey` e `StorageContainerName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**. Creare un'applicazione console di .NET Core.

![Nuovo progetto](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

Aggiungere al progetto i pacchetti NuGet [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) della libreria .NET Standard seguendo questa procedura: 

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Sfoglia**, cercare **Microsoft.Azure.EventHubs** e quindi selezionare il pacchetto **Microsoft.Azure.EventHubs**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
3. Ripetere i passaggi 1 e 2 e installare il pacchetto **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementare l'interfaccia IEventProcessor

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Aggiungi** e fare clic su **Classe**. Assegnare il nome **SimpleEventProcessor** alla nuova classe.

2. Aprire il file SimpleEventProcessor.cs e aggiungere le istruzioni `using` seguenti all'inizio del file.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementare l'interfaccia `IEventProcessor`. Sostituire l'intero contenuto della classe `SimpleEventProcessor` con il codice seguente:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aggiornare il metodo Main per l'uso di SimpleEventProcessor

1. Aggiungere le istruzioni `using` seguenti all'inizio del file Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Aggiungere le costanti alla classe `Program` per la stringa di connessione dell'hub eventi, il nome dell'hub, il nome del contenitore dell'account di archiviazione, il nome dell'account di archiviazione e la chiave dell'account di archiviazione. Aggiungere il codice seguente, sostituendo i segnaposto con i relativi valori:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Aggiungere alla classe `Program` un nuovo metodo denominato `MainAsync` come da esempio seguente:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Aggiungere la riga di codice seguente al metodo `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Ecco l'aspetto che avrà il file Program.cs:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Eseguire il programma e assicurarsi che non siano presenti errori.


## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Le funzionalità e la terminologia nell'hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)


