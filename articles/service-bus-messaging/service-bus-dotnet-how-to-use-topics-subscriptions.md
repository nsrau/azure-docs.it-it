---
title: Introduzione agli argomenti e alle sottoscrizioni del bus di servizio di Azure | Microsoft Docs
description: Scrivere un'applicazione console C# che usa gli argomenti e le sottoscrizioni di messaggistica del bus di servizio.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 3646d14be662af0fdf80790cb53ddc581b33a146
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-topics"></a>Introduzione agli argomenti del bus di servizio

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questa esercitazione illustra i passaggi seguenti:

1. Creare uno spazio dei nomi del bus di servizio usando il portale di Azure.
2. Creare un argomento del bus di servizio usando il portale di Azure.
3. Creare una sottoscrizione a tale argomento del bus di servizio usando il portale di Azure.
4. Scrivere un'applicazione console per inviare un messaggio all'argomento.
5. Scrivere un'applicazione console per ricevere tale messaggio dalla sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

1. [Visual Studio 2015 o versione successiva](http://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2017.
2. Una sottoscrizione di Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creare uno spazio dei nomi tramite il portale di Azure

Se è già stato creato uno spazio dei nomi di messaggistica del bus di servizio, passare alla sezione [Creare un argomento usando il portale di Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Creare un argomento usando il portale di Azure

1. Accedere al [portale di Azure][azure-portal].
2. Nel riquadro di spostamento a sinistra del portale, fare clic su **Bus di servizio** (se non viene visualizzato **Bus di servizio**, fare clic su **Altri servizi**).
3. Fare clic sullo spazio dei nomi in cui si vuole creare l'argomento. Viene visualizzato il pannello della panoramica dello spazio dei nomi:
   
    ![Creare un argomento][createtopic1]
4. Nel pannello **Spazio dei nomi del bus di servizio** fare clic su **Argomenti** e quindi su **Aggiungi argomento**.
   
    ![Selezionare gli argomenti][createtopic2]
5. Immettere un nome per l'argomento e deselezionare l'opzione **Abilita partizionamento**. Lasciare invariati i valori predefiniti delle altre opzioni.
   
    ![Selezionare Nuovo][createtopic3]
6. Fare clic su **Crea**nella parte inferiore del pannello.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Creare una sottoscrizione all'argomento

1. Nel riquadro delle risorse del portale fare clic sullo spazio dei nomi creato nel passaggio 1, quindi fare clic sul nome dell'argomento creato nel passaggio 2.
2. Nella parte superiore del riquadro della panoramica fare clic sul segno più accanto a **Sottoscrizione** per aggiungere una sottoscrizione a questo argomento.

    ![Creare la sottoscrizione][createtopic4]

3. Immettere un nome per la sottoscrizione. Lasciare invariati i valori predefiniti delle altre opzioni.

## <a name="4-send-messages-to-the-topic"></a>4. Inviare messaggi all'argomento

Per inviare messaggi all'argomento, si scrive un'applicazione console C# in Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console

Avviare Visual Studio e creare un nuovo progetto **App console (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic sulla scheda **Esplora**, cercare **WindowsAzure.ServiceBus** e quindi selezionare l'elemento **WindowsAzure.ServiceBus**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
   
    ![Selezionare un pacchetto NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>Scrivere il codice per inviare un messaggio all'argomento

1. Aggiungere l'istruzione `using` seguente all'inizio del file Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Aggiungere il codice seguente al metodo `Main` . Impostare la variabile `connectionString` sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare `topicName` sul nome usato durante la creazione dell'argomento.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Eseguire il programma e controllare il portale di Azure: fare clic sul nome dell'argomento nel pannello **Panoramica** dello spazio dei nomi. Viene visualizzato il pannello **Informazioni di base** dell'argomento. Nelle sottoscrizioni elencate nella parte inferiore del pannello si noti che il valore di **Numero di messaggi attivi** per ogni sottoscrizione ora è 1. Ogni volta che si avvia l'applicazione mittente senza recuperare i messaggi (come illustrato nella sezione successiva), questo valore aumenta di 1. Si noti anche che la dimensione corrente dell'argomento aumenta il valore di **Corrente** nel pannello **Informazioni di base** ogni volta che l'app aggiunge un messaggio all'argomento o alla sottoscrizione.
   
      ![Dimensioni dei messaggi][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Ricevere messaggi dalla sottoscrizione

1. Per ricevere uno o più messaggi inviati, creare una nuova applicazione console e aggiungere un riferimento al pacchetto NuGet del bus di servizio, come per l'applicazione mittente precedente.
2. Aggiungere l'istruzione `using` seguente all'inizio del file Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Aggiungere il codice seguente al metodo `Main` . Impostare la variabile `connectionString` sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare `topicName` sul nome usato durante la creazione dell'argomento. Assicurarsi di sostituire `<your subscription name>` con il nome della sottoscrizione creata nel passaggio 3. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    ![Lunghezza argomento][topic-message-receive]

Congratulazioni. Sono stati creati un argomento e una sottoscrizione ed è stato inviato un messaggio che è stato quindi ricevuto.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [repository GitHub con esempi](https://github.com/Azure/azure-service-bus/tree/master/samples) che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
