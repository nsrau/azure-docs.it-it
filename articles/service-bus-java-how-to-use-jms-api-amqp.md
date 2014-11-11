<properties linkid="develop-java-how-to-guides-service-bus-amqp" urlDisplayName="Service Bus AMQP" pageTitle="How to use AMQP 1.0 with the Java Service Bus API - Azure" metaKeywords="ava Messsage AMQP, Service Bus AMQP, download AMQP JMS library" description="Learn how to use the Java Message Service (JMS) with Azure Service Bus and Advanced Message Queuing Protodol (AMQP) 1.0." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to use the Java Message Service (JMS) API with Service Bus &amp; AMQP 1.0" authors="" solutions="" writer="sethm" manager="dwrede" editor="mattshel" />

Come utilizzare l'API JMS (Java Message Service) con Service Bus e AMQP 1.0
===========================================================================

Introduzione
------------

AMQP (Advanced Message Queuing Protocol) 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere utilizzato per creare potenti applicazioni di messaggistica multipiattaforma.

Grazie al supporto per AMQP 1.0 nel bus di servizio, è ora possibile utilizzare le funzionalità di accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione da numerose piattaforme, tramite un efficiente protocollo binario. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

In questa guida dettagliata viene illustrato come utilizzare le funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) da applicazioni Java tramite la diffusa API standard JMS (Java Message Service). È disponibile una guida complementare in cui viene illustrato come eseguire le stesse procedure utilizzando l'API .NET di Service Bus. È possibile consultare queste due guide per acquisire informazioni sulla messaggistica multipiattaforma con AMQP 1.0.

Attività iniziali per il bus di servizio
----------------------------------------

In questa guida si presuppone che si disponga già di uno spazio dei nomi del bus di servizio contenente una coda denominata "queue1". In caso contrario, è necessario creare lo spazio dei nomi e la coda tramite il [portale di gestione di Azure](http://manage.windowsazure.com). Per ulteriori informazioni su come creare spazi dei nomi e code del bus di servizio, vedere la guida dettagliata [Come utilizzare le code del bus di servizio](https://www.windowsazure.com/it-it/develop/net/how-to-guides/service-bus-queues/).

Download della libreria client JMS basata su AMQP 1.0
-----------------------------------------------------

Per informazioni sul download dell'ultima versione della libreria client JMS basata su AMQP 1.0 di Apache Qpid, visitare il sito Web all'indirizzo <http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html>.

È necessario aggiungere i seguenti quattro file JAR dall'archivio di distribuzione AMQP 1.0 di Apache Qpid al CLASSPATH Java durante la compilazione e l'esecuzione di applicazioni JMS con il bus di servizio:

-   geronimo-jms\_1.1\_spec-1.0.jar
-   qpid-amqp-1-0-client-[version].jar
-   qpid-amqp-1-0-client-jms-[version].jar
-   qpid-amqp-1-0-common-[version].jar

Compilazione di applicazioni Java
---------------------------------

### Java Naming and Directory Interface (JNDI)

JMS utilizza l'interfaccia JNDI (Java Naming and Directory Interface) per creare una separazione tra i nomi logici e i nomi fisici. Con JNDI vengono risolti due tipi di oggetti JMS: ConnectionFactory e Destination. JNDI utilizza un modello di provider in cui è possibile inserire diversi servizi directory per gestire le attività di risoluzione dei nomi. La libreria JMS basata su AMQP 1.0 di Apache Qpid viene fornita con un semplice provider JNDI, basato su un file delle proprietà, che viene configurato mediante un file di testo:

    # servicebus.properties - sample JNDI configuration

    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net

    # Register some queues in JNDI using the form
    # queue.[jndi_name] = [physical_name]
    # topic.[jndi_name] = [physical_name]
    queue.QUEUE = queue1

**Configurazione della factory di connessione**

La voce utilizzata per definire un oggetto **ConnectionFactory** nel provider JNDI basato sul file delle proprietà Qpid è nel formato seguente:

    connectionfactory.[jndi_name] = [ConnectionURL]

Dove [jndi\_name] e [ConnectionURL] hanno i significati seguenti:

<table data-morhtml="true">
  <tr data-morhtml="true">
    <td data-morhtml="true">[jndi_name]</td>
    <td data-morhtml="true">Nome logico dell'oggetto ConnectionFactory. Tale nome viene risolto nell'applicazione Java mediante l'utilizzo del metodo JNDI IntialContext.lookup().</td>
  </tr>
  <tr data-morhtml="true">
    <td data-morhtml="true">[ConnectionURL]</td>
    <td data-morhtml="true">URL che fornisce alla libreria JMS le informazioni necessarie per il broker AMQP.</td>
  </tr>
</table>

Il formato di **ConnectionURL** è il seguente:

    amqps://[username]:[password]@[namespace].servicebus.windows.net

Dove [namespace], [username] e [password] hanno i significati seguenti:

<table data-morhtml="true">
  <tr data-morhtml="true">
    <td data-morhtml="true">[namespace]</td>
    <td data-morhtml="true">Spazio dei nomi del bus di servizio ottenuto dal portale di gestione di Azure.</td>
  </tr>
  <tr data-morhtml="true">
    <td data-morhtml="true">[username]</td>
    <td data-morhtml="true">Nome dell'autorit&agrave; emittente del bus di servizio ottenuto dal portale di gestione di Azure.</td>
  </tr>
  <tr data-morhtml="true">
    <td data-morhtml="true">[password]</td>
    <td data-morhtml="true">Chiave dell'autorit&agrave; emittente del bus di servizio con codifica URL ottenuta dal portale di gestione di Azure.</td>
  </tr>
</table>

**Nota**: è necessario applicare manualmente la codifica URL alla password. Un'efficace utilità di codifica URL è disponibile all'indirizzo <http://www.w3schools.com/tags/ref_urlencode.asp>.

Ad esempio, se le informazioni ottenute dal portale di gestione di Azure sono le seguenti:

<table data-morhtml="true">
  <tr data-morhtml="true">
    <td data-morhtml="true">Spazio dei nomi:</td>
    <td data-morhtml="true">foo.servicebus.windows.net</td>
  </tr>
  <tr data-morhtml="true">
    <td data-morhtml="true">Nome autorit&agrave; emittente:</td>
    <td data-morhtml="true">proprietario</td>
  </tr>
  <tr data-morhtml="true">
    <td data-morhtml="true">Chiave autorit&agrave; emittente:</td>
    <td data-morhtml="true">j9VYv1q33Ea+cbahWsHFYnLkEzrF0yA5SAqcLNvU7KM=</td>
  </tr>
</table>

Per definire un oggetto **ConnectionFactory** denominato "SBCF", la stringa di configurazione sarà la seguente:

    connectionfactory.SBCF = amqps://owner:j9VYv1q33Ea%2BcbahWsHFYnLkEzrF0yA5SAqcLNvU7KM%3D@foo.servicebus.windows.net

**Configurazione delle destinazioni**

La voce utilizzata per definire una destinazione nel provider JNDI basato sul file delle proprietà Qpid è nel formato seguente:

    queue.[jndi_name] = [physical_name]

oppure

    topic.[jndi_name] = [physical_name]

Dove [jndi\_name] e [physical\_name] hanno i significati seguenti:

<table data-morhtml="true">
  <tr data-morhtml="true">
    <td data-morhtml="true">[jndi_name]</td>
    <td data-morhtml="true">Nome logico della destinazione. Tale nome viene risolto nell'applicazione Java mediante l'utilizzo del metodo JNDI IntialContext.lookup().</td>
  </tr>
  <tr data-morhtml="true">
    <td data-morhtml="true">[physical_name]</td>
    <td data-morhtml="true">Nome dell'entit&agrave; del bus di servizio a cui l'applicazione invia messaggi o da cui l'applicazione riceve messaggi.</td>
  </tr>
</table>

**Nota**: In caso di ricezione da una sottoscrizione a un argomento del bus di servizio, il nome fisico specificato in JNDI deve essere il nome dell'argomento. Il nome della sottoscrizione viene fornito al momento della creazione della sottoscrizione durevole nel codice dell'applicazione JMS. Nella [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/it-it/library/windowsazure/jj841071.aspx) sono disponibili istruzioni dettagliate sull'utilizzo delle sottoscrizioni ad argomenti del bus di servizio da JMS.

### Scrivere l'applicazione JMS

Per utilizzare JMS con il bus di servizio non sono necessarie opzioni o API specifiche. Esistono tuttavia alcune limitazioni che verranno illustrate più avanti. Come per qualsiasi applicazione JMS, la prima operazione da eseguire è la configurazione dell'ambiente JNDI, in modo da poter risolvere gli oggetti di tipo **ConnectionFactory** e le destinazioni.

**Configurare il contesto JNDI iniziale**

La configurazione dell'ambiente JNDI viene eseguita passando una tabella hash di informazioni di configurazione al costruttore della classe javax.naming.InitialContext. I due elementi necessari nella tabella hash sono il nome della classe della factory del contesto iniziale e l'URL del provider. Nel codice seguente viene illustrato come configurare l'ambiente JNDI per utilizzare il provider JNDI basato sul file delle proprietà Qpid con un file delle proprietà denominato **servicebus.properties**.

    Hashtable<String, String> env = new Hashtable<String, String>(); 
    env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
    env.put(Context.PROVIDER_URL, "servicebus.properties"); 
    InitialContext context = new InitialContext(env); 

### Semplice applicazione JMS che utilizza la coda del bus di servizio

L'applicazione di esempio seguente consente di inviare e ricevere messaggi di testo JMS verso e da una coda del bus di servizio con il nome JNDI logico QUEUE.

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

            // Lookup ConnectionFactory and Queue
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

### Esecuzione dell'applicazione

L'esecuzione dell'applicazione produce un output nel formato seguente:

    > java SimpleSenderReceiver
    Press [enter] to send a message. Type 'exit' + [enter] to quit.

    Sent message with JMSMessageID = ID:2867600614942270318
    Received message with JMSMessageID = ID:2867600614942270318

    Sent message with JMSMessageID = ID:7578408152750301483
    Received message with JMSMessageID = ID:7578408152750301483

    Sent message with JMSMessageID = ID:956102171969368961
    Received message with JMSMessageID = ID:956102171969368961
    exit

Messaggistica multipiattaforma tra JMS e .NET
---------------------------------------------

In questa guida è stato illustrato come inviare e ricevere messaggi verso e dal bus di servizio utilizzando JMS. Tuttavia, uno dei principali vantaggi di AMQP 1.0 è che consente di creare applicazioni da componenti scritti in linguaggi diversi, con i messaggi scambiati in modo affidabile e con la massima fedeltà.

Utilizzando l'applicazione JMS di esempio descritta in precedenza e un'applicazione .NET simile presa dalla guida complementare [Come utilizzare AMQP 1.0 con l'API .NET di Service Bus](http://aka.ms/lym3vk), è possibile scambiare messaggi tra .NET e Java.

Per ulteriori informazioni sulla messaggistica multipiattaforma con Service Bus e AMQP 1.0, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/it-it/library/windowsazure/jj841071.aspx).

### Da JMS a .NET

Per verificare la messaggistica da JMS a .NET, eseguire la procedura seguente:

-   Avviare l'applicazione di esempio .NET senza argomenti della riga di comando.
-   Avviare l'applicazione di esempio Java con l'argomento della riga di comando "sendonly". In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
-   Premere alcune volte **INVIO** nella console dell'applicazione Java per generare l'invio di messaggi.
-   Questi messaggi vengono ricevuti dall'applicazione .NET.

**Output dell'applicazione JMS**

    > java SimpleSenderReceiver sendonly
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Sent message with JMSMessageID = ID:4364096528752411591
    Sent message with JMSMessageID = ID:459252991689389983
    Sent message with JMSMessageID = ID:1565011046230456854
    exit

**Output dell'applicazione .NET**

    > SimpleSenderReceiver.exe    
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Received message with MessageID = 4364096528752411591
    Received message with MessageID = 459252991689389983
    Received message with MessageID = 1565011046230456854
    exit

### Da .NET a JMS

Per verificare la messaggistica da .NET a JMS, eseguire la procedura seguente:

-   Avviare l'applicazione di esempio .NET con l'argomento "sendonly" della riga di comando. In questa modalità l'applicazione non riceverà messaggi dalla coda, ma potrà solo inviarne.
-   Avviare l'applicazione di esempio Java senza argomenti della riga di comando.
-   Premere alcune volte **INVIO** nella console dell'applicazione .NET per generare l'invio di messaggi.
-   Questi messaggi vengono ricevuti dall'applicazione Java.

**Output dell'applicazione .NET**

    > SimpleSenderReceiver.exe sendonly
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
    Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
    Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
    exit

**Output dell'applicazione JMS**

    > java SimpleSenderReceiver   
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
    Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
    Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
    exit

Funzionalità non supportate e restrizioni
-----------------------------------------

Quando si utilizza JMS su AMQP 1.0 con il bus di servizio esistono le seguenti restrizioni:

-   È consentito solo un oggetto **MessageProducer** o **MessageConsumer** per **sessione**. Se si desidera creare più oggetti **MessageProducer** o **MessageConsumer** in un'applicazione, creare una **sessione** dedicata per ognuno di essi.
-   Le sottoscrizioni a un argomento volatile non sono attualmente supportate.
-   Gli oggetti **MessageSelector** non sono supportati.
-   Le destinazioni temporanee, ad esempio **TemporaryQueue** o **TemporaryTopic**, non sono attualmente supportate, così come le API **QueueRequestor** e **TopicRequestor** da cui vengono utilizzate.
-   Le sessioni transazionali non sono supportate e le transazioni distribuite non sono supportate.

Riepilogo
---------

In questa guida dettagliata è stato illustrato come accedere alle funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) del bus di servizio da Java tramite la diffusa API JMS e AMQP 1.0.

È anche possibile utilizzare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui .NET, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie al supporto per AMQP 1.0 nel bus di servizio. Per ulteriori informazioni, vedere la [guida per sviluppatori di AMQP 1.0 per il bus di servizio](http://msdn.microsoft.com/it-it/library/windowsazure/jj841071.aspx).

Ulteriori informazioni
----------------------

-   [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](http://aka.ms/pgr3dp)
-   [Come utilizzare AMQP 1.0 con l'API .NET di Service Bus](http://aka.ms/lym3vk)
-   [AMQP 1.0 per Service Bus - Guida per sviluppatori](http://msdn.microsoft.com/it-it/library/windowsazure/jj841071.aspx)
-   [Come utilizzare le code del bus di servizio](http://www.windowsazure.com/it-it/develop/net/how-to-guides/service-bus-queues/)

