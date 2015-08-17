<properties 
	pageTitle="Come usare AMQP 1.0 con l'API .NET del bus di servizio - Azure" 
	description="Informazioni sull'uso del protocollo AMQP (Advanced Message Queuing Protocol) 1.0 con API .NET di Bus di servizio di Azure." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="sethm"/>

# Come usare AMQP 1.0 con l'API .NET del bus di servizio

AMQP (Advanced Message Queuing Protocol) 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere usato per creare potenti applicazioni di messaggistica multipiattaforma.

Grazie al supporto per AMQP 1.0 nel bus di servizio, è ora possibile usare le funzionalità di accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione da numerose piattaforme, tramite un efficiente protocollo binario. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

In questa guida dettagliata viene illustrato come usare le funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) da applicazioni .NET tramite l'API .NET di Service Bus. È disponibile una guida complementare in cui viene illustrato come eseguire le stesse procedure usando l'API Java Message Service (JMS) standard. È possibile consultare queste due guide per acquisire informazioni sulla messaggistica multipiattaforma con AMQP 1.0.

## Introduzione al bus di servizio

In questa guida si presuppone che si disponga già di uno spazio dei nomi del bus di servizio contenente una coda denominata "queue1". In caso contrario, è necessario creare lo spazio dei nomi e la coda tramite il [portale di gestione di Azure](http://manage.windowsazure.com). Per altre informazioni su come creare spazi dei nomi e code del bus di servizio, vedere la guida dettagliata "[Come usare le code del bus di servizio](https://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)".

## Scaricare l’SDK del bus di servizio

Il supporto per AMQP 1.0 è disponibile nell'SDK del bus di servizio versione 2.1 o successiva. È possibile scaricare la versione più recente dell'SDK da NuGet all'indirizzo [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## Codificare le applicazioni .NET

Per impostazione predefinita, la libreria client .NET del bus di servizio comunica con il bus di servizio tramite un protocollo dedicato basato su SOAP. Per usare AMQP 1.0 anziché il protocollo predefinito, è necessaria una configurazione esplicita nella stringa di connessione del bus di servizio, come illustrato nella sezione successiva. A parte questa modifica, il codice dell'applicazione rimane essenzialmente invariato durante l'uso di AMQP 1.0.

Nella versione corrente alcune funzionalità API non sono supportate con l'uso di AMQP. Tali funzionalità sono elencate più avanti nella sezione "Funzionalità non supportate e restrizioni". Anche alcune impostazioni di configurazione avanzate assumono un significato differente quando si utilizza AMQP. In questa breve guida queste impostazioni non vengono utilizzate, ma ulteriori dettagli sono disponibili nella [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/library/jj841071.aspx).

### Configurare tramite App.config

È consigliabile usare il file di configurazione App.config per l'archiviazione delle impostazioni delle applicazioni. Per le applicazioni del bus di servizio, è possibile usare App.config per archiviare la stringa **ConnectionString** del bus di servizio. Questa applicazione di esempio usa App.config anche per archiviare il nome dell'entità di messaggistica del bus di servizio usata.

Di seguito è riportato un file App.config di esempio:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  	<appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
        	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
  		</appSettings>
	</configuration>

### Configurare la stringa di connessione del bus di servizio

Il valore dell'impostazione **Microsoft.ServiceBus.ConnectionString** corrisponde alla stringa di connessione del bus di servizio usata per configurare la connessione con il bus di servizio. Il formato è il seguente:

	Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Dove [spazio dei nomi] e [chiave di firma di accesso condiviso] si ottengono dal portale di gestione di Azure. Per altre informazioni, vedere [Come usare le code del bus di servizio][].

Quando si usa AMQP, la stringa di connessione viene aggiunta alla fine di ";TransportType=Amqp", che indica alla libreria client di effettuare questa connessione con il bus di servizio tramite AMQP 1.0.

### Configurare il nome di entità

In questa applicazione di esempio viene usata l'impostazione `EntityName` nella sezione **appSettings** del file App.config per configurare il nome della coda con cui l'applicazione scambia messaggi.

### Semplice applicazione .NET che usa la coda del bus di servizio

Nel seguente esempio vengono scambiati messaggi con una coda del bus di servizio:

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
	            BrokeredMessage message = 
	                new BrokeredMessage("Test AMQP message from .NET");
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

### Eseguire l'applicazione

L'esecuzione dell'applicazione produce un output nel formato seguente:

	> SimpleSenderReceiver.exe
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
	Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
	Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
	Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
	Received message with MessageID = f27f79ec124548c196fd0db8544bca49
	Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
	exit

## Messaggistica multipiattaforma tra JMS e .NET

In questa guida è stato illustrato come inviare messaggi al bus di sevizio e come riceverne tramite .NET. Tuttavia, uno dei principali vantaggi di AMQP 1.0 è che consente di creare applicazioni da componenti scritti in linguaggi diversi, con i messaggi scambiati in modo affidabile e con la massima fedeltà.

Utilizzando l'applicazione .NET di esempio descritta in precedenza e un'applicazione Java simile presa dalla guida complementare, [Come utilizzare l'API JMS (Java Message Service) con Service Bus e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), è possibile scambiare messaggi tra .NET e Java.

Per ulteriori informazioni sulla messaggistica multipiattaforma con Service Bus e AMQP 1.0, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/library/azure/jj841071.aspx).

### Da JMS a .NET

Per verificare la messaggistica da JMS a .NET, eseguire la procedura seguente:

* Avviare l'applicazione di esempio .NET senza argomenti della riga di comando.
* Avviare l'applicazione di esempio Java con l'argomento della riga di comando "sendonly". In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
* Premere alcune volte **INVIO** nella console dell'applicazione Java per generare l'invio di messaggi.
* Questi messaggi vengono ricevuti dall'applicazione .NET.

### Output dell'applicazione JMS

	> java SimpleSenderReceiver sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with JMSMessageID = ID:4364096528752411591
	Sent message with JMSMessageID = ID:459252991689389983
	Sent message with JMSMessageID = ID:1565011046230456854
	exit

### Output dell'applicazione .NET

	> SimpleSenderReceiver.exe	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with MessageID = 4364096528752411591
	Received message with MessageID = 459252991689389983
	Received message with MessageID = 1565011046230456854
	exit

## Da .NET a JMS

Per verificare la messaggistica da .NET a JMS, eseguire la procedura seguente:

* Avviare l'applicazione di esempio .NET con l'argomento "sendonly" della riga di comando. In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
* Avviare l'applicazione di esempio Java senza argomenti della riga di comando.
* Premere alcune volte **INVIO** nella console dell'applicazione .NET per generare l'invio di messaggi.
* Questi messaggi vengono ricevuti dall'applicazione Java.

#### Output dell'applicazione .NET

	> SimpleSenderReceiver.exe sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


#### Output dell'applicazione JMS

	> java SimpleSenderReceiver	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

## Funzionalità non supportate e restrizioni

Le seguenti funzionalità dell'API .NET di Service Bus non sono al momento supportate con AMQP:

* Transazioni
* Invio tramite destinazione del trasferimento
* Ricezione in base al numero di sequenza del messaggio
* Esplorazione di messaggi e sessioni
* Stato sessione
* API basate su batch
* Ricezione con scalabilità orizzontale
* Manipolazione in runtime delle regole di sottoscrizione
* Rinnovo del blocco della sessione
* Alcune piccole differenze nel comportamento

Per ulteriori informazioni, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/library/azure/jj841071.aspx). In questo argomento è riportato un elenco dettagliato delle API non supportate.

## Riepilogo

In questa guida dettagliata è stato illustrato come accedere alle funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) da applicazioni .NET tramite AMQP1.0 e l'API .NET del bus di servizio.

È anche possibile usare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui Java, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie all’utilizzo di AMQP 1.0 nel bus di servizio. Per ulteriori informazioni, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/library/azure/jj841071.aspx).

## Passaggi successivi

* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Come usare l'API JMS (Java Message Service) con il bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [AMQP 1.0 per il bus di servizio: Guida per gli sviluppatori](http://msdn.microsoft.com/library/azure/jj841071.aspx)
* [Come usare le code del bus di servizio](service-bus-dotnet-how-to-use-queues.md)
 

<!---HONumber=August15_HO6-->