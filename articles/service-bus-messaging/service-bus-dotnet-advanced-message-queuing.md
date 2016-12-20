---
title: Come usare AMQP 1.0 con l&quot;API del bus di servizio .NET | Microsoft Docs
description: Informazioni sull&quot;uso del protocollo AMQP (Advanced Message Queuing Protocol) 1.0 con API .NET di Bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 79c7b2f2-e962-4fb4-8cc8-79d927ba55e6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/29/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 79e2e916747aee0feaddfec7ad87efc1485e9bc7


---
# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Come usare AMQP 1.0 con l'API .NET del bus di servizio
AMQP (Advanced Message Queuing Protocol) 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere usato per creare potenti applicazioni di messaggistica multipiattaforma.

Grazie al supporto per AMQP 1.0 nel bus di servizio, è ora possibile utilizzare le funzionalità di accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione da numerose piattaforme, tramite un efficiente protocollo binario. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

In questo articolo viene illustrato come usare le funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) da applicazioni .NET tramite l'API .NET del bus di servizio. È disponibile un [articolo complementare](service-bus-java-how-to-use-jms-api-amqp.md) che illustra come eseguire le stesse procedure usando l'interfaccia API di Java Message Service (JMS) standard. È possibile consultare queste due guide per acquisire informazioni sulla messaggistica multipiattaforma con AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introduzione al bus di servizio
In questo articolo si presuppone che si disponga già di uno spazio dei nomi del bus di servizio contenente una coda denominata "queue1". In caso contrario, è necessario creare lo spazio dei nomi e la coda tramite il [portale di Azure][portale di Azure]. Per altre informazioni su come creare spazi dei nomi e code del bus di servizio, vedere [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-azure-portal).

## <a name="download-the-service-bus-sdk"></a>Scaricare l’SDK del bus di servizio
Il supporto per AMQP 1.0 è disponibile nell'SDK del bus di servizio versione 2.1 o successiva. È possibile scaricare la versione più recente da NuGet all'indirizzo [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Codificare le applicazioni .NET
Per impostazione predefinita, la libreria client .NET del bus di servizio comunica con il bus di servizio tramite un protocollo dedicato basato su SOAP. Per usare AMQP 1.0 anziché il protocollo predefinito, è necessaria una configurazione esplicita nella stringa di connessione del bus di servizio, come illustrato nella sezione successiva. A parte questa modifica, il codice dell'applicazione rimane essenzialmente invariato durante l'uso di AMQP 1.0.

Nella versione corrente alcune funzionalità API non sono supportate se si usa AMQP. Queste funzionalità non supportate sono elencate più avanti nella sezione [Funzionalità non supportate e restrizioni](#unsupported-features-and-restrictions). Anche alcune impostazioni di configurazione avanzate assumono un significato differente quando si utilizza AMQP. In questo articolo non viene usata nessuna di queste impostazioni, ma altri dettagli sono disponibili in [Panoramica di AMQP per il bus di servizio](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### <a name="configure-via-appconfig"></a>Configurare tramite App.config
È consigliabile usare il file di configurazione App.config per l'archiviazione delle impostazioni delle applicazioni. Per le applicazioni del bus di servizio, è possibile usare App.config per archiviare **ConnectionString** del bus di servizio. Questa applicazione di esempio usa App.config anche per archiviare il nome dell'entità di messaggistica del bus di servizio usata.

Di seguito è riportato un file App.config di esempio:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
      <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
                value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurare la stringa di connessione del bus di servizio
Il valore dell'impostazione **Microsoft.ServiceBus.ConnectionString** corrisponde alla stringa di connessione del bus di servizio usata per configurare la connessione al bus di servizio. Il formato è il seguente:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Dove `[namespace]` e `[SAS key]` vengono ottenuti dal [portale di Azure][portale di Azure]. Per altre informazioni, vedere Come usare le code del bus di servizio.

Quando si usa AMQP, la stringa di connessione viene aggiunta alla fine di `;TransportType=Amqp`, che indica alla libreria client di effettuare questa connessione con il bus di servizio tramite AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurare il nome di entità
Questa applicazione di esempio usa l'impostazione `EntityName` nella sezione **appSettings** del file App.config per configurare il nome della coda con cui l'applicazione scambia messaggi.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Semplice applicazione .NET che usa la coda del bus di servizio
Nell'esempio seguente vengono scambiati messaggi con una coda del bus di servizio:

```
// SimpleSenderReceiver.cs

using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;

        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;

                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();

                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");

                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }

        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);

            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }

        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }

        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = "
                + message.MessageId);
        }

    }

    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }

        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message =
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " +
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }

        public void RequestStop()
        {
            _shouldStop = true;
        }

        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Eseguire l'applicazione
L'esecuzione dell'applicazione produce un output nel formato seguente:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf

Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06

Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Messaggistica multipiattaforma tra JMS e .NET
In questo argomento è stato illustrato come inviare messaggi al bus di servizio e come riceverne tramite .NET. Tuttavia, uno dei principali vantaggi di AMQP 1.0 è che consente di creare applicazioni da componenti scritti in linguaggi diversi, con i messaggi scambiati in modo affidabile e con la massima fedeltà.

Usando l'applicazione .NET di esempio descritta in precedenza e un'applicazione Java simile presa dalla guida complementare, [Come usare l'interfaccia API di JMS (Java Message Service) con il bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), è possibile scambiare messaggi tra .NET e Java.

Per altre informazioni sulla messaggistica multipiattaforma che usa il bus di servizio e AMQP 1.0, vedere la [panoramica di AMQP 1.0 per il bus di servizio](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>Da JMS a .NET
Per verificare la messaggistica da JMS a .NET, eseguire la procedura seguente:

* Avviare l'applicazione di esempio .NET senza argomenti della riga di comando.
* Avviare l'applicazione di esempio Java con l'argomento della riga di comando "sendonly". In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
* Premere alcune volte **INVIO** nella console dell'applicazione Java per inviare i messaggi.
* Questi messaggi vengono ricevuti dall'applicazione .NET.

### <a name="output-from-jms-application"></a>Output dell'applicazione JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Output dell'applicazione .NET
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>Da .NET a JMS
Per verificare la messaggistica da .NET a JMS, eseguire la procedura seguente:

* Avviare l'applicazione di esempio .NET con l'argomento "sendonly" della riga di comando. In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
* Avviare l'applicazione di esempio Java senza argomenti della riga di comando.
* Premere alcune volte **INVIO** nella console dell'applicazione .NET per generare l'invio di messaggi.
* Questi messaggi vengono ricevuti dall'applicazione Java.

#### <a name="output-from-net-application"></a>Output dell'applicazione .NET
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Output dell'applicazione JMS
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Funzionalità non supportate e restrizioni
Le seguenti funzionalità dell'API .NET di Service Bus non sono al momento supportate con AMQP:

* Transazioni
* Invio tramite destinazione del trasferimento

Per altre informazioni, vedere [Funzionalità non supportate, restrizioni e differenze di comportamento](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Riepilogo
Questo articolo illustra come accedere alle funzionalità di messaggistica negoziata del bus di servizio (code e pubblicazione/sottoscrizione di argomenti) da applicazioni .NET tramite AMQP 1.0 e l'API .NET del bus di servizio.

È anche possibile usare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui Java, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie all’utilizzo di AMQP 1.0 nel bus di servizio. Per altre informazioni, vedere la [panoramica di AMQP per il bus di servizio](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata letta una panoramica del bus di servizio e di AMQP con .NET, vedere i collegamenti seguenti per altre informazioni.

* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Come usare l'interfaccia API di JMS (Java Message Service) con il bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

[portale di Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


