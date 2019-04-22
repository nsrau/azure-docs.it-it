---
title: Inviare e ricevere eventi usando .NET Framework - hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione .NET Framework che invia eventi all'hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 0cccf6f6187f894faadbe4f572d75c483638aafd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679245"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Inviare eventi a o ricevere eventi da hub eventi di Azure usando .NET Framework
Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione illustra come creare applicazioni console .NET Framework in C# per inviare eventi a o ricevere eventi da un hub eventi. 

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- [Microsoft Visual Studio 2017 o versione successiva](https://visualstudio.com).
- **Creare uno spazio dei nomi di hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Quindi, ottenere il **stringa di connessione per lo spazio dei nomi dell'hub eventi** seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.

## <a name="send-events"></a>Inviare eventi 
Questa sezione illustra come creare un'applicazione console .NET Framework per inviare eventi a un hub eventi. 

### <a name="create-a-console-application"></a>Creare un'applicazione console

In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **Sender**.
   
![Creare un'applicazione console](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Sender**, quindi scegliere **Gestisci pacchetti NuGet per la soluzione**. 
2. Fare clic sulla scheda **Sfoglia** e quindi cercare `WindowsAzure.ServiceBus`. Fare clic su **Installa**e accettare le condizioni per l'utilizzo. 
   
    ![Installare il pacchetto NuGet del bus di servizio](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio scarica e installa il [pacchetto NuGet delle librerie del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus)e aggiunge un riferimento al pacchetto.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori segnaposto con il nome dell'hub eventi creato nella sezione precedente e la stringa di connessione a livello di spazio dei nomi salvata in precedenza. È possibile copiare la stringa di connessione per l'hub eventi dalla **Stringa di connessione - chiave primaria** in **RootManageSharedAccessKey** nella pagina dell'Hub eventi nel portale di Azure. Per informazioni dettagliate, vedere [Get connection string](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) (Ottenere la stringa di connessione).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Aggiungere il metodo seguente alla classe **Program** :
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Questo metodo invia continuamente eventi all'hub eventi con un ritardo di 200 millisecondi.
4. Aggiungere infine le righe seguenti al metodo **Main** :
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Eseguire il programma e assicurarsi che non siano presenti errori.
  
## <a name="receive-events"></a>Ricevere eventi
In questa sezione si scrive un'applicazione console .NET Framework che riceve i messaggi da un hub eventi usando il [Host processore di eventi](event-hubs-event-processor-host.md). L'[host processore di eventi](event-hubs-event-processor-host.md) è una classe .NET che semplifica la ricezione di eventi dagli hub eventi gestendo checkpoint persistenti e ricezioni parallele da tali hub. Usando l'host processore di eventi è possibile suddividere gli eventi su più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare l'host processore di eventi per un ricevitore singolo. L'esempio di [elaborazione di eventi con aumento del numero di istanze][Scale out Event Processing with Event Hubs] illustra come usare l'host processore di eventi con più ricevitori.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Creare un'applicazione console

In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **Receiver**.
   
![Creare un'applicazione console](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Receiver**, quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.
2. Fare clic sulla scheda **Sfoglia** e quindi cercare `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Fare clic su **Installa**e accettare le condizioni per l'utilizzo.
   
    ![Ricerca del pacchetto NuGet dell'host processore di eventi](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio scarica e installa il [pacchetto NuGet Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)e aggiunge un riferimento al pacchetto con tutte le relative dipendenze.

### <a name="implement-the-ieventprocessor-interface"></a>Implementare l'interfaccia IEventProcessor

1. Fare clic con il pulsante destro del mouse sul progetto **Receiver**, scegliere **Aggiungi** e quindi **Classe**. Assegnare alla nuova classe il nome **SimpleEventProcessor** e quindi fare clic su **Aggiungi** per crearla.
   
    ![Aggiungere la classe SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Aggiungere le istruzioni seguenti all'inizio del file SimpleEventProcessor.cs:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Sostituire il corpo della classe con il codice seguente:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Questa classe è chiamata da **EventProcessorHost** per elaborare gli eventi ricevuti dall'hub eventi. La classe `SimpleEventProcessor` usa un cronometro per chiamare periodicamente il metodo checkpoint sul contesto di **EventProcessorHost**. Questa elaborazione assicura che, se il ricevitore viene riavviato, non perde più di cinque minuti di lavoro di elaborazione.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aggiornare il metodo Main per l'uso di SimpleEventProcessor

1. Nella classe **Program** aggiungere l'istruzione `using` seguente all'inizio del file:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Sostituire quindi il metodo `Main` nella classe `Program` con il codice seguente, sostituendo il nome dell'hub eventi e la stringa di connessione a livello di spazio dei nomi salvata in precedenza, nonché l'account di archiviazione e la chiave copiata nelle sezioni precedenti. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Eseguire il programma e assicurarsi che non siano presenti errori.
  
## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Le funzionalità e la terminologia nell'hub eventi di Azure](event-hubs-features.md).
- [Domande frequenti su Hub eventi](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
