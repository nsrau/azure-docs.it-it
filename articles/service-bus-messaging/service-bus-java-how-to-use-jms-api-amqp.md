---
title: Usare AMQP con l'API del servizio di messaggistica Java e il bus di servizio di Azure
description: Usare Java Message Service (JMS) con il bus di servizio di Azure e il Advance Message Queueing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086692"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Usare il servizio messaggi Java con il bus di servizio di Azure e AMQP 1,0

> [!WARNING]
> Questo articolo illustra il *supporto limitato* per l'API JMS (Java Message Service) 1,1 ed esiste solo per il livello standard del bus di servizio di Azure.
>
> Il supporto completo per l'API Java Message Service 2,0 è disponibile solo nel [livello Premium del bus di servizio di Azure in anteprima](how-to-use-java-message-service-20.md). Si consiglia di usare questo livello.
>

Questo articolo illustra come usare le funzionalità di messaggistica del bus di servizio da applicazioni Java usando il noto standard dell'API JMS. Queste funzionalità di messaggistica includono le code e la pubblicazione o la sottoscrizione di argomenti. Un [Articolo complementare](service-bus-amqp-dotnet.md) illustra come eseguire la stessa operazione usando l'API .NET del bus di servizio di Azure. È possibile usare questi due articoli insieme per informazioni sulla messaggistica multipiattaforma con il Advance Message Queueing Protocol (AMQP) 1,0.

AMQP 1,0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere usato per creare applicazioni di messaggistica multipiattaforma affidabili.

Il supporto per AMQP 1,0 nel bus di servizio significa che è possibile usare le funzionalità di Accodamento e di messaggistica negoziata di pubblicazione o sottoscrizione da una gamma di piattaforme usando un protocollo binario efficiente. È anche possibile compilare applicazioni costituite da componenti creati con una combinazione di linguaggi, Framework e sistemi operativi.

## <a name="get-started-with-service-bus"></a>Introduzione al bus di servizio

