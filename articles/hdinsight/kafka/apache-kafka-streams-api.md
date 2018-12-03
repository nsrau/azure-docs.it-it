---
title: "Esercitazione: Usare l'API Apache Kafka Streams - Azure HDInsight "
description: Informazioni su come usare l'API Apache Kafka Streams con Kafka in HDInsight. Questa API consente di eseguire l'elaborazione di flussi tra più argomenti in Kafka.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 8319376c597f16a5bfe1a357d74c59453b797e51
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495136"
---
# <a name="tutorial-apache-kafka-streams-api"></a>Esercitazione: API Apache Kafka Streams

Informazioni su come creare un'applicazione che usa l'API Apache Kafka Streams ed eseguirla con Kafka in HDInsight. 

In questa esercitazione viene usata un'applicazione di conteggio delle parole. L'applicazione legge i dati di testo da un argomento Kafka, estrae singole parole e quindi archivia il conteggio delle parole in un altro argomento Kafka.

> [!NOTE]
> L'elaborazione dei flussi di Kafka viene spesso eseguita usando Apache Spark o Apache Storm. Con Kafka versione 0.10.0 (in HDInsight 3.5 e 3.6) è stata introdotta l'API Kafka Streams, che consente di trasformare i flussi di dati tra argomenti di input e argomenti di output. In alcuni casi, questa può essere una valida alternativa alla creazione di una soluzione di streaming Spark or Storm. 
>
> Per altre informazioni su Kafka Streams, vedere la documentazione di [introduzione ai flussi](https://kafka.apache.org/10/documentation/streams/) su Apache.org.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurazione dell'ambiente di sviluppo
> * Informazioni sul codice
> * Compilare e distribuire l'applicazione
> * Configurare gli argomenti Kafka
> * Eseguire il codice

## <a name="prerequisites"></a>Prerequisiti

* Kafka nel cluster HDInsight 3.6. Per informazioni su come creare un cluster Kafka in HDInsight, vedere il documento su come [iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

* Completare i passaggi descritti nel documento [API Apache Kafka Producer e Consumer](apache-kafka-producer-consumer-api.md). La procedura descritta in questo documento usa l'applicazione e gli argomenti di esempio creati in questa esercitazione.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Nell'ambiente di sviluppo devono essere installati i componenti seguenti:

* [Java JDK 8](https://aka.ms/azure-jdks) o equivalente, ad esempio OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Client SSH e comando `scp`. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Informazioni sul codice

L'applicazione di esempio si trova in [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) nella sottodirectory `Streaming`. L'applicazione è costituita da due file:

* `pom.xml`: definisce le dipendenze di progetto, la versione di Java e i metodi di creazione dei pacchetti.
* `Stream.java`: implementa la logica di flusso.

### <a name="pomxml"></a>Pom.xml

Gli aspetti importanti da comprendere nel file `pom.xml` sono:

* Dipendenze: questo progetto si basa sull'API Kafka Streams, che è disponibile nel pacchetto `kafka-clients`. Il codice XML seguente definisce questa dipendenza:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > La voce `${kafka.version}` viene dichiarata nella sezione `<properties>..</properties>` di `pom.xml` ed è configurata per la versione Kafka del cluster HDInsight.

* Plug-in: i plug-in Maven offrono varie funzionalità. In questo progetto vengono usati i plug-in seguenti:

    * `maven-compiler-plugin`: usato per impostare su 8 la versione di Java usata dal progetto. Java 8 è richiesto da HDInsight 3.6.
    * `maven-shade-plugin`: usato per generare un file uberjar che contiene questa applicazione, nonché eventuali dipendenze. Viene inoltre usato per impostare il punto di ingresso dell'applicazione, in modo che sia possibile eseguire il file con estensione jar direttamente senza dover specificare la classe principale.

### <a name="streamjava"></a>Stream.Java

Il file [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) usa l'API Streams per implementare un'applicazione di conteggio delle parole. Legge i dati da un argomento Kafka denominato `test` e scrive il conteggio delle parole in un argomento denominato `wordcounts`.

Il codice seguente descrive l'applicazione di conteggio delle parole:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>Creare e distribuire l'esempio

Per creare e distribuire il progetto in un cluster Kafka in HDInsight, seguire questa procedura:

1. Scaricare gli esempi da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Per creare un pacchetto JAR, passare alla directory `Streaming` e usare il comando seguente:

    ```bash
    mvn clean package
    ```

    Questo comando crea il pacchetto in `target/kafka-streaming-1.0-SNAPSHOT.jar`.

3. Usare il comando seguente per copiare il file `kafka-streaming-1.0-SNAPSHOT.jar` nel cluster HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `clustername` con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH. Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

## <a name="create-apache-kafka-topics"></a>Creare gli argomenti di Apache Kafka

1. Per aprire una connessione SSH al cluster, usare il comando seguente:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `clustername` con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH. Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

2. Per salvare il nome del cluster in una variabile e installare un'utilità di analisi JSON (`jq`), usare i comandi seguenti. Al prompt, immettere il nome del cluster Kafka:

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. Per ottenere gli host del broker Kafka e gli host Apache Zookeeper, usare i comandi seguenti. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster. Viene richiesto di specificare la password due volte.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. Per creare gli argomenti usati dall'operazione di streaming, usare i comandi seguenti:

    > [!NOTE]
    > È possibile che venga visualizzato un errore che indica che l'argomento `test` esiste già. Non è un problema poiché potrebbe essere stato creato nell'esercitazione dell'API Producer e Consumer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Gli argomenti vengono usati per gli scopi seguenti:

    * `test`: in questo argomento vengono ricevuti i record. Applicazione di streaming legge i dati da questo argomento.
    * `wordcounts`: in questo argomento l'applicazione di streaming archivia l'output.
    * `RekeyedIntermediateTopic`: questo argomento viene usato per partizionare nuovamente i dati mentre il conteggio viene aggiornato dall'operatore `countByKey`.
    * `wordcount-example-Counts-changelog`: questo argomento è un archivio di stati usato dall'operazione `countByKey`.

    > [!IMPORTANT]
    > È possibile configurare Kafka in HDInsight anche in modo che gli argomenti vengano creati automaticamente. Per altre informazioni, vedere il documento [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Configurare la creazione automatica degli argomenti).

## <a name="run-the-code"></a>Eseguire il codice

1. Per avviare l'applicazione di streaming come processo in background, usare il comando seguente:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > È possibile che venga visualizzato un avviso relativo a log4j. Questo avviso può essere ignorato.

2. Per inviare i record all'argomento `test`, usare il comando seguente per avviare l'applicazione producer:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Al termine dell'elaborazione del producer, usare il comando seguente per visualizzare le informazioni archiviate nell'argomento `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > I parametri `--property` indicano al consumer di console di stampare sia la chiave (parola) sia il numero (valore). Questo parametro configura anche il deserializzatore da usare durante la lettura dei valori da Kafka.

    L'output è simile al testo seguente:
   
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
    > Il parametro `--from-beginning` configura il consumer in modo che venga avviato all'inizio dei record archiviati nell'argomento. Il conteggio viene incrementato ogni volta che viene rilevata una parola, pertanto l'argomento contiene più voci per ogni parola, con un numero crescente.

7. Usare __Ctrl + C__ per chiudere il producer. Usare ancora __Ctrl + C__ per chiudere l'applicazione e il consumer.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare l'API Apache Kafka Streams con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Apache Kafka](apache-kafka-mirroring.md)
