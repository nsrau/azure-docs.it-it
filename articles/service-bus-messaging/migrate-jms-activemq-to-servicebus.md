---
title: Eseguire la migrazione di applicazioni Java Message Service (JMS) da Apache ActiveMQ al bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di applicazioni JMS esistenti che interagiscono con Apache ActiveMQ per interagire con il bus di servizio di Azure.
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
ms.openlocfilehash: 7926e3b8aedde63c3a1a5a57c42b3d4f29cb9797
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076240"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Eseguire la migrazione di applicazioni Java Message Service (JMS) 2,0 esistenti da Apache ActiveMQ al bus di servizio di Azure

Questo articolo illustra come modificare un'applicazione Java Message Service (JMS) 2,0 esistente che interagisce con un broker JMS per interagire con il bus di servizio di Azure. In particolare, l'articolo descrive la migrazione da Apache ActiveMQ o Amazon MQ.

Il bus di servizio di Azure supporta i carichi di lavoro Java 2 Platform, Enterprise Edition e Spring che usano l'API JMS 2,0 per Advanced Message Queueing Protocol (AMQP).

## <a name="before-you-start"></a>Prima di iniziare

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Differenze tra il bus di servizio di Azure e Apache ActiveMQ

Il bus di servizio di Azure e Apache ActiveMQ sono entrambi broker di messaggi, funzionanti come provider JMS per le applicazioni client per l'invio e la ricezione di messaggi da. Entrambi consentono la semantica Point-to-Point con le code e la semantica di pubblicazione-sottoscrizione con argomenti e sottoscrizioni. 

Anche in questo caso, esistono alcune differenze tra le due, come illustrato nella tabella seguente:

| Category | ActiveMQ | Bus di servizio di Azure |
| --- | --- | --- |
| Suddivisione in livelli dell'applicazione | Monolith cluster | A due livelli <br> (gateway + back-end) |
| Supporto dei protocolli | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Modalità di provisioning | <ul> <li> Infrastruttura distribuita come servizio (IaaS), locale </li> <li> Amazon MQ (piattaforma gestita come servizio) </li> | Piattaforma distribuita come servizio (PaaS) |
| Dimensioni dei messaggi | Configurabile dal cliente | 1 MB (livello Premium) |
| Disponibilità elevata | Gestita dal cliente | Piattaforma gestita |
| Ripristino di emergenza | Gestita dal cliente | Piattaforma gestita | 

### <a name="current-supported-and-unsupported-features"></a>Funzionalità correnti supportate e non supportate

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Considerazioni

La natura a due livelli del bus di servizio di Azure offre diverse funzionalità di continuità aziendale (disponibilità elevata e ripristino di emergenza). Tuttavia, esistono alcune considerazioni quando si usano le funzionalità JMS.

#### <a name="service-upgrades"></a>Aggiornamenti del servizio

In caso di aggiornamenti e riavvii del bus di servizio, le code o gli argomenti temporanei vengono eliminati. Se l'applicazione è sensibile alla perdita di dati per code o argomenti temporanei, non usare code o argomenti temporanei. Usare invece code, argomenti e sottoscrizioni durevoli.

#### <a name="data-migration"></a>Migrazione dei dati

Come parte della migrazione e della modifica delle applicazioni client per interagire con il bus di servizio di Azure, i dati contenuti in ActiveMQ non vengono migrati al bus di servizio. Potrebbe essere necessaria un'applicazione personalizzata per svuotare le code, gli argomenti e le sottoscrizioni ActiveMQ, quindi riprodurre i messaggi per le code, gli argomenti e le sottoscrizioni del bus di servizio.

#### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Il controllo degli accessi in base al ruolo (RBAC), supportato da Azure Active Directory, è il meccanismo di autenticazione preferito per il bus di servizio. Poiché RBAC, o l'autenticazione basata su attestazioni, non è attualmente supportata da Apache QPID JMS, è tuttavia necessario usare chiavi SAS per l'autenticazione.

## <a name="pre-migration"></a>Pre-migrazione

### <a name="version-check"></a>Controllo della versione

Si usano i componenti e le versioni seguenti durante la scrittura delle applicazioni JMS: 

| Componente | Versione |
|---|---|
| API JMS (Java Message Service) | 1,1 o versione successiva |
| Protocollo AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Verificare che le porte AMQP siano aperte

Il bus di servizio supporta la comunicazione tramite il protocollo AMQP. A tale scopo, abilitare la comunicazione sulle porte 5671 (AMQP) e 443 (TCP). A seconda della posizione in cui sono ospitate le applicazioni client, potrebbe essere necessario un ticket di supporto per consentire la comunicazione su queste porte.

> [!IMPORTANT]
> Il bus di servizio supporta solo il protocollo AMQP 1,0.

### <a name="set-up-enterprise-configurations"></a>Configurare le configurazioni aziendali

Il bus di servizio consente diverse funzionalità di sicurezza aziendale e disponibilità elevata. Per altre informazioni, vedere: 

  * [Endpoint servizio di rete virtuale](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Crittografia lato servizio con chiave gestita dal cliente (BYOK)](configure-customer-managed-key.md)
  * [Endpoint privati](private-link-service.md)
  * [Autenticazione e autorizzazione](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitoraggio, avvisi e traccia

Per ogni spazio dei nomi del bus di servizio, è possibile pubblicare le metriche in monitoraggio di Azure. È possibile usare queste metriche per gli avvisi e il ridimensionamento dinamico delle risorse allocate allo spazio dei nomi.

Per altre informazioni sulle diverse metriche e su come configurare gli avvisi, vedere [metriche del bus di servizio in monitoraggio di Azure](service-bus-metrics-azure-monitor.md). È anche possibile trovare altre informazioni sulla [traccia lato client per le operazioni sui dati](service-bus-end-to-end-tracing.md) e [la registrazione operativa/diagnostica per le operazioni di gestione](service-bus-diagnostic-logs.md).

### <a name="metrics---new-relic"></a>Metriche-New Relic

È possibile correlare le metriche di ActiveMQ mappate alle metriche del bus di servizio di Azure. Nel sito Web New Relic vedere quanto segue:

  * [Metriche di New Relic per ActiveMQ/Amazon MQ](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Metriche di New Relic del bus di servizio di Azure](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Attualmente, New Relic non ha una perfetta integrazione diretta con ActiveMQ, ma dispone di metriche disponibili per Amazon MQ. Poiché Amazon MQ è derivato da ActiveMQ, la tabella seguente mappa le metriche di New Relic da Amazon MQ al bus di servizio di Azure.
>

|Raggruppamento metrica| Metrica di Amazon MQ/ActiveMQ | Metrica del bus di servizio di Azure |
|------------|---------------------------|--------------------------|
|Gestore|`CpuUtilization`|`CPUXNS`|
|Gestore|`MemoryUsage`|`WSXNS`|
|Gestore|`CurrentConnectionsCount`|`activeConnections`|
|Gestore|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Gestore|`InactiveDurableTopicSubscribersCount`|Usare le metriche della sottoscrizione|
|Gestore|`TotalMessageCount`|Usa livello coda/argomento/sottoscrizione`activeMessages`|
|Coda/argomento|`EnqueueCount`|`incomingMessages`|
|Coda/argomento|`DequeueCount`|`outgoingMessages`|
|Coda|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrazione

Per eseguire la migrazione dell'applicazione JMS 2,0 esistente per interagire con il bus di servizio, seguire i passaggi nelle sezioni successive.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Esportare la topologia da ActiveMQ e creare le entità nel bus di servizio (facoltativo)

Per garantire che le applicazioni client possano connettersi senza problemi con il bus di servizio, migrare la topologia (incluse le code, gli argomenti e le sottoscrizioni) da Apache ActiveMQ al bus di servizio.

> [!NOTE]
> Per le applicazioni JMS, è possibile creare code, argomenti e sottoscrizioni come operazione di Runtime. La maggior parte dei provider JMS (broker di messaggi) fornisce la possibilità di crearli in fase di esecuzione. Ecco perché questo passaggio di esportazione è considerato facoltativo. Per assicurarsi che l'applicazione disponga delle autorizzazioni per creare la topologia in fase di esecuzione, usare la stringa di connessione con le `Manage` autorizzazioni SAS.

A tale scopo, effettuare l'operazione seguente:

1. Usare gli [strumenti da riga di comando ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) per esportare la topologia.
1. Ricreare la stessa topologia usando un [modello di Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Eseguire il modello di Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importare la dipendenza Maven per l'implementazione di JMS del bus di servizio

Per garantire una connettività senza problemi con il bus di servizio, aggiungere il `azure-servicebus-jms` pacchetto come dipendenza al `pom.xml` File Maven, come indicato di seguito:

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

Questa parte è personalizzata per il server applicazioni che ospita le applicazioni client che si connettono a ActiveMQ.

#### <a name="tomcat"></a>Tomcat

Qui si inizia con la configurazione specifica per ActiveMQ, come illustrato nel `/META-INF/context.xml` file.

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

Questa operazione viene adattata in modo da puntare al bus di servizio, come indicato di seguito:

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

##### <a name="update-the-applicationproperties-file"></a>Aggiornare il `application.properties` file

Se si usa un'applicazione Spring boot per connettersi a ActiveMQ, è necessario rimuovere le proprietà specifiche di ActiveMQ dal `application.properties` file.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Aggiungere quindi le proprietà specifiche del bus di servizio al `application.properties` file.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Sostituire `ActiveMQConnectionFactory` con `ServiceBusJmsConnectionFactory`.

Il passaggio successivo consiste nel sostituire l'istanza di `ActiveMQConnectionFactory` con `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> Le modifiche effettive al codice sono specifiche per l'applicazione e la modalità di gestione delle dipendenze, ma nell'esempio seguente vengono fornite le indicazioni sugli elementi da modificare.
>

In precedenza, è possibile che sia stata creata un'istanza di un oggetto di `ActiveMQConnectionFactory` , come indicato di seguito:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

A questo punto, si sta cambiando questa impostazione per creare un'istanza di un oggetto di `ServiceBusJmsConnectionFactory` , come indicato di seguito:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Post-migrazione

Ora che l'applicazione è stata modificata per avviare l'invio e la ricezione di messaggi dal bus di servizio, è necessario verificare che funzioni come previsto. Al termine, è possibile continuare a perfezionare e modernizzare ulteriormente lo stack di applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Usare l'utilità [di avvio Spring boot per il bus di servizio di Azure JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) per una perfetta integrazione con il bus di servizio.

Per ulteriori informazioni sulla messaggistica del bus di servizio e JMS, vedere:

* [JMS del bus di servizio](service-bus-java-how-to-use-jms-api-amqp.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