Questo articolo presuppone che sia già presente uno spazio dei nomi del bus di servizio contenente una coda denominata `basicqueue` . In caso contrario, è possibile [creare lo spazio dei nomi e la coda](service-bus-create-namespace-portal.md) usando il [portale di Azure](https://portal.azure.com). Per altre informazioni su come creare spazi dei nomi e code del bus di servizio, vedere [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Le code e gli argomenti partizionati supportano anche AMQP. Per altre informazioni, vedere le [entità di messaggistica partizionate](service-bus-partitioning.md) e [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Scaricare la libreria client JMS AMQP 1,0

Per informazioni su dove scaricare la versione più recente della libreria client di Apache Qpid JMS AMQP 1,0, vedere il [sito di download di Apache Qpid](https://qpid.apache.org/download.html).

È necessario aggiungere i file JAR seguenti dall'archivio di distribuzione di Apache Qpid JMS AMQP 1,0 alla variabile di ambiente CLASSPATH Java quando si compilano ed eseguono applicazioni JMS con il bus di servizio:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[versione].jar

> [!NOTE]
> I nomi e le versioni dei JAR JMS potrebbero essere stati modificati. Per altre informazioni, vedere [QPID JMS AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Applicazioni Java del codice

### <a name="java-naming-and-directory-interface"></a>Denominazione e interfaccia di directory Java

JMS usa l'interfaccia JNDI (Java Naming and Directory Interface) per creare una separazione tra i nomi logici e i nomi fisici. Due tipi di oggetti JMS vengono risolti tramite JNDI: **ConnectionFactory** e **Destination**. JNDI utilizza un modello di provider in cui è possibile inserire diversi servizi directory per gestire le attività di risoluzione dei nomi. La libreria JMS AMQP 1,0 di Apache Qpid viene fornita con un semplice provider JNDI basato su file di proprietà configurato usando un file delle proprietà con il formato seguente:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>Configurare il contesto JNDI e configurare l'oggetto ConnectionFactory

La stringa di connessione a cui si fa riferimento è quella disponibile nei criteri di accesso condiviso nella [portale di Azure](https://portal.azure.com) nella **stringa di connessione primaria**.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configurare le code di destinazione Producer e consumer

La voce usata per definire una destinazione nel provider JNDI del file delle proprietà qpid è nel formato seguente.

Per creare una coda di destinazione per il producer:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Per creare una coda di destinazione per il consumer:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Scrivere l'applicazione JMS

Quando si usa JMS con il bus di servizio, non sono necessarie API o opzioni speciali. Ci sono alcune restrizioni che verranno descritte in seguito. Come per qualsiasi applicazione JMS, la prima cosa necessaria è la configurazione dell'ambiente JNDI per poter risolvere un oggetto **ConnectionFactory** e le destinazioni.

#### <a name="configure-the-jndi-initialcontext-object"></a>Configurare l'oggetto JNDI contesto

L'ambiente JNDI viene configurato passando una tabella hash delle informazioni di configurazione nel costruttore della classe javax.naming.InitialContext. I due elementi necessari nella tabella hash sono il nome della classe della factory del contesto iniziale e l'URL del provider. Il codice seguente illustra come configurare l'ambiente JNDI per usare il provider JNDI basato su file delle proprietà qpid con un file di proprietà denominato **ServiceBus. Properties**.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Una semplice applicazione JMS che usa una coda del bus di servizio

Il programma di esempio seguente invia messaggi di testo JMS a una coda del bus di servizio con il nome logico JNDI della coda e riceve i messaggi.

È possibile accedere a tutte le informazioni sul codice sorgente e sulla configurazione dalla [Guida introduttiva della coda JMS degli esempi del bus di servizio di Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Eseguire l'applicazione

Passare il valore di **Stringa di connessione** in Criteri di accesso condiviso per eseguire l'applicazione.
Il seguente output è nel formato che esegue l'applicazione:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Mapping della disposizione AMQP e dell'operazione del bus di servizio

Ecco il modo in cui una disposizione AMQP si traduce in un'operazione del bus di servizio:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Argomenti di JMS e argomenti del bus di servizio

L'uso di argomenti e sottoscrizioni del bus di servizio tramite l'API JMS fornisce funzionalità di base di invio e ricezione. Si tratta di una scelta comoda quando si trasferiscono le applicazioni da altri broker di messaggi con API conformi a JMS, anche se gli argomenti del bus di servizio differiscono dagli argomenti JMS e richiedono alcune modifiche.

Gli argomenti del bus di servizio instradano i messaggi nelle sottoscrizioni denominate, condivise e durevoli gestite tramite l'interfaccia di gestione risorse di Azure, gli strumenti da riga di comando di Azure o i portale di Azure. Ogni sottoscrizione consente un massimo di 2.000 regole di selezione, ciascuna delle quali potrebbe avere una condizione di filtro e, per i filtri SQL, anche un'azione di trasformazione dei metadati. Ogni corrispondenza di condizione di filtro consente di selezionare il messaggio di input da copiare nella sottoscrizione.  

La ricezione di messaggi dalle sottoscrizioni è identica alla ricezione di messaggi dalle code. A ogni sottoscrizione è associata una coda di messaggi non recapitabili e la possibilità di inviare automaticamente i messaggi a un'altra coda o a altri argomenti.

Gli argomenti JMS consentono ai client di creare in modo dinamico Sottoscrittori non durevoli e durevoli che facoltativamente consentono di filtrare messaggi con selettori di messaggi. Queste entità non condivise non sono supportate dal bus di servizio. La sintassi della regola di filtro SQL per il bus di servizio è simile alla sintassi del selettore dei messaggi supportata da JMS.

Il lato Publisher dell'argomento JMS è compatibile con il bus di servizio, come illustrato in questo esempio, ma i sottoscrittori dinamici non lo sono. Le API JMS correlate alla topologia seguenti non sono supportate con il bus di servizio.

## <a name="unsupported-features-and-restrictions"></a>Funzionalità non supportate e restrizioni

Quando si usa JMS su AMQP 1,0 con il bus di servizio, esistono le restrizioni seguenti:

* È consentito un solo oggetto **MessageProducer** o **MessageConsumer** per sessione. Se è necessario creare più oggetti **MessageProducer** o **MessageConsumer** in un'applicazione, creare una sessione dedicata per ognuno di essi.
* Le sottoscrizioni di argomenti volatili non sono attualmente supportate.
* Gli oggetti **MessageSelector** non sono attualmente supportati.
* Le transazioni distribuite non sono supportate, ma sono supportate le sessioni transazionali.

Il bus di servizio suddivide il piano di controllo dal piano dati, quindi non supporta diverse funzioni della topologia dinamica di JMS.

| Metodo non supportato          | Sostituire con                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Creare una sottoscrizione dell'argomento che porti il selettore dei messaggi.                                |
| createDurableConsumer       | Creare una sottoscrizione dell'argomento che porti il selettore dei messaggi.                                |
| createSharedConsumer        | Gli argomenti del bus di servizio sono sempre condivisibili. Vedere la sezione "argomenti JMS e argomenti del bus di servizio".                                    |
| createSharedDurableConsumer | Gli argomenti del bus di servizio sono sempre condivisibili. Vedere la sezione "argomenti JMS e argomenti del bus di servizio".                                      |
| createTemporaryTopic        | Creare un argomento tramite l'API di gestione, gli strumenti o il portale con *AutoDeleteOnIdle* impostato su un periodo di scadenza. |
| createTopic                 | Creare un argomento tramite l'API di gestione, gli strumenti o il portale.                                         |
| unsubscribe                 | Eliminare l'API, gli strumenti o il portale di gestione degli argomenti.                                            |
| createBrowser               | Non supportato. Usare la funzionalità Visualizza () dell'API del bus di servizio.                         |
| createQueue                 | Creare una coda tramite l'API di gestione, gli strumenti o il portale.                                           | 
| createTemporaryQueue        | Creare una coda tramite l'API di gestione, gli strumenti o il portale con *AutoDeleteOnIdle* impostato su un periodo di scadenza. |
| receiveNoWait               | Usare il metodo Receive () fornito da Service Bus SDK e specificare un timeout molto basso o zero. |

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare le funzionalità di messaggistica negoziata del bus di servizio, ad esempio le code e gli argomenti di pubblicazione o sottoscrizione, da Java usando la nota API JMS e AMQP 1,0.

È anche possibile usare il bus di servizio AMQP 1,0 da altri linguaggi, ad esempio .NET, C, Python e PHP. I componenti creati con questi linguaggi diversi possono scambiare messaggi in modo affidabile e con la massima fedeltà usando il supporto di AMQP 1,0 nel bus di servizio.

## <a name="next-steps"></a>Passaggi successivi

* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Usare AMQP 1,0 con l'API .NET del bus di servizio](./service-bus-amqp-dotnet.md)
* [Guida per gli sviluppatori di AMQP 1,0 del bus di servizio](service-bus-amqp-dotnet.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Centro per sviluppatori Java](https://azure.microsoft.com/develop/java/)