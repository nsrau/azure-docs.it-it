---
title: 'Avvio rapido: Streaming di dati con Hub eventi tramite il protocollo Kafka'
description: 'Avvio rapido: Questo articolo fornisce informazioni su come eseguire lo streaming in Hub eventi di Azure tramite il protocollo e le API Kafka.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: e87d9b1e4a444695d64dc6acfa0e29f7b72e37f2
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319360"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Avvio rapido: Streaming di dati con Hub eventi tramite il protocollo Kafka
Questo argomento di avvio rapido illustra come eseguire lo streaming in Hub eventi senza modificare i client di protocollo o eseguire cluster personalizzati. Si apprenderà come usare i producer e i consumer per comunicare con Hub eventi con solo una modifica della configurazione nelle applicazioni. 

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Prerequisiti

Per completare questo avvio rapido, assicurarsi che vengano soddisfatti i prerequisiti seguenti:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Quando si crea uno spazio dei nomi di Hub eventi di livello **Standard**, l'endpoint Kafka per lo spazio dei nomi viene abilitato automaticamente. È possibile trasmettere eventi dalle applicazioni che usano il protocollo Kafka in Hub eventi di livello Standard. Seguire le istruzioni dettagliate illustrate in [Creare un hub eventi usando il portale di Azure](event-hubs-create.md) per creare uno spazio dei nomi di Hub eventi di livello **Standard**. 

> [!NOTE]
> Hub eventi per Kafka è disponibile solo nei livelli **Standard** e **Dedicato**. Il livello **Basic** non supporta Kafka in Hub eventi.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Inviare e ricevere messaggi con Kafka in Hub eventi

1. Clonare gli [Hub eventi di Azure per il repository di Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Accedere a `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aggiornare i dettagli di configurazione per il producer in `src/main/resources/producer.config` come indicato di seguito:

    **TLS/SSL:**

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
4. Eseguire il codice del producer e trasmettere eventi in Hub eventi:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Accedere a `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aggiornare i dettagli di configurazione per il consumer in `src/main/resources/consumer.config` come indicato di seguito:
   
    **TLS/SSL:**

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
7. Eseguire il codice consumer ed elaborare gli eventi dell'hub eventi con i client Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se il cluster Kafka di Hub eventi include eventi, si dovrebbe ora iniziare a riceverli dal consumer.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come eseguire lo streaming in Hub eventi senza modificare i client di protocollo o eseguire cluster personalizzati. Per altre informazioni, vedere [Guida per sviluppatori Apache Kafka per Hub eventi di Azure](apache-kafka-developer-guide.md).