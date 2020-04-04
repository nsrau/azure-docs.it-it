---
title: Uso di Apache Flink per Apache Kafka - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come connettere Apache Flink a un hub eventi di AzureThis article provides information on how to connect Apache Flink to an Azure event hub
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 2e5a2924cdc00c1cc057d71c40645085df4bae6a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632821"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Uso di Apache Flink con Hub eventi di Azure per Apache Kafka
Questa esercitazione illustra come connettere Apache Flink a un hub eventi senza modificare i client di protocollo o eseguire i propri cluster. Hub eventi di Azure supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html).

Uno dei principali vantaggi dell'uso di Apache Kafka è l'ecosistema di framework a cui si può connettere. Hub eventi combina la flessibilità di Kafka con la scalabilità, la coerenza e il supporto dell'ecosistema Azure.

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
* [Kit di sviluppo Java (JDK) 1.7](https://aka.ms/azure-jdks)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/downloads)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Per l'invio o la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Vedere [Creazione di un hub eventi](event-hubs-create.md) per istruzioni sulla creazione di uno spazio dei nomi e di un hub eventi. Assicurarsi di copiare la stringa di connessione di Hub eventi per usarla in seguito.

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio

Ora che si dispone della stringa di connessione Hub eventi, clonare il `flink` repository Hub eventi di Azure per Kafka e passare alla sottocartella:

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

Il producer inizierà ora a inviare `test` eventi all'hub eventi in corrispondenza dell'argomento e a stampare gli eventi in stdout.

## <a name="run-flink-consumer"></a>Eseguire il consumer Flink

Usando l'esempio di consumer fornito, ricevere messaggi dall'hub eventi. 

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

Se l'hub eventi include eventi (ad esempio, se è in esecuzione anche `test`il producer), il consumer inizia ora a ricevere eventi dall'argomento.

Consultare la [Guida del connettore Kafka di Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) per altre informazioni sulla connessione di Flink a Kafka.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli hub eventi per Kafka, vedere gli articoli seguenti:To learn more about Event Hubs for Kafka, see the following articles:  

- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Integrazione di Kafka Connect con un hub eventi](event-hubs-kafka-connect-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka developer guide for Azure Event Hubs](apache-kafka-developer-guide.md)