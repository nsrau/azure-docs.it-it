---
title: Usare l'API Apache Kafka Streams - Azure HDInsight | Microsoft Docs
description: "Informazioni su come usare l'API Apache Kafka Streams con Kafka in HDInsight. Questa API consente di eseguire l'elaborazione di flussi tra più argomenti in Kafka."
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
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 1ea20eceb28fead003c7279632b1e75ae1fd3553
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-streams-api"></a>API Apache Kafka Streams

Informazioni su come creare un'applicazione che usa l'API Kafka Streams ed eseguirla con Kafka in HDInsight.

Quando si usa Apache Kafka, l'elaborazione dei flussi viene spesso eseguita usando Apache Spark o Storm. Con Kafka versione 0.10.0 (in HDInsight 3.5 e 3.6) è stata introdotta l'API Kafka Streams, che consente di trasformare flussi di dati tra argomenti di input e output tramite un'applicazione eseguita in Kafka. In alcuni casi, questa può essere una valida alternativa alla creazione di una soluzione di streaming Spark or Storm. Per altre informazioni su Kafka Streams, vedere la documentazione di [introduzione ai flussi](https://kafka.apache.org/10/documentation/streams/) su Apache.org.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Nell'ambiente di sviluppo devono essere installati i componenti seguenti:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, ad esempio OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Client SSH e comando `scp`. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configurare l'ambiente di sviluppo

Questo esempio richiede Kafka in HDInsight 3.6. Per informazioni su come creare un cluster Kafka in HDInsight, vedere il documento su come [iniziare a usare Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Creare e distribuire l'esempio

Seguire questa procedura per creare e distribuire il progetto in un cluster Kafka in HDInsight.

1. Scaricare gli esempi da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Per creare un pacchetto JAR, passare alla directory `Streaming` e usare il comando seguente:

    ```bash
    mvn clean package
    ```

    Questo comando crea una directory denominata `target`, che contiene un file denominato `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Usare il comando seguente per copiare il file `kafka-streaming-1.0-SNAPSHOT.jar` nel cluster HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Sostituire **SSHUSER** con l'utente SSH del cluster e **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH. Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

## <a name="run-the-example"></a>Eseguire l'esempio

1. Per aprire una connessione SSH al cluster, usare il comando seguente:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Sostituire **SSHUSER** con l'utente SSH del cluster e **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH. Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

4. Per creare gli argomenti Kafka adottati in questo esempio, usare i comandi seguenti:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Sostituire __your cluster name__ con il nome del cluster HDInsight. Quando richiesto, immettere la password dell'account di accesso al cluster HDInsight.

    > [!NOTE]
    > Se l'account di accesso del cluster è diverso dal valore predefinito di `admin`, nei comandi precedenti sostituire il valore `admin` con il nome dell'account di accesso del cluster in uso.

5. Per completare questo esempio, è necessario eseguire tre operazioni:

    * Avviare la soluzione Streams contenuta in `kafka-streaming.jar`.
    * Avviare un producer che scrive nell'argomento `test`.
    * Avviare un consumer in modo da poter visualizzare l'output scritto nell'argomento `wordcounts`

    Per eseguire queste operazioni, è possibile aprire tre sessioni SSH. Per ogni sessione SSH, tuttavia, è necessario impostare `$KAFKABROKERS` e `$KAFKAZKHOSTS` eseguendo in ciascuna di esse il passaggio 4 di questa sezione. Una soluzione più semplice consiste nell'uso dell'utility `tmux`, che consente di dividere la visualizzazione SSH corrente in più sezioni. Per avviare il flusso, il producer e il consumer tramite `tmux`, usare il comando seguente:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Questo comando suddivide la visualizzazione SSH in tre sezioni:

    * Nella sezione sinistra viene eseguito un consumer di console, che legge i messaggi dall'argomento `wordcounts`: `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > I parametri `--property` indicano al consumer di console di stampare sia la chiave (parola) sia il numero (valore). Questo parametro configura anche il deserializzatore da usare durante la lettura dei valori da Kafka.

    * Nella sezione superiore destra viene eseguita la soluzione API Streams: `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * Nella sezione inferiore destra viene eseguito il producer di console e si attende che l'utente immetta i messaggi da inviare all'argomento `test`: `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Quando viene suddivisa la visualizzazione con il comando `tmux`, il cursore si posiziona nella sezione inferiore destra. Iniziare a immettere le frasi. Dopo ogni frase, il riquadro a sinistra viene aggiornato in modo da visualizzare un numero di parole univoche. L'output è simile al testo seguente:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > Il conteggio viene incrementato ogni volta che viene rilevata una parola.

7. Usare __Ctrl + C__ per chiudere il producer. Usare ancora __Ctrl + C__ per chiudere l'applicazione e il consumer.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare l'API Kafka Streams con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Analizzare i log di Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Kafka](apache-kafka-mirroring.md)
* [API per producer e consumer Kafka con HDInsight](apache-kafka-producer-consumer-api.md)
* [Usare lo streaming Apache Spark (DStream) con Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usare lo streaming strutturato Apache Spark con Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Usare lo streaming strutturato Apache Spark per spostare dati da Kafka in HDInsight a Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)