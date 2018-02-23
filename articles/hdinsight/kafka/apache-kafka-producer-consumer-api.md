---
title: Usare le API Apache Kafka Producer e Consumer - Azure HDInsight | Microsoft Docs
description: Informazioni su come usare l'API Apache Kafka Producer e Consumer con Kafka in HDInsight. Queste API consentono di sviluppare applicazioni che eseguono operazioni di scrittura e lettura in Apache Kafka.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: b57745d6bd993a993e923c964327d9071e745413
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>API Apache Kafka Producer e Consumer

Informazioni su come creare un'applicazione che usa le API Kafka Producer e Consumer con Kafka in HDInsight.

Per la documentazione sulle API, vedere [API Producer](https://kafka.apache.org/documentation/#producerapi) e [API Consumer](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Nell'ambiente di sviluppo devono essere installati i componenti seguenti:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, ad esempio OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Client SSH e comando `scp`. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configurare l'ambiente di sviluppo

Questo esempio richiede Kafka in HDInsight 3.6. Per informazioni su come creare un cluster Kafka in HDInsight, vedere il documento su come [iniziare a usare Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Creare e distribuire l'esempio

1. Scaricare gli esempi da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Passare alla directory `Producer-Consumer` e usare il comando seguente:

    ```
    mvn clean package
    ```

    Questo comando crea una directory denominata `target`, che contiene un file denominato `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Usare i comandi seguenti per copiare il file `kafka-producer-consumer-1.0-SNAPSHOT.jar` nel cluster HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Sostituire **SSHUSER** con l'utente SSH del cluster e **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'utente SSH.

## <a id="run"></a> Eseguire l'esempio

1. Per aprire una connessione SSH al cluster, usare il comando seguente:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Sostituire **SSHUSER** con l'utente SSH del cluster e **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH. Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

2. Per creare gli argomenti Kafka usati in questo esempio, usare i comandi seguenti:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Sostituire __your cluster name__ con il nome del cluster HDInsight. Quando richiesto, immettere la password dell'account di accesso al cluster HDInsight.

    > [!NOTE]
    > Se l'account di accesso del cluster è diverso dal valore predefinito di `admin`, nei comandi precedenti sostituire il valore `admin` con il nome dell'account di accesso al cluster personale.

3. Per eseguire il producer e scrivere i dati nell'argomento, usare il comando seguente:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Quando l'esecuzione del producer è terminata, usare il comando seguente per leggere dall'argomento:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Verranno visualizzati i record letti e il numero di record.

5. Usare __Ctrl + C__ per uscire dal consumer.

### <a name="multiple-consumers"></a>Consumer multipli

I consumer Kafka usano un gruppo di consumer durante la lettura dei record. L'uso dello stesso gruppo con più consumer consente di ottenere operazioni di lettura con bilanciamento del carico da un argomento. Ogni consumer nel gruppo riceve una parte dei record.

L'applicazione Consumer accetta un parametro che viene usato come ID del gruppo. Ad esempio, il comando seguente avvia un consumer usando un ID gruppo denominato `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Per verificare il funzionamento del processo, seguire questa procedura:

1. Ripetere i passaggi 1 e 2 descritti nella sezione [Eseguire l'esempio](#run) per aprire una nuova sessione SSH.

2. In entrambe le sessioni SSH immettere il comando seguente:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Immettere entrambi i comandi contemporaneamente, in modo che siano entrambi in esecuzione nello stesso momento.

    Il conteggio delle letture dei messaggi non è quello della sezione precedente, in cui è presente solo un consumer. Le letture dei messaggi vengono invece suddivise tra le istanze.

Il consumo da parte dei client dello stesso gruppo viene gestito tramite le partizioni dell'argomento. Per l'argomento `test` creato in precedenza sono presenti otto partizioni. Se si aprono otto sessioni SSH e si avvia un consumer in tutte le sessioni, ogni consumer legge i record da una singola partizione dell'argomento.

> [!IMPORTANT]
> Un gruppo di consumer non può contenere un numero di istanze di consumer maggiore del numero di partizioni. In questo esempio, un gruppo di consumer può contenere fino a otto consumer perché è questo il numero di partizioni nell'argomento. In alternativa è possibile avere più gruppi di consumer, ognuno con al massimo otto consumer.

I record vengono archiviati in Kafka nell'ordine in cui vengono ricevuti in una partizione. Per ottenere il recapito dei record nell'ordine *all'interno di una partizione*, creare un gruppo di consumer con un numero di istanze corrispondente al numero di partizioni. Per ottenere il recapito dei record nell'ordine *all'interno dell'argomento*, creare un gruppo di consumer con una sola istanza.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare le API Kafka Producer e Consumer con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Analizzare i log di Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Kafka](apache-kafka-mirroring.md)
* [API Kafka Streams con HDInsight](apache-kafka-streams-api.md)
* [Usare lo streaming Apache Spark (DStream) con Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usare lo streaming strutturato Apache Spark con Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Usare lo streaming strutturato Apache Spark per spostare dati da Kafka in HDInsight a Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)