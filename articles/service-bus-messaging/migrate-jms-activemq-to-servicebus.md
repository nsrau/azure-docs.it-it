---
title: Eseguire la migrazione di applicazioni Java Message Service (JMS) da ActiveMQ al bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di applicazioni JMS esistenti che interagiscono con il MQ attivo per interagire con il bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122339"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Eseguire la migrazione di applicazioni Java Message Service (JMS) 2,0 esistenti da un MQ attivo a un bus di servizio di Azure

Il bus di servizio di Azure supporta i carichi di lavoro Java/J2EE e Spring che usano l'API Java Message Service (JMS) 2,0 sul protocollo AMQP (Advanced Message Queueing Protocol).

Questa guida descrive gli elementi da tenere in considerazione quando si vuole modificare un'applicazione Java Message Service (JMS) 2,0 esistente che interagisce con un broker JMS (in particolare Apache ActiveMQ o Amazon MQ) per interagire con il bus di servizio di Azure.

## <a name="before-you-start"></a>Prima di iniziare

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Differenze tra il bus di servizio di Azure e Apache ActiveMQ

Il bus di servizio di Azure e Apache ActiveMQ sono entrambi broker di messaggi che funzionano come provider JMS per le applicazioni client per l'invio e la ricezione di messaggi da. Entrambi consentono la semantica Point-to-Point con le **Code** e la semantica di pubblicazione-sottoscrizione con **argomenti** e **sottoscrizioni**. 

Anche in questo caso, esistono alcune differenze.

| Category | MQ attivi | Bus di servizio di Azure |
| --- | --- | --- |
| Suddivisione in livelli dell'applicazione | Monolith cluster | A due livelli <br> (Gateway + back-end) |
| Supporto dei protocolli | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Modalità di provisioning | <ul> <li> IaaS (locale) </li> <li> Amazon MQ (Managed PaaS) </li> | PaaS gestito |
| Dimensioni dei messaggi | Configurabile dal cliente | 1 MB (livello Premium) |
| Disponibilità elevata | Gestita dal cliente | Piattaforma gestita |
| Ripristino di emergenza | Gestita dal cliente | Piattaforma gestita | 

### <a name="current-supported-and-unsupported-features"></a>Funzionalità correnti supportate e non supportate

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Precisazioni

La natura a due livelli del bus di servizio di Azure offre diverse funzionalità di continuità aziendale (disponibilità elevata e ripristino di emergenza). Tuttavia, esistono alcune considerazioni relative all'utilizzo delle funzionalità JMS.

#### <a name="service-upgrades"></a>Aggiornamenti del servizio

In caso di aggiornamenti e riavvii del bus di servizio, le code o gli argomenti temporanei verranno eliminati.

Se l'applicazione è sensibile alla perdita di dati in code o argomenti temporanei, è consigliabile **non** utilizzare code o argomenti temporanei e utilizzare invece code, argomenti e sottoscrizioni durevoli.

#### <a name="data-migration"></a>Migrazione dei dati

Come parte della migrazione e della modifica delle applicazioni client per interagire con il bus di servizio di Azure, i dati contenuti in ActiveMQ non verranno migrati nel bus di servizio.

Potrebbe essere necessaria un'applicazione personalizzata per svuotare le code, gli argomenti e le sottoscrizioni ActiveMQ e riprodurre i messaggi in code, argomenti e sottoscrizioni del bus di servizio.

#### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Il controllo degli accessi in base al ruolo supportato da Azure ActiveDirectory è il meccanismo di autenticazione preferito per il bus di servizio di Azure.

Tuttavia, poiché il controllo degli accessi in base al ruolo non è attualmente supportato a causa della mancanza di supporto dell'autenticazione basata su attestazioni di Apache QPID

Per il momento, l'autenticazione è supportata solo con le chiavi SAS.

## <a name="pre-migration"></a>Pre-migrazione

### <a name="version-check"></a>Controllo della versione

Di seguito sono riportati i componenti utilizzati durante la scrittura delle applicazioni JMS e delle versioni specifiche supportate. 

| Componenti | Versione |
|---|---|
| API JMS (Java Message Service) | 1,1 o versione successiva |
| Protocollo AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Verificare che le porte AMQP siano aperte

Il bus di servizio di Azure supporta la comunicazione tramite il protocollo AMQP. A questo scopo, è necessario abilitare la comunicazione sulle porte 5671 (AMQP) e 443 (TCP). A seconda della posizione in cui sono ospitate le applicazioni client, potrebbe essere necessario un ticket di supporto per consentire la comunicazione su queste porte.

> [!IMPORTANT]
> Il bus di servizio di Azure supporta **solo** il protocollo AMQP 1,0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Configurare le configurazioni aziendali (VNET, firewall, endpoint privato e così via)

Il bus di servizio di Azure offre diverse funzionalità di sicurezza aziendale e disponibilità elevata. Per altre informazioni, seguire i collegamenti alla documentazione seguenti.

  * [Endpoint del servizio rete virtuale](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Crittografia lato servizio con chiave gestita dal cliente (BYOK)](configure-customer-managed-key.md)
  * [Endpoint privati](private-link-service.md)
  * [Autenticazione e autorizzazione](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitoraggio, avvisi e traccia

Le metriche vengono pubblicate per ogni spazio dei nomi del bus di servizio in monitoraggio di Azure e possono essere utilizzate per gli avvisi e il ridimensionamento dinamico delle risorse allocate allo spazio dei nomi.

Scopri di più sulle diverse metriche e su come impostare gli avvisi su di essi in [metriche del bus di servizio in monitoraggio di Azure](service-bus-metrics-azure-monitor.md).

Ulteriori informazioni sulla [traccia lato client per le operazioni sui dati](service-bus-end-to-end-tracing.md) e [la registrazione operativa/diagnostica per le operazioni di gestione](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Metriche-NewRelic

Di seguito è riportata una guida utile sulle metriche di ActiveMQ mappate alle metriche del bus di servizio di Azure. Di seguito viene fatto riferimento a NewRelic.

  * [Metriche di ActiveMQ/Amazon MQ NewRelic](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Metriche NewRelic del bus di servizio di Azure](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Attualmente, NewRelic non ha una semplice integrazione con ActiveMQ direttamente, ma sono disponibili metriche per Amazon MQ.
> Poiché Amazon MQ è derivato da ActiveMQ, la guida seguente esegue il mapping della metrica NewRelic da AmazonMQ al bus di servizio di Azure.
>

|Raggruppamento metrica| Metrica AmazonMQ/MQ attivo | Metrica del bus di servizio di Azure |
|------------|---------------------------|--------------------------|
|Gestore|`CpuUtilization`|`CPUXNS`|
|Gestore|`MemoryUsage`|`WSXNS`|
|Gestore|`CurrentConnectionsCount`|`activeConnections`|
|Gestore|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Gestore|`InactiveDurableTopicSubscribersCount`|Sfruttare le metriche di sottoscrizione|
|Gestore|`TotalMessageCount`|Utilizzare il livello coda/argomento/sottoscrizione`activeMessages`|
|Coda/argomento|`EnqueueCount`|`incomingMessages`|
|Coda/argomento|`DequeueCount`|`outgoingMessages`|
|Coda|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrazione

Per eseguire la migrazione dell'applicazione JMS 2,0 esistente per interagire con il bus di servizio di Azure, è necessario eseguire i passaggi seguenti.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Esportare la topologia da ActiveMQ e creare le entità nel bus di servizio di Azure (facoltativo)

Per assicurarsi che le applicazioni client possano connettersi senza problemi con il bus di servizio di Azure, la topologia, che include code, argomenti e sottoscrizioni, deve essere migrata da **Apache ActiveMQ** al **bus di servizio di Azure**.

> [!NOTE]
> Per le applicazioni Java Message Service (JMS), la creazione di code, argomenti e sottoscrizioni è un'operazione di Runtime. La maggior parte dei provider JMS (Java Message Service) (broker di messaggi) offre la funzionalità per creare *Code*, *argomenti* e *sottoscrizioni* in fase di esecuzione.
>
> Il passaggio precedente è pertanto facoltativo.
>
> Per assicurarsi che l'applicazione disponga delle autorizzazioni necessarie per creare la topologia in fase di esecuzione, verificare che venga usata la stringa di connessione con le autorizzazioni di ***"gestione" di SAS*** .

Per 
  * Utilizzare gli [strumenti da riga di comando ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) per esportare la topologia
  * Ricreare la stessa topologia usando un [modello di Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Eseguire il modello di Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importare la dipendenza Maven per l'implementazione di JMS del bus di servizio

Per garantire una connettività senza problemi con il bus di servizio di Azure, è necessario aggiungere il pacchetto ***Azure-ServiceBus-JMS*** come dipendenza al `pom.xml` File Maven come indicato di seguito.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Modifiche alla configurazione del server applicazioni

Questa parte è personalizzata per il server applicazioni che ospita le applicazioni client che si connettono a Active MQ.

#### <a name="tomcat"></a>Tomcat

Qui, si inizia con la configurazione specifica per il MQ attivo, come illustrato nel `/META-INF/context.xml` file.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

che può essere adattato come indicato di seguito per puntare al bus di servizio di Azure

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Applicazioni Spring

##### <a name="update-applicationproperties-file"></a>Aggiorna `application.properties` file

Se si usa un'applicazione Spring boot per connettersi a ActiveMQ.

L'obiettivo consiste nel ***rimuovere*** le proprietà specifiche di ActiveMQ dal `application.properties` file.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

***Aggiungere*** quindi le proprietà specifiche del bus di servizio al `application.properties` file.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Sostituire ActiveMQConnectionFactory con ServiceBusJmsConnectionFactory

Il passaggio successivo consiste nel sostituire l'istanza di ActiveMQConnectionFactory con ServiceBusJmsConnectionFactory.

> [!NOTE] 
> Le modifiche effettive al codice sono specifiche per l'applicazione e la modalità di gestione delle dipendenze, ma nell'esempio seguente vengono fornite le indicazioni sugli ***elementi*** da modificare.
>

In precedenza è possibile creare un'istanza di un oggetto di ActiveMQConnectionFactory come indicato di seguito.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Questa operazione verrebbe modificata nel creare un'istanza di un oggetto di ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Post-migrazione

Ora che l'applicazione è stata modificata per avviare l'invio e la ricezione di messaggi dal bus di servizio di Azure, è necessario verificare che funzioni come previsto. Al termine, è possibile continuare a perfezionare e modernizzare ulteriormente lo stack di applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Usare [Spring boot Starter per il bus di servizio di Azure JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) per una perfetta integrazione con il bus di servizio di Azure.

Per ulteriori informazioni sulla messaggistica del bus di servizio e sul servizio messaggi Java (JMS), vedere gli argomenti seguenti:

* [JMS del bus di servizio](service-bus-java-how-to-use-jms-api-amqp.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
