---
title: Uso di Akka Streams con Hub eventi di Azure per Apache Kafka | Microsoft Docs
description: Connessione di Akka Streams a un Hub eventi abilitato per Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: a7eb335a2e4764287ab9e0b67dc75c2443ba2856
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322006"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Uso di Akka Streams con Hub eventi per Apache Kafka
Questa esercitazione illustra come connettere Akka Streams all'Hub eventi abilitato per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Hub eventi di Azure per Kafka supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html)

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Eseguire il producer di Akka Streams 
> * Eseguire il consumer di Akka Streams

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di disporre dei prerequisiti seguenti:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario Maven.
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/downloads)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Per l'invio o la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Vedere [Creazione di un hub eventi con supporto per Kafka](event-hubs-create-kafka-enabled.md) per informazioni su come ottenere un endpoint Kafka di Hub eventi. Assicurarsi di copiare la stringa di connessione di Hub eventi per usarla in seguito.

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio

Dopo avere creato una stringa di connessione di Hub eventi con supporto per Kafka, clonare il repository di Hub eventi di Azure per Kafka e passare alla sottocartella `akka`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Eseguire il producer di Akka Streams

Usando l'esempio del producer Akka Streams fornito, inviare messaggi al servizio Hub eventi.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornire un endpoint Kafka di Hub eventi

#### <a name="producer-applicationconf"></a>Producer application.conf

Aggiornare i valori `bootstrap.servers` e `sasl.jaas.config` in `producer/src/main/resources/application.conf` per indirizzare il producer all'endpoint Kafka di Hub eventi con l'autenticazione corretta.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Eseguire il producer dalla riga di comando

Per eseguire il producer dalla riga di comando, generare il file JAR e quindi eseguirlo dall'interno di Maven (o generare il file JAR usando Maven, quindi eseguirlo in Java aggiungendo i JAR Kafka necessari al classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Il producer inizia a inviare eventi all'Hub eventi abilitato per Kafka all'argomento `test` e stampa gli eventi in stdout.

## <a name="run-akka-streams-consumer"></a>Eseguire il consumer di Akka Streams

Usando l'esempio consumer, fornito, ricevere messaggi dall'Hub eventi abilitato per Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornire un endpoint Kafka di Hub eventi

#### <a name="consumer-applicationconf"></a>Consumer application.conf

Aggiornare i valori `bootstrap.servers` e `sasl.jaas.config` in `consumer/src/main/resources/application.conf` per indirizzare il consumer all'endpoint Kafka di Hub eventi con l'autenticazione corretta.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Eseguire il consumer dalla riga di comando

Per eseguire il consumer dalla riga di comando, generare il file JAR e quindi eseguirlo dall'interno di Maven (o generare il file JAR usando Maven, quindi eseguirlo in Java aggiungendo i JAR Kafka necessari al classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se l'hub eventi abilitato per Kafka dispone di eventi (ad esempio, se è in esecuzione anche il producer), il consumer inizia a ricevere eventi dall'argomento `test`. 

Consultare il manuale [Akka Streams Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) per altre informazioni su Akka Streams.

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come connettere Akka Streams all'Hub eventi abilitato per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Hub eventi di Azure per Kafka supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html). Nell'ambito di questa esercitazione sono state eseguite le azioni seguenti: 

> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Eseguire il producer di Akka Streams 
> * Eseguire il consumer di Akka Streams

Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere l'argomento seguente:  

* [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)
* [Leggere le informazioni su Hub eventi per Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Esplorare altri esempi di Hub eventi per Kafka in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per lo [streaming di eventi dall'istanza di Kafka locale all'istanza di Hub eventi abilitata per Kafka nel cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Informazioni su come eseguire lo streaming negli Hub eventi abilitati per Kafka usando le [applicazioni native Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) o [Apache Flink](event-hubs-kafka-flink-tutorial.md)
