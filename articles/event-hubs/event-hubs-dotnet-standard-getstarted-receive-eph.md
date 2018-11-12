---
title: Ricevere eventi da Hub eventi di Azure usando la libreria .NET Standard | Microsoft Docs
description: Guida introduttiva alla ricezione di messaggi con EventProcessorHost in .NET Standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 9adbd8b9e7934ebe454d14ac6e47fe96898c9184
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234392"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Guida introduttiva alla ricezione di messaggi con Event Processor Host in .NET Standard
Hub eventi è un servizio che consente di elaborare grandi quantità di dati di telemetria sugli eventi da applicazioni e dispositivi connessi. Dopo aver raccolto i dati in Hub eventi, è possibile archiviarli usando un cluster di archiviazione o trasformarli usando un provider di analisi in tempo reale. Questa funzionalità di elaborazione e raccolta di eventi su vasta scala rappresenta un componente chiave delle moderne architetture di applicazioni, tra cui Internet delle cose (IoT). Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

In questa esercitazione si scriverà un'applicazione console .NET Core per ricevere messaggi da un hub eventi usando l'[host processore di eventi](event-hubs-event-processor-host.md). L'[host processore di eventi](event-hubs-event-processor-host.md) è una classe .NET che semplifica la ricezione di eventi dagli hub eventi gestendo checkpoint persistenti e ricezioni parallele da tali hub. Usando l'host processore di eventi è possibile suddividere gli eventi su più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare l'host processore di eventi per un ricevitore singolo. L'esempio di [elaborazione di eventi con aumento del numero di istanze] [elaborazione di eventi con aumento del numero di istanze con hub eventi] illustra come usare l'host processore di eventi con più ricevitori.

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), sostituire le stringhe `EventHubConnectionString`, `EventHubName`, `StorageAccountName`, `StorageAccountKey` e `StorageContainerName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti
* [Microsoft Visual Studio 2015 o 2017](https://www.visualstudio.com). Gli esempi inclusi nell'esercitazione usano Visual Studio 2017, ma è supportato anche Visual Studio 2015.
* [Strumenti di Visual Studio 2015 o 2017 per .NET Core](https://www.microsoft.com/net/core).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi
Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi procedere con i passaggi seguenti di questa esercitazione.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**. Creare un'applicazione console di .NET Core.

![Nuovo progetto][2]

## <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

Aggiungere al progetto i pacchetti NuGet [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) della libreria .NET Standard seguendo questa procedura: 

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Sfoglia**, cercare **Microsoft.Azure.EventHubs** e quindi selezionare il pacchetto **Microsoft.Azure.EventHubs**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
3. Ripetere i passaggi 1 e 2 e installare il pacchetto **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implementare l'interfaccia IEventProcessor

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

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aggiornare il metodo Main per l'uso di SimpleEventProcessor

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

Congratulazioni! Sono stati appena ricevuti dei messaggi da un hub eventi usando l'host del processore di eventi.

> [!NOTE]
> Questa esercitazione usa una singola istanza di [EventProcessorHost](event-hubs-event-processor-host.md). Per aumentare la velocità effettiva, è consigliabile eseguire più istanze di [EventProcessorHost](event-hubs-event-processor-host.md), come illustrato nell'esempio di [elaborazione di eventi con aumento del numero di istanze](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). In questi casi, le diverse istanze si coordinano automaticamente tra loro per bilanciare il carico relativo agli eventi ricevuti. 

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata un'applicazione .NET Standard che ha ricevuto messaggi da un hub eventi. Per informazioni su come inviare eventi a un hub eventi usando .NET Standard, vedere la [Guida introduttiva all'invio di messaggi a Hub eventi di Azure in .NET Standard](event-hubs-dotnet-standard-getstarted-send.md).

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
