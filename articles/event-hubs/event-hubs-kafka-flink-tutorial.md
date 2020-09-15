---
title: Uso di Apache Flink per Apache Kafka - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come connettere Apache flink a un hub eventi di Azure
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f2e6eeb74c5a334d1692357edec0fd363349c7c0
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061649"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Uso di Apache Flink con Hub eventi di Azure per Apache Kafka
Questa esercitazione illustra come connettere Apache flink a un hub eventi senza modificare i client del protocollo o eseguire i propri cluster. Per altre informazioni sul supporto di hub eventi per il protocollo consumer Apache Kafka, vedere [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Eseguire il producer Flink 
> * Eseguire il consumer Flink

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di disporre dei prerequisiti seguenti:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/downloads)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Per l'invio o la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Per istruzioni su come creare uno spazio dei nomi e un hub eventi, vedere [creazione di un hub eventi](event-hubs-create.md) . Assicurarsi di copiare la stringa di connessione di Hub eventi per usarla in seguito.

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio

Ora che si dispone della stringa di connessione di hub eventi, clonare gli hub eventi di Azure per il repository Kafka e passare alla `flink` sottocartella:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Eseguire il producer Flink

Usando l'esempio del producer Flink fornito, inviare messaggi al servizio Hub eventi.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornire un endpoint Kafka di Hub eventi

#### <a name="producerconfig"></a>producer.config

Aggiornare i valori `bootstrap.servers` e `sasl.jaas.config` in `producer/src/main/resources/producer.config` per indirizzare il producer all'endpoint Kafka di Hub eventi con l'autenticazione corretta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Eseguire il producer dalla riga di comando

Per eseguire il producer dalla riga di comando, generare il file JAR e quindi eseguirlo dall'interno di Maven (o generare il file JAR usando Maven, quindi eseguirlo in Java aggiungendo i JAR Kafka necessari al classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Il producer inizierà a inviare gli eventi all'hub eventi in questo argomento `test` e a stampare gli eventi in stdout.

## <a name="run-flink-consumer"></a>Eseguire il consumer Flink

Usando l'esempio di consumer specificato, ricevere i messaggi dall'hub eventi. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornire un endpoint Kafka di Hub eventi

#### <a name="consumerconfig"></a>consumer.config

Aggiornare i valori `bootstrap.servers` e `sasl.jaas.config` in `consumer/src/main/resources/consumer.config` per indirizzare il consumer all'endpoint Kafka di Hub eventi con l'autenticazione corretta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Eseguire il consumer dalla riga di comando

Per eseguire il consumer dalla riga di comando, generare il file JAR e quindi eseguirlo dall'interno di Maven (o generare il file JAR usando Maven, quindi eseguirlo in Java aggiungendo i JAR Kafka necessari al classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Se per l'hub eventi sono presenti eventi, ad esempio se il Producer è anche in esecuzione, il consumer inizia a ricevere gli eventi dall'argomento `test` .

Consultare la [Guida del connettore Kafka di Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) per altre informazioni sulla connessione di Flink a Kafka.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Integrare Kafka Connect con un hub eventi](event-hubs-kafka-connect-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Guida per gli sviluppatori Apache Kafka per hub eventi di Azure](apache-kafka-developer-guide.md)