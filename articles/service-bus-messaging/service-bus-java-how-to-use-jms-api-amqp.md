---
title: Come usare AMQP 1.0 con l'API del bus di servizio Java | Microsoft Docs
description: Come usare JMS (Java Message Service ) con il bus di servizio di Azure e il protocollo AMQP (Advanced Message Queuing Protocol) 1.0.
services: service-bus
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-the-java-message-service-(jms)-api-with-service-bus-and-amqp-1.0"></a>Come usare l'API JMS (Java Message Service) con il bus di servizio e AMQP 1.0
AMQP (Advanced Message Queuing Protocol) 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere utilizzato per creare potenti applicazioni di messaggistica multipiattaforma.

Grazie al supporto per AMQP 1.0 nel bus di servizio, è ora possibile utilizzare le funzionalità di accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione da numerose piattaforme, tramite un efficiente protocollo binario. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

Questo articolo illustra come usare le funzionalità di messaggistica del bus di servizio, ad esempio code e pubblicazione/sottoscrizione di argomenti, da applicazioni Java usando l'API standard JMS più richiesta (Java Message Service). È disponibile un [articolo complementare](service-bus-dotnet-advanced-message-queuing.md) che illustra come eseguire le stesse procedure usando l'API . NET del bus di servizio. È possibile consultare queste due guide per acquisire informazioni sulla messaggistica multipiattaforma con AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introduzione al bus di servizio
In questa guida si presuppone di avere già uno spazio dei nomi del bus di servizio contenente una coda denominata **coda1**. In caso contrario, è necessario [creare lo spazio dei nomi e la coda](service-bus-create-namespace-portal.md) tramite il [portale di Azure](https://portal.azure.com). Per altre informazioni su come creare spazi dei nomi e code del bus di servizio, vedere la pagine contenente le informazioni su [come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Le code e gli argomenti partizionati supportano anche AMQP. Per altre informazioni, vedere le [entità di messaggistica partizionate](service-bus-partitioning.md) e [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-1.0-jms-client-library"></a>Download della libreria client JMS basata su AMQP 1.0
Per informazioni su dove scaricare l'ultima versione della libreria client JMS basata su AMQP 1.0 di Apache Qpid, visitare la pagina [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

È necessario aggiungere i seguenti quattro file JAR dall'archivio di distribuzione AMQP 1.0 di Apache Qpid al CLASSPATH Java durante la compilazione e l'esecuzione di applicazioni JMS con il bus di servizio:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-amqp-1-0-client-[version].jar
* qpid-amqp-1-0-client-jms-[version].jar
* qpid-amqp-1-0-common-[version].jar

## <a name="coding-java-applications"></a>Compilazione di applicazioni Java
### <a name="java-naming-and-directory-interface-(jndi)"></a>Java Naming and Directory Interface (JNDI)
JMS usa l'interfaccia JNDI (Java Naming and Directory Interface) per creare una separazione tra i nomi logici e i nomi fisici. Con JNDI vengono risolti due tipi di oggetti JMS: ConnectionFactory e Destination. JNDI utilizza un modello di provider in cui è possibile inserire diversi servizi directory per gestire le attività di risoluzione dei nomi. La libreria JMS basata su AMQP 1.0 di Apache Qpid viene fornita con un semplice provider JNDI, basato su un file delle proprietà, che viene configurato mediante un file di testo:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurare ConnectionFactory
La voce usata per definire un oggetto **ConnectionFactory** nel provider JNDI basato sul file delle proprietà Qpid è nel formato seguente:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Dove **[jndi_name]** e **[ConnectionURL]** hanno i significati seguenti:

* **[jndi_name]**: nome logico dell'oggetto ConnectionFactory. Tale nome viene risolto nell'applicazione Java mediante l'utilizzo del metodo JNDI IntialContext.lookup().
* **[ConnectionURL]**: URL che comunica alla libreria JMS le informazioni necessarie per il broker AMQP.

Il formato di **ConnectionURL** è il seguente:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Dove **[namespace]**, **[SASPolicyName]** e **[SASPolicyKey]** hanno i significati seguenti:

* **[namespace]**: spazio dei nomi del bus di servizio.
* **[SASPolicyName]**: nome del criterio della firma di accesso condiviso relativa alla coda.
* **[SASPolicyKey]**: chiave del criterio della firma di accesso condiviso relativa alla coda.

> [!NOTE]
> è necessario applicare manualmente la codifica URL alla password. Sul sito [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp) è disponibile un'utilità per codificare facilmente l'URL.
> 
> 

#### <a name="configure-destinations"></a>Configurare le destinazioni
La voce utilizzata per definire una destinazione nel provider JNDI basato sul file delle proprietà Qpid è nel formato seguente:

```
queue.[jndi_name] = [physical_name]
```

oppure

```
topic.[jndi_name] = [physical_name]
```

Dove **[jndi\_name]** e **[physical\_name]** hanno i significati seguenti:

* **[jndi_name]**: nome logico della destinazione. Tale nome viene risolto nell'applicazione Java mediante l'utilizzo del metodo JNDI IntialContext.lookup().
* **[physical_name]**: nome dell'entità del bus di servizio a cui l'applicazione invia messaggi o da cui l'applicazione riceve messaggi.

> [!NOTE]
> In caso di ricezione da una sottoscrizione a un argomento del bus di servizio, il nome fisico specificato in JNDI deve essere il nome dell'argomento. Il nome della sottoscrizione viene fornito al momento della creazione della sottoscrizione durevole nel codice dell'applicazione JMS. La [guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md) include istruzioni dettagliate sull'uso delle sottoscrizioni di argomenti del bus di servizio di JMS.
> 
> 

### <a name="write-the-jms-application"></a>Scrivere l'applicazione JMS
Non esistono API speciali oppure opzioni obbligatorie quando si utilizza JMS con il bus di servizio. Tuttavia, esistono alcune limitazioni che verranno illustrate più avanti. Come per qualsiasi applicazione JMS, la prima operazione da eseguire è la configurazione dell'ambiente JNDI, in modo da poter risolvere gli oggetti di tipo **ConnectionFactory** e le destinazioni.

#### <a name="configure-the-jndi-initialcontext"></a>Configurare il contesto JNDI iniziale
La configurazione dell'ambiente JNDI viene eseguita passando una tabella hash di informazioni di configurazione al costruttore della classe javax.naming.InitialContext. I due elementi necessari nella tabella hash sono il nome della classe della factory del contesto iniziale e l'URL del provider. Nel codice seguente viene illustrato come configurare l'ambiente JNDI per usare il provider JNDI basato sul file delle proprietà Qpid con un file delle proprietà denominato **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Semplice applicazione JMS che usa la coda del bus di servizio
L'applicazione di esempio seguente consente di inviare e ricevere messaggi di testo JMS verso e da una coda del bus di servizio con il nome JNDI logico QUEUE.

```
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Eseguire l'applicazione
L'esecuzione dell'applicazione produce un output nel formato seguente:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-.net"></a>Messaggistica multipiattaforma tra JMS e .NET
In questa guida è stato illustrato come inviare e ricevere messaggi verso e dal bus di servizio utilizzando JMS. Tuttavia, uno dei principali vantaggi di AMQP 1.0 è che consente di creare applicazioni da componenti scritti in linguaggi diversi, con i messaggi scambiati in modo affidabile e con la massima fedeltà.

Usando l'applicazione JMS di esempio descritta in precedenza e un'applicazione .NET simile presa dalla guida complementare [Come usare AMQP 1.0 con l'API .NET del bus di servizio](service-bus-dotnet-advanced-message-queuing.md), è possibile scambiare messaggi tra .NET e Java. 

Per altre informazioni dettagliate sulla messaggistica multipiattaforma con il bus di servizio e AMQP 1.0, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md).

### <a name="jms-to-.net"></a>Da JMS a .NET
Per verificare la messaggistica da JMS a .NET, eseguire la procedura seguente:

* Avviare l'applicazione di esempio .NET senza argomenti della riga di comando.
* Avviare l'applicazione di esempio Java con l'argomento della riga di comando "sendonly". In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
* Premere alcune volte **INVIO** nella console dell'applicazione Java per inviare i messaggi.
* Questi messaggi vengono ricevuti dall'applicazione .NET.

#### <a name="output-from-jms-application"></a>Output dell'applicazione JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-.net-application"></a>Output dell'applicazione .NET
```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name=".net-to-jms"></a>Da .NET a JMS
Per verificare la messaggistica da .NET a JMS, eseguire la procedura seguente:

* Avviare l'applicazione di esempio .NET con l'argomento "sendonly" della riga di comando. In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
* Avviare l'applicazione di esempio Java senza argomenti della riga di comando.
* Premere alcune volte **INVIO** nella console dell'applicazione .NET per inviare i messaggi.
* Questi messaggi vengono ricevuti dall'applicazione Java.

#### <a name="output-from-.net-application"></a>Output dell'applicazione .NET
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
Quando si utilizza JMS su AMQP 1.0 con il bus di servizio esistono le seguenti restrizioni:

* È consentito solo un oggetto **MessageProducer** o **MessageConsumer** per **sessione**. Se si vuole creare più oggetti **MessageProducers** o **MessageConsumers** in un'applicazione, creare una **sessione** dedicata per ognuno di essi.
* Le sottoscrizioni a un argomento volatile non sono attualmente supportate.
* Gli oggetti**MessageSelectors** non sono attualmente supportati.
* Le destinazioni temporanee, ad esempio **TemporaryQueue**, **TemporaryTopic**, non sono attualmente supportate, così come le API **QueueRequestor** e **TopicRequestor** da cui vengono usate.
* Le sessioni transazionali non sono supportate e le transazioni distribuite non sono supportate.

## <a name="summary"></a>Riepilogo
Questa guida dettagliata ha illustrato come accedere alle funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) del bus di servizio da Java usando la diffusa API JMS e AMQP 1.0.

È anche possibile utilizzare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui .NET, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie al supporto per AMQP 1.0 nel bus di servizio. Per altre informazioni, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi
* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Come usare AMQP 1.0 con l'API .NET del bus di servizio](service-bus-dotnet-advanced-message-queuing.md)
* [Guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md)
* [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Centro per sviluppatori Java](/develop/java/).

<!--HONumber=Oct16_HO2-->


