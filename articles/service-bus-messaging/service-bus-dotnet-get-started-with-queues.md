<properties
    pageTitle="Introduzione alle code del bus di servizio | Microsoft Azure"
    description="Come scrivere un'applicazione console C# per la messaggistica del bus di servizio"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>  

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>  

# Introduzione alle code del bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## Contenuto dell'esercitazione

In questa esercitazione si completeranno questi passaggi:

1. Creare uno spazio dei nomi del bus di servizio usando il portale di Azure.

2. Creare una coda di messaggistica del bus di servizio usando il portale di Azure.

3. Scrivere un'applicazione console per inviare un messaggio.

4. Scrivere un'applicazione console per ricevere messaggi.

## Prerequisiti

1. [Visual Studio 2013 o Visual Studio 2015](http://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2015.

2. Una sottoscrizione di Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1\. Creare uno spazio dei nomi tramite il portale di Azure

Se è già stato creato uno spazio dei nomi del bus di servizio, passare alla sezione [Creare una coda usando il portale di Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2\. Creare una coda usando il portale di Azure

Se è già stata creata una coda del bus di servizio, passare alla sezione [Inviare messaggi alla coda](#3-send-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3\. Inviare messaggi alla coda

Per inviare messaggi alla coda, si scriverà un'applicazione console C# in Visual Studio.

### Creare un'applicazione console

1. Avviare Visual Studio e creare una nuova applicazione console.

### Aggiungere il pacchetto NuGet del bus di servizio

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.

2. Fare clic sulla scheda **Esplora**, quindi cercare "Bus di servizio di Microsoft Azure" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

    ![Selezionare un pacchetto NuGet][nuget-pkg]

### Scrivere il codice per inviare un messaggio alla coda

1. Aggiungere l'istruzione using seguente all'inizio del file Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Aggiungere il codice seguente al metodo `Main`, impostare la variabile **connectionString** sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare **queueName** sul nome della coda usato durante la creazione della coda.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Ecco l'aspetto che avrà il file Program.cs.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. Eseguire il programma e verificare il portale di Azure. Fare clic sul nome della coda nel pannello **Panoramica** dello spazio dei nomi. Si noti che ora il valore di **Numero di messaggi attivi** è 1.
    
      ![Numero messaggi][queue-message]  
    
## 4\. Ricezione di messaggi dalla coda

1. Creare una nuova applicazione console e aggiungere un riferimento al pacchetto NuGet del bus di servizio, come per l'applicazione di invio precedente.

2. Aggiungere l'istruzione `using` seguente all'inizio del file Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Aggiungere il codice seguente al metodo `Main`, impostare la variabile **connectionString** sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e impostare **queueName** sul nome della coda usato durante la creazione della coda.

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

	Ecco l'aspetto che avrà il file Program.cs:

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. Eseguire il programma e verificare il portale. Si noti che ora il valore in **Lunghezza coda** è 0.

    ![Lunghezza coda][queue-message-receive]
  
Congratulazioni. È stata creata una coda ed è stato inviato e ricevuto un messaggio.

## Passaggi successivi

Vedere il [repository GitHub con esempi](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) che illustrano alcune delle funzionalità più avanzate della messaggistica del bus di servizio di Azure.

<!--Image references-->  

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->