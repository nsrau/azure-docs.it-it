---
title: Inviare eventi a Hub eventi di Azure usando .NET Framework | Microsoft Docs
description: Iniziare a inviare eventi a Hub eventi usando .NET Framework
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
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: ae5d89aab4ce1bd599ed9a50dc46336f8a96a2f5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456225"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Inviare eventi a Hub eventi di Azure usando .NET Framework
Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione illustra come inviare eventi a un hub eventi usando un'applicazione console scritta in C# con .NET Framework. 

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* [Microsoft Visual Studio 2017 o versione successiva](http://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi
Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi procedere con i passaggi seguenti di questa esercitazione.

## <a name="create-a-console-application"></a>Creare un'applicazione console

In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **Sender**.
   
![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Sender**, quindi scegliere **Gestisci pacchetti NuGet per la soluzione**. 
2. Fare clic sulla scheda **Sfoglia** e quindi cercare `WindowsAzure.ServiceBus`. Fare clic su **Installa**e accettare le condizioni per l'utilizzo. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio scarica e installa il [pacchetto NuGet delle librerie del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus)e aggiunge un riferimento al pacchetto.

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
      ```csharp
      using System.Threading;
      using Microsoft.ServiceBus.Messaging;
      ```
2. Aggiungere i campi seguenti alla classe **Program**, sostituendo i valori segnaposto con il nome dell'hub eventi creato nella sezione precedente e la stringa di connessione a livello di spazio dei nomi salvata in precedenza.
   
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
  
Congratulazioni. Sono stati inviati messaggi a un hub eventi.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si sono inviati messaggi a un hub eventi usando .NET Framework. Per informazioni su come ricevere eventi da un hub eventi usando .NET Framework, vedere [Ricevere eventi da Hub eventi di Azure usando .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

