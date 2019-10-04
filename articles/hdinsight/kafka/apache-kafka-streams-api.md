---
title: "Esercitazione: Usare l'API Apache Kafka Streams - Azure HDInsight "
description: "Esercitazione: Informazioni su come usare l'API Apache Kafka Streams con Kafka in HDInsight. Questa API consente di eseguire l'elaborazione di flussi tra più argomenti in Kafka."
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 0639ecaa0e4ae0581a6c88e1ea9a47de870a8355
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446385"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Esercitazione: Usare l'API Apache Kafka Streams in Azure HDInsight

Informazioni su come creare un'applicazione che usa l'API Apache Kafka Streams ed eseguirla con Kafka in HDInsight.

In questa esercitazione viene usata un'applicazione di conteggio delle parole. L'applicazione legge i dati di testo da un argomento Kafka, estrae singole parole e quindi archivia il conteggio delle parole in un altro argomento Kafka.

L'elaborazione dei flussi di Kafka viene spesso eseguita usando Apache Spark o Apache Storm. Con Kafka versione 1.1.0 (in HDInsight 3.5 e 3.6) è stata introdotta l'API Kafka Streams, che consente di trasformare i flussi di dati tra argomenti di input e argomenti di output. In alcuni casi, questa può essere una valida alternativa alla creazione di una soluzione di streaming Spark or Storm.

Per altre informazioni su Kafka Streams, vedere la documentazione di [introduzione ai flussi](https://kafka.apache.org/10/documentation/streams/) su Apache.org.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Informazioni sul codice
> * Compilare e distribuire l'applicazione
> * Configurare gli argomenti Kafka
> * Eseguire il codice

## <a name="prerequisites"></a>Prerequisiti

* Kafka nel cluster HDInsight 3.6. Per informazioni su come creare un cluster Kafka in HDInsight, vedere il documento su come [iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

* Completare i passaggi descritti nel documento [API Apache Kafka Producer e Consumer](apache-kafka-producer-consumer-api.md). La procedura descritta in questo documento usa l'applicazione e gli argomenti di esempio creati in questa esercitazione.

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks) o equivalente, ad esempio OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Informazioni sul codice

L'applicazione di esempio si trova in [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) nella sottodirectory `Streaming`. L'applicazione è costituita da due file:

* `pom.xml`: Questo file definisce le dipendenze progetto, la versione Java e i metodi di creazione pacchetti.
* `Stream.java`: questo file implementa la logica di streaming.

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

    La voce `${kafka.version}` viene dichiarata nella sezione `<properties>..</properties>` di `pom.xml` ed è configurata per la versione Kafka del cluster HDInsight.

* Plug-in: I plug-in Maven offrono varie funzionalità. In questo progetto vengono usati i plug-in seguenti:

    * `maven-compiler-plugin`: Usato per impostare su 8 la versione Java usata dal progetto. Java 8 è richiesto da HDInsight 3.6.
    * `maven-shade-plugin`: Usato per generare un file uberjar che contiene questa applicazione, nonché eventuali dipendenze. Viene inoltre usato per impostare il punto di ingresso dell'applicazione, in modo che sia possibile eseguire il file con estensione jar direttamente senza dover specificare la classe principale.

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

1. Impostare la directory corrente sul percorso della directory `hdinsight-kafka-java-get-started-master\Streaming` e quindi usare il comando seguente per creare un pacchetto JAR:

    ```cmd
    mvn clean package
    ```

    Questo comando crea il pacchetto in `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `clustername` con il nome del cluster. Usare il comando seguente per copiare il file `kafka-streaming-1.0-SNAPSHOT.jar` nel cluster HDInsight. Quando richiesto, immettere la password per l'account utente SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Creare gli argomenti di Apache Kafka

1. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Per aprire una connessione SSH al cluster, immettere il comando seguente. Quando richiesto, immettere la password per l'account utente SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Installare [jq](https://stedolan.github.io/jq/), un processore JSON da riga di comando. Dalla connessione SSH aperta, immettere il comando seguente per installare `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Impostare le variabili di ambiente. Sostituire `PASSWORD` e `CLUSTERNAME` rispettivamente con la password di accesso al cluster e il nome del cluster, quindi immettere il comando:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Estrarre il nome del cluster con l'uso corretto di maiuscole e minuscole. L'uso effettivo di maiuscole e minuscole nel nome del cluster può differire dal previsto, a seconda della modalità di creazione del cluster. Questo comando ottiene le maiuscole e minuscole effettive, le archivia in una variabile e quindi visualizza il nome con le maiuscole e minuscole corrette e il nome specificato in precedenza. Immettere il comando seguente:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Per ottenere gli host del broker Kafka e gli host Apache Zookeeper, usare i comandi seguenti. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster. Viene richiesto di specificare la password due volte.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`;
    export KAFKABROKERS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`;
    ```

6. Per creare gli argomenti usati dall'operazione di streaming, usare i comandi seguenti:

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
   * `wordcount-example-Counts-changelog`: questo argomento è un archivio di stati usato dall'operazione `countByKey`

    È possibile configurare Kafka in HDInsight anche in modo che gli argomenti vengano creati automaticamente. Per altre informazioni, vedere il documento [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Configurare la creazione automatica degli argomenti).

## <a name="run-the-code"></a>Eseguire il codice

1. Per avviare l'applicazione di streaming come processo in background, usare il comando seguente:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    È possibile che venga visualizzato un avviso relativo ad Apache log4j. Questo avviso può essere ignorato.

2. Per inviare i record all'argomento `test`, usare il comando seguente per avviare l'applicazione producer:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Al termine dell'elaborazione del producer, usare il comando seguente per visualizzare le informazioni archiviate nell'argomento `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    I parametri `--property` indicano al consumer di console di stampare sia la chiave (parola) sia il numero (valore). Questo parametro configura anche il deserializzatore da usare durante la lettura dei valori da Kafka.

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
   
    Il parametro `--from-beginning` configura il consumer in modo che venga avviato all'inizio dei record archiviati nell'argomento. Il conteggio viene incrementato ogni volta che viene rilevata una parola, pertanto l'argomento contiene più voci per ogni parola, con un numero crescente.

4. Usare __Ctrl + C__ per chiudere il producer. Usare ancora __Ctrl + C__ per chiudere l'applicazione e il consumer.

5. Per eliminare gli argomenti usati dall'operazione di streaming, eseguire questi comandi:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa esercitazione, eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare l'API Apache Kafka Streams con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

> [!div class="nextstepaction"]
> [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)