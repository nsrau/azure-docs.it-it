---
title: Introduzione alle code del bus di servizio di Azure | Microsoft Docs
description: Scrivere un'applicazione console C# che usa le code di messaggistica del bus di servizio.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-service-bus-queues"></a>Introduzione alle code del bus di servizio
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione
Questa esercitazione illustra i passaggi seguenti:

1. Creare uno spazio dei nomi del bus di servizio usando il portale di Azure.
2. Creare una coda del bus di servizio usando il portale di Azure.
3. Scrivere un'applicazione console per inviare un messaggio.
4. Scrivere un'applicazione console per ricevere i messaggi inviati nel passaggio precedente.

## <a name="prerequisites"></a>Prerequisiti
1. [Visual Studio 2015 o versione successiva](http://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2017.
2. Una sottoscrizione di Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creare uno spazio dei nomi tramite il portale di Azure
Se è già stato creato uno spazio dei nomi di messaggistica del bus di servizio, passare alla sezione [Creare una coda usando il portale di Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Creare una coda usando il portale di Azure
Se è già stata creata una coda del bus di servizio, passare alla sezione [Inviare messaggi alla coda](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Inviare messaggi alla coda
Per inviare messaggi alla coda, si scrive un'applicazione console C# in Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio e creare un nuovo progetto **App console (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio
1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Esplora**, cercare **Bus di servizio di Microsoft Azure** e quindi selezionare l'elemento **WindowsAzure.ServiceBus**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
   
    ![Selezionare un pacchetto NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Scrivere il codice per inviare un messaggio alla coda
1. Aggiungere l'istruzione `using` seguente all'inizio del file Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Aggiungere il codice seguente al metodo `Main` . Impostare la variabile `connectionString` sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare `queueName` sul nome della coda usato durante la creazione della coda.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Ecco l'aspetto che avrà il file Program.cs.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Eseguire il programma e controllare il portale di Azure: fare clic sul nome della coda nel pannello **Panoramica** dello spazio dei nomi. Viene visualizzato il pannello **Informazioni di base** della coda. Si noti che ora il valore di **Numero di messaggi attivi** è 1. Ogni volta che si avvia l'applicazione mittente senza recuperare i messaggi, questo valore aumenta di 1. Tenere anche presente che la dimensione corrente della coda aumenta ogni volta che l'app aggiunge un messaggio alla coda.
   
      ![Dimensioni dei messaggi][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Ricezione di messaggi dalla coda

1. Per ricevere i messaggi inviati, creare una nuova applicazione console e aggiungere un riferimento al pacchetto NuGet del bus di servizio, come per l'applicazione mittente precedente.
2. Aggiungere l'istruzione `using` seguente all'inizio del file Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Aggiungere il codice seguente al metodo `Main` . Impostare la variabile `connectionString` sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare `queueName` sul nome della coda usato durante la creazione della coda.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Ecco l'aspetto che avrà il file Program.cs:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Eseguire il programma e verificare di nuovo il portale. Si noti che ora i valori di **Numero di messaggi attivi** e di **Corrente** sono pari a 0.
   
    ![Lunghezza coda][queue-message-receive]

Congratulazioni. È stata creata una coda ed è stato inviato e ricevuto un messaggio.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [repository GitHub con esempi](https://github.com/Azure/azure-service-bus/tree/master/samples) che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
