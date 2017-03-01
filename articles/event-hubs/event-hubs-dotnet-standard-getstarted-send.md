---
title: Inviare eventi a Hub eventi di Azure usando .NET Standard | Microsoft Docs
description: Guida introduttiva all&quot;invio di eventi a Hub eventi in .NET Standard
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
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 57175ddc53d5856cd3492d4c631a92d4bf9247c4
ms.openlocfilehash: a6c5ff034450c9c6a01feb4ae6d84cebd75a5682
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Guida introduttiva all'invio di messaggi a Hub eventi in .NET Standard

> [!NOTE]
> Questo esempio è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender).

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione

In questa esercitazione viene illustrato come creare la soluzione esistente **SampleSender** (all'interno della cartella). È possibile eseguire la soluzione così com'è, sostituendo le stringhe `EhConnectionString`, `EhEntityPath` e `StorageAccount` con i valori di Hub eventi o seguire questa esercitazione per crearne una propria.

In questa esercitazione si scriverà un'applicazione console .NET Core per inviare messaggi a un Hub eventi.

## <a name="prerequisites"></a>Prerequisiti

1. [Visual Studio 2015](http://www.visualstudio.com).

2. [Strumenti di Visual Studio 2015 per .NET Core](http://www.microsoft.com/net/core).

3. Una sottoscrizione di Azure.

4. Uno spazio dei nomi di Hub eventi.

## <a name="send-messages-to-an-event-hub"></a>Inviare messaggi a un Hub eventi

Per inviare messaggi a un Hub eventi, si scriverà un'applicazione console C# in Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console

* Avviare Visual Studio e creare una nuova applicazione console .NET Core.

### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

* Aggiungere il pacchetto NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) al progetto.

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Scrivere il codice per inviare messaggi all'Hub eventi

1. Aggiungere l'istruzione `using` seguente all'inizio del file Program.cs.

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. Aggiungere le costanti per la classe `Program` per la stringa di connessione e il percorso dell'entità di Hub eventi (nome dell'Hub eventi singolo). Sostituire i segnaposto tra parentesi con i valori specifici ottenuti durante la creazione di Hub eventi.

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Aggiungere alla classe `Program` un nuovo metodo denominato `MainAsync` come il seguente:

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. Aggiungere alla classe `Program` un nuovo metodo denominato `SendMessagesToEventHub` come il seguente:

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
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

5. Aggiungere il codice seguente al metodo `Main` nella classe `Program`.

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Ecco l'aspetto che avrà il file Program.cs.

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
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
  
Congratulazioni. È stato appena inviato un messaggio a un Hub eventi.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Receive events from Event Hubs](event-hubs-dotnet-standard-getstarted-receive-eph.md) (Ricezione di eventi dall'Hub eventi)
* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
