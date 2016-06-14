<properties
   pageTitle="Introduzione alle code del bus di servizio | Microsoft Azure"
   description="Come scrivere un'applicazione console C# per la messaggistica del bus di servizio"
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Introduzione alle code del bus di servizio
[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

##Contenuto dell'esercitazione
In questa esercitazione si completeranno questi passaggi:

1. Creare uno spazio dei nomi del bus di servizio usando il portale di Azure.

2. Creare una coda di messaggistica del bus di servizio usando il portale di Azure.

3. Scrivere un'applicazione console per inviare un messaggio.

4. Scrivere un'applicazione console per ricevere messaggi.

##Prerequisiti
1. [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com)

2. Una sottoscrizione di Azure

##1\. Creare uno spazio dei nomi tramite il portale di Azure
Se è già stato creato uno spazio dei nomi del bus di servizio, passare alla sezione [Creare una coda usando il portale di Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

##2\. Creare una coda usando il portale di Azure
Se è già stata creata una coda del bus di servizio, passare alla sezione [Invio di messaggi alla coda](#3-sending-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

##3\. Invio di messaggi alla coda
Per inviare messaggi alla coda, si scriverà un'applicazione console C# in Visual Studio.

###Creare un'applicazione console
1. Avviare Visual Studio e creare una nuova applicazione console.

###Aggiungere il pacchetto NuGet del bus di servizio
1. Fare clic con il pulsante destro del mouse sul progetto appena creato e selezionare **Gestisci pacchetti NuGet**.

2. Cercare "Bus di servizio di Microsoft Azure" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

    ![Selezionare un pacchetto NuGet][1]

###Scrivere il codice per inviare un messaggio alla coda
1. Aggiungere l'istruzione using seguente all'inizio del file Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Aggiungere il codice seguente al metodo Main e impostare la variabile **connectionString** sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e **queueName** sul nome della coda usato durante la creazione della coda.

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
  
3. Eseguire il programma e verificare il portale di Azure. Si noti che ora il valore in **Lunghezza coda** è 1.
    
      ![Lunghezza coda][2]
    
##4\. Ricezione di messaggi dalla coda
1. Creare una nuova applicazione console e aggiungere un riferimento al pacchetto NuGet del bus di servizio, come è stato fatto per l'applicazione di invio precedente.

2. Aggiungere l'istruzione using seguente all'inizio del file Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Aggiungere il codice seguente al metodo Main e impostare la variabile **connectionString** sulla stessa stringa di connessione ottenuta al momento della creazione dello spazio dei nomi e **queueName** sul nome della coda usato durante la creazione della coda.

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
  
4. Eseguire il programma e verificare il portale di Azure. Si noti che ora il valore in **Lunghezza coda** è 0.

    ![Lunghezza coda][3]
  
Congratulazioni. È stata creata una coda ed è stato inviato e ricevuto un messaggio.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##Passaggi successivi

Per esempi che illustrano alcune delle funzionalità più avanzate di messaggistica del bus di servizio di Azure, vedere il repository GitHub. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

<!--Image references-->

[1]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[2]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-send.png
[3]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->