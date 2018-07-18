---
title: Eseguire lo streaming in Hub eventi di Azure per l'ecosistema Kafka| Microsoft Docs
description: Eseguire lo streaming in Hub eventi usando le API e il protocollo Kafka.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297224"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>Eseguire lo streaming in Hub eventi per l'ecosistema Kafka

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs)

Questa guida introduttiva illustra come eseguire lo streaming in un'istanza di Hub eventi abilitata per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Si apprenderà come usare i producer e i consumer per comunicare con un'istanza di Hub eventi abilitata per Kafka con solo una modifica della configurazione nelle applicazioni. Hub eventi di Azure per l'ecosistema Kafka supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario Maven.
* [Git](https://www.git-scm.com/)
* [Uno spazio dei nomi di Hub eventi abilitato per Kafka](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Inviare e ricevere messaggi con Kafka in Hub eventi

1. Clonare il [repository di Hub eventi di Azure](https://github.com/Azure/azure-event-hubs).

2. Accedere a `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Aggiornare i dettagli di configurazione per il producer in `src/main/resources/producer.config` come indicato di seguito:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Eseguire il codice del producer ed eseguire lo streaming nell'istanza di Hub eventi abilitata per Kafka.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Accedere a `azure-event-hubs/samples/kafka/quickstart/consumer`.

6. Aggiornare i dettagli di configurazione per il consumer in `src/main/resources/consumer.config` come indicato di seguito:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Eseguire il codice del consumer ed eseguire l'elaborazione dall'istanza di Hub eventi abilitata per Kafka usando i client Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se il cluster Kafka di Hub eventi include eventi, si dovrebbe ora iniziare a riceverli dal consumer.

## <a name="next-steps"></a>Passaggi successivi

* [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)
* [Leggere le informazioni su Hub eventi per l'ecosistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per lo [streaming di eventi dall'istanza di Kafka locale all'istanza di Hub eventi abilitata per Kafka nel cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Informazioni su come eseguire lo streaming negli hub eventi abilitati per Kafka usando [Apache Flink](event-hubs-kafka-flink-tutorial.md) o [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md).
