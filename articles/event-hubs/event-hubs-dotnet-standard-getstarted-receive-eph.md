---
title: Ricevere eventi a Hub eventi di Azure usando .NET Standard | Microsoft Docs
description: Guida introduttiva alla ricezione di messaggi con EventProcessorHost in .NET Standard
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 7506430f4ec5522165274f05a2fd5b77e3d6252d
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Guida introduttiva alla ricezione di messaggi con Event Processor Host in .NET Standard

> [!NOTE]
> Questo esempio è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver).

In questa esercitazione si scriverà un'applicazione console .NET Core per ricevere messaggi da un Hub eventi usando **EventProcessorHost**. È possibile eseguire la soluzione [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) così com'è, sostituendo le stringhe con i valori di Hub eventi e dell'account di archiviazione o seguire questa esercitazione per crearne una propria. 

## <a name="prerequisites"></a>Prerequisiti

1. [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com). Gli esempi inclusi nell'esercitazione usano Visual Studio 2015, ma è supportato anche Visual Studio 2017.
2. [Strumenti di Visual Studio 2015 o 2017 per .NET Core](http://www.microsoft.com/net/core).
3. Una sottoscrizione di Azure.
4. Uno spazio dei nomi di Hub eventi.
5. Un account dell'Archiviazione di Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creare uno spazio dei nomi di Hub eventi e un Hub eventi  
  
Il primo passaggio consiste nell'usare il [Portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi con tipo Hub eventi e ottenere le credenziali di gestione richieste dall'applicazione per comunicare con l'Hub eventi. Per creare uno spazio dei nomi e un Hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e procedere con i passaggi seguenti.  

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure  

1. Accedere al [portale di Azure](https://portal.azure.com).  
2. Nel riquadro di spostamento sinistro del portale fare clic su **Nuovo**, quindi su **Archiviazione** e quindi su **Account di archiviazione**.  
3. Completare i campi nel pannello dell'account di archiviazione e quindi fare clic su **Crea**.
  
    ![][1]

4. Dopo aver visualizzato il messaggio **Deployments Succeeded** (Le distribuzioni sono riuscite), fare clic sul nome del nuovo account di archiviazione e nel pannello **Informazioni di base** fare clic su **BLOB**. Quando si apre il pannello **Servizio BLOB**, fare clic su **+ Contenitore** in alto. Assegnare un nome al contenitore, quindi chiudere il pannello **Servizio BLOB**.  
5. Fare clic su **Chiavi di accesso** nel pannello a sinistra e copiare il nome del contenitore di archiviazione, dell'account di archiviazione e il valore **key1**. Salvare questi valori nel Blocco note o in un'altra posizione temporanea.  
    
## <a name="create-a-console-application"></a>Creare un'applicazione console

1. Avviare Visual Studio. Scegliere **Nuovo** dal menu File e quindi fare clic su **Progetto**. Creare un'applicazione console di .NET Core.

    ![][2]

2. In Esplora soluzioni fare doppio clic sul file **project.json** per aprirlo nell'editor di Visual Studio.
3. Aggiungere la stringa `"portable-net45+win8"` alla dichiarazione `"imports"` all'interno della sezione `"frameworks`. Tale sezione dovrebbe ora avere l'aspetto seguente. Questa stringa è necessaria a causa della dipendenza da OData dell'archiviazione di Azure:

    ```json
    "frameworks": {
      "netcoreapp1.0": {
        "imports": [
          "dnxcore50",
          "portable-net45+win8"
        ]
      }
    }
    ```

4. Fare clic su **Salva tutto** nel menu File.

Si noti che questa esercitazione illustra come scrivere un'applicazione di .NET Core, ma se si desidera usare .NET Framework nella sua interezza è necessario aggiungere la linea seguente di codice al file project.json, nella sezione `"frameworks"`:

```json
"net451": {
},
``` 

## <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

* Aggiungere i pacchetti NuGet seguenti al progetto:
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

## <a name="implement-the-ieventprocessor-interface"></a>Implementare l'interfaccia IEventProcessor

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Aggiungi** e fare clic su **Classe**. Assegnare il nome **SimpleEventProcessor** alla nuova classe.

2. Aprire il file SimpleEventProcessor.cs e aggiungere le istruzioni `using` seguenti all'inizio del file.

    ```csharp
    using System.Text;
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Scrivere un metodo console principale che usi la classe SimpleEventProcessor per ricevere i messaggi

1. Aggiungere le istruzioni `using` seguenti all'inizio del file Program.cs.
  
    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Aggiungere le costanti alla classe `Program` per la stringa di connessione di Hub eventi, il nome dell'Hub, il nome del contenitore di archiviazione, il nome dell'account di archiviazione e la chiave dell'account di archiviazione. Aggiungere il codice seguente, sostituendo i segnaposto con i relativi valori.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
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
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
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
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
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
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
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
  
Congratulazioni. Sono stati appena ricevuti dei messaggi da un Hub eventi usando Event Processor Host.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
