---
title: 'Avvio rapido: Streaming di dati con Hub eventi tramite il protocollo Kafka'
description: 'Avvio rapido: Questo articolo fornisce informazioni su come eseguire lo streaming in Hub eventi di Azure tramite il protocollo e le API Kafka.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 18976a29a716a0e5a627747d98edc0d3e1bf71e9
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587142"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Avvio rapido: Streaming di dati con Hub eventi tramite il protocollo Kafka
Questa guida introduttiva illustra come eseguire lo streaming in un'istanza di Hub eventi abilitata per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Si apprenderà come usare i producer e i consumer per comunicare con un'istanza di Hub eventi abilitata per Kafka con solo una modifica della configurazione nelle applicazioni. Hub eventi di Azure supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Prerequisites

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi con supporto per Kafka
Quando si crea uno spazio dei nomi di Hub eventi di livello **Standard**, l'endpoint Kafka per lo spazio dei nomi viene abilitato automaticamente. È possibile trasmettere eventi dalle applicazioni che usano il protocollo Kafka in Hub eventi di livello Standard. Seguire le istruzioni dettagliate illustrate in [Creare un hub eventi usando il portale di Azure](event-hubs-create.md) per creare uno spazio dei nomi di Hub eventi di livello **Standard**. 

> [!NOTE]
> Hub eventi per Kafka è disponibile solo nei livelli **Standard** e **Dedicato**. Il livello **Basic** non supporta Kafka in Hub eventi.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Inviare e ricevere messaggi con Kafka in Hub eventi

1. Clonare gli [Hub eventi di Azure per il repository di Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Accedere a `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aggiornare i dettagli di configurazione per il producer in `src/main/resources/producer.config` come indicato di seguito:

    **SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Il codice sorgente per la classe del gestore di esempio CustomAuthenticateCallbackHandler è disponibile in GitHub [qui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Eseguire il codice del producer e trasmettere eventi nell'istanza di Hub eventi abilitata per Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Accedere a `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aggiornare i dettagli di configurazione per il consumer in `src/main/resources/consumer.config` come indicato di seguito:
   
    **SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Il codice sorgente per la classe del gestore di esempio CustomAuthenticateCallbackHandler è disponibile in GitHub [qui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Tutti gli esempi di OAuth per Hub eventi per Kafka sono disponibili [qui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Eseguire il codice del consumer ed eseguire l'elaborazione dall'istanza di Hub eventi abilitata per Kafka usando i client Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se il cluster Kafka di Hub eventi include eventi, si dovrebbe ora iniziare a riceverli dal consumer.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come eseguire lo streaming in un'istanza di Hub eventi abilitata per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Per altre informazioni, vedere gli articoli e gli esempi seguenti:

- [Leggere le informazioni su Hub eventi per Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Guide di avvio rapido per Hub eventi per Kafka in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Esercitazioni per Hub eventi per Kafka in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per lo [streaming di eventi dall'istanza di Kafka locale all'istanza di Hub eventi abilitata per Kafka nel cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
- Scoprire come eseguire lo streaming negli hub eventi abilitati per Kafka usando [Apache Flink](event-hubs-kafka-flink-tutorial.md) o [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
