---
title: Usare l'API Java Message Service 2,0 con Azure Service Bus Premium
description: Come usare Java Message Service (JMS) con il bus di servizio di Azure
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801490"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>Usare l'API del servizio messaggi Java 2,0 con il bus di servizio Premium di Azure (anteprima)

Questo articolo illustra come usare la nota API **2,0 di Java Message Service (JMS)** per interagire con il bus di servizio di Azure tramite il protocollo Advanced Message Queueing Protocol (AMQP 1,0).

> [!NOTE]
> Il supporto per Java Message Service (JMS) 2,0 API è disponibile solo nel **livello Premium del bus di servizio di Azure** ed è attualmente in fase di **Anteprima**.
>

## <a name="get-started-with-service-bus"></a>Introduzione al bus di servizio

Questa guida presuppone che sia già presente uno spazio dei nomi del bus di servizio. In caso contrario, è possibile [creare lo spazio dei nomi e la coda](service-bus-create-namespace-portal.md) usando il [portale di Azure](https://portal.azure.com). 

Per altre informazioni su come creare gli spazi dei nomi e le code del bus di servizio, vedere [Introduzione alle code del bus di servizio tramite il portale di Azure](service-bus-quickstart-portal.md).

## <a name="what-jms-features-are-supported"></a>Quali funzionalità JMS sono supportate?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Download della libreria client di Java Message Service (JMS)

Per usare tutte le funzionalità disponibili nel livello Premium del bus di servizio di Azure, è necessario aggiungere la libreria seguente al percorso di compilazione del progetto.

[Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Per aggiungere [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) al percorso di compilazione, usare lo strumento di gestione delle dipendenze preferito per il progetto, ad esempio [Maven](https://maven.apache.org/) o [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Compilazione di applicazioni Java

Una volta importate le dipendenze, le applicazioni Java possono essere scritte in modo indipendente dal provider JMS.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Connessione al bus di servizio di Azure tramite JMS

Per connettersi al bus di servizio di Azure con client JMS, è necessaria la **stringa di connessione** disponibile nei ' criteri di accesso condiviso ' nel [portale di Azure](https://portal.azure.com) in **stringa di connessione primaria**.

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

### <a name="write-the-jms-application"></a>Scrivere l'applicazione JMS

Una volta `Session` creata l'istanza di o `JMSContext` , l'applicazione può utilizzare le API JMS note per eseguire operazioni di gestione e dati.

Vedere l'elenco delle [funzionalità JMS supportate](how-to-use-java-message-service-20.md#what-jms-features-are-supported) per vedere quali API sono supportate come parte di questa versione di anteprima.

Di seguito sono riportati alcuni frammenti di codice di esempio per iniziare a usare JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Invio di messaggi a una coda e un argomento

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Ricezione di messaggi da una coda

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Ricezione di messaggi da una sottoscrizione durevole condivisa in un argomento

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Riepilogo

Questa guida ha presentato il modo in cui le applicazioni client Java che usano Java Message Service (JMS) su AMQP 1,0 possono interagire con il bus di servizio di Azure.

È anche possibile utilizzare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui .NET, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie al supporto per AMQP 1.0 nel bus di servizio.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul bus di servizio di Azure e dettagli sulle entità JMS (Java Message Service), consultare i collegamenti seguenti: 
* [Bus di servizio: code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md)
* [Bus di servizio-entità del servizio messaggi Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [API del servizio messaggi Java (documento Oracle esterno)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Informazioni su come eseguire la migrazione da ActiveMQ a Service Bus](migrate-jms-activemq-to-servicebus.md)
