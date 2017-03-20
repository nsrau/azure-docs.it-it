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
ms.date: 03/01/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 6a6fe5e2e706fd8ab4ee6c51cde5b54fa703688b
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Guida introduttiva all'invio di messaggi a Hub eventi in .NET Standard

> [!NOTE]
> Questo esempio è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender).

Questa esercitazione illustra come scrivere un'applicazione console .NET Core che invii un set di messaggi a un Hub eventi. È possibile eseguire la soluzione [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) così com'è, sostituendo le stringhe `EhConnectionString` e `EhEntityPath` con i valori di Hub eventi o seguire questa esercitazione per crearne una propria.

## <a name="prerequisites"></a>Prerequisiti

1. [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com). Gli esempi inclusi nell'esercitazione usano Visual Studio 2015, ma è supportato anche Visual Studio 2017.
2. [Strumenti di Visual Studio 2015 o 2017 per .NET Core](http://www.microsoft.com/net/core).
3. Una sottoscrizione di Azure.
4. Uno spazio dei nomi di Hub eventi.

Per inviare messaggi a un Hub eventi, si scriverà un'applicazione console C# in Visual Studio.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creare uno spazio dei nomi di Hub eventi e un Hub eventi

Il primo passaggio consiste nell'usare il [Portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi con tipo Hub eventi e ottenere le credenziali di gestione richieste dall'applicazione per comunicare con l'Hub eventi. Per creare uno spazio dei nomi e un Hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e procedere con i passaggi seguenti.

## <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio. Scegliere **Nuovo** dal menu File e quindi fare clic su **Progetto**. Creare un'applicazione console di .NET Core.

![][1]

## <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

Aggiungere il pacchetto NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) al progetto.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Scrivere il codice per inviare messaggi all'Hub eventi

1. Aggiungere le istruzioni `using` seguenti all'inizio del file Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    ```

2. Aggiungere le costanti per la classe `Program` per la stringa di connessione e il percorso dell'entità di Hub eventi (nome dell'Hub eventi singolo). Sostituire i segnaposto tra parentesi con i valori specifici ottenuti durante la creazione di Hub eventi.

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Aggiungere alla classe `Program` un nuovo metodo denominato `MainAsync` come da esempio seguente:

    ```csharp
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

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```
    
4. Aggiungere alla classe `Program` un nuovo metodo denominato `SendMessagesToEventHub` come il seguente:

    ```csharp
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
        
                Console.WriteLine("Press ENTER to exit.");
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

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
