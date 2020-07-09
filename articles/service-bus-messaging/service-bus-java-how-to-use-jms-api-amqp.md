---
title: Usare AMQP con l'API del servizio messaggi Java & il bus di servizio di Azure
description: Come usare JMS (Java Message Service ) con il bus di servizio di Azure e il protocollo AMQP (Advanced Message Queuing Protocol) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119158"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Usare Java Message Service (JMS) con il bus di servizio di Azure e AMQP 1,0

Il supporto per il protocollo **Advance Message Queueing Protocol (AMQP) 1,0** nel bus di servizio di Azure significa che è possibile usare le funzionalità di Accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione da una vasta gamma di piattaforme usando un protocollo binario efficiente. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

Questo articolo illustra come usare le funzionalità di messaggistica del bus di servizio di Azure (code e argomenti di pubblicazione/sottoscrizione) dalle applicazioni Java usando l'API **JMS (Java Message Service** ) più diffusa sul protocollo AMQP.

## <a name="get-started-with-service-bus"></a>Introduzione al bus di servizio
Questa guida presuppone che sia già presente uno spazio dei nomi del bus di servizio contenente una coda denominata `basicqueue` . In caso contrario, è possibile [creare lo spazio dei nomi e la coda](service-bus-create-namespace-portal.md) usando il [portale di Azure](https://portal.azure.com). Per altre informazioni su come creare spazi dei nomi e code del bus di servizio, vedere [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Le code e gli argomenti partizionati supportano anche AMQP. Per altre informazioni, vedere le [entità di messaggistica partizionate](service-bus-partitioning.md) e [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Quali funzionalità JMS sono supportate?

Di seguito sono riportate le funzionalità JMS supportate nel bus di servizio di Azure.

| Funzionalità | Livello standard del bus di servizio di Azure-JMS 1,1 | Livello Premium del bus di servizio di Azure-JMS 2,0 (anteprima) |
|---|---|---|
| Creazione automatica di entità su AMQP | Non supportato | **Supportato** |
| Code | **Supportato** | **Supportato** |
| Argomenti | **Supportato** | **Supportato** |
| Code temporanee | Non supportato <br/> (Creare una coda normale con *AutoDeleteOnIdle* impostato) | **Supportato** |
| Argomenti temporanei | Non supportato | **Supportato** |
| Selettori di messaggi | Non supportato | **Supportato** |
| Browser di Accodamento | Non supportato <br/> (Usare la funzionalità *Visualizza* dell'API del bus di servizio) | **Supportato** |
| Sottoscrizioni durevoli condivise | **Supportato** | **Supportato**|
| Sottoscrizioni durevoli non condivise | Non supportato | **Supportato** |
| Sottoscrizioni non durevoli condivise | Non supportato | **Supportato** |
| Sottoscrizioni non durevoli non condivise | Non supportato | **Supportato** |
| Annulla sottoscrizione per sottoscrizioni durevoli | Non supportato | **Supportato** |
| ReceiveNoWait | Non supportato | **Supportato** |
| Transazioni distribuite | Non supportato | Non supportato |
| Terminus durevole | Non supportato | Non supportato |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Avvertenze aggiuntive per il livello standard del bus di servizio
È consentito solo un oggetto **MessageProducer** o **MessageConsumer** per **sessione**. Se si vuole creare più oggetti **MessageProducers** o **MessageConsumers** in un'applicazione, creare una **sessione** dedicata per ognuno di essi.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Download della libreria client di Java Message Service (JMS)

Per connettersi al bus di servizio di Azure e usare l'API JMS (Java Message Service) su AMQP, è necessario sfruttare le librerie seguenti. Questi devono essere aggiunti al percorso di compilazione utilizzando lo strumento di gestione delle dipendenze preferito per il progetto.

La libreria client necessaria dipende dal piano tariffario utilizzato.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Livello Premium-JMS 2,0 su AMQP (anteprima)

Per usare tutte le funzionalità di anteprima disponibili nel livello Premium del bus di servizio di Azure, usare la libreria [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) .

### <a name="standard-tier---jms-11-over-amqp"></a>Livello standard-JMS 1,1 su AMQP

Per usare le funzionalità JMS supportate dal livello standard del bus di servizio (vedere [quali funzionalità JMS sono supportate?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) usare le librerie seguenti:

* [Specifica Geronimo JMS 1,1](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Client JMS qpid](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Le versioni e i nomi JAR JMS possono essere stati modificati. Per informazioni dettagliate, vedere [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Compilazione di applicazioni Java

Una volta importate le dipendenze, le applicazioni Java possono essere scritte in modo indipendente dal provider JMS.

Poiché il bus di servizio di Azure standard e Premium differiscono dalle dipendenze e dal numero di funzionalità JMS supportate, il modello di programmazione è leggermente diverso per i due.

> [!IMPORTANT]
> La guida seguente illustra come connettersi al bus di servizio di Azure in base a una semplice applicazione.
>
> Dato che la maggior parte delle architetture delle applicazioni aziendali può avere una modalità personalizzata per gestire le dipendenze e le configurazioni, usare le informazioni seguenti come guida per comprendere gli elementi necessari e adattarsi in modo appropriato all'applicazione.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Connessione al bus di servizio di Azure tramite JMS

Per connettersi al bus di servizio di Azure con client JMS, è necessario il formato **ConnectionString** disponibile nei ' criteri di accesso condivisi ' nel [portale di Azure](https://portal.azure.com) in **stringa di connessione primaria**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Connessione al bus di servizio Premium di Azure tramite JMS 2,0 (anteprima)

1. Creare un'istanza di`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Creare un'istanza `ServiceBusJmsConnectionFactory` di con l'oggetto appropriato `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Utilizzare `ConnectionFactory` per creare un oggetto `Connection` e quindi un oggetto`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    o `JMSContext` (per i client JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Connessione allo standard del bus di servizio di Azure tramite JMS 1,1

1. Inserire la configurazione del bus di servizio di Azure nel file delle proprietà JNDI denominato **ServiceBus. Properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Configurare il contesto JNDI e configurare ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Utilizzare `ConnectionFactory` per creare un oggetto `Connection` e quindi un oggetto `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Scrivere l'applicazione JMS

Una volta `Session` creata l'istanza di o `JMSContext` , l'applicazione può sfruttare le note API JMS per eseguire operazioni di gestione e dati.

Vedere l'elenco delle [funzionalità JMS supportate](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) per il livello standard e Premium per vedere quali API saranno supportate in ogni livello.

## <a name="summary"></a>Summary
Questa guida ha presentato il modo in cui le applicazioni client Java che usano Java Message Service (JMS) su AMQP 1,0 possono interagire con il bus di servizio di Azure.

È anche possibile utilizzare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui .NET, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie al supporto per AMQP 1.0 nel bus di servizio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul bus di servizio di Azure e informazioni dettagliate sulle entità di Java Message Service (JMS), vedere i collegamenti seguenti: 
* [Bus di servizio: code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md)
* [Bus di servizio-entità del servizio messaggi Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

