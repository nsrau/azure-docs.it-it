---
title: 'Esercitazione: Usare le API Apache Kafka Producer e Consumer - Azure HDInsight '
description: Informazioni su come usare l'API Apache Kafka Producer e Consumer con Kafka in HDInsight. In questa esercitazione si apprenderà come usare queste API con Kafka in HDInsight da un'applicazione Java.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 556fc1f04cc6a1d1b594bdd3787ed43d30f607c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091172"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Esercitazione: Usare le API Apache Kafka Producer e Consumer

Informazioni su come usare l'API Apache Kafka Producer e Consumer con Kafka in HDInsight.

L'API Kafka Producer consente alle applicazioni di inviare flussi di dati al cluster Kafka. L'API Kafka Consumer consente alle applicazioni di leggere flussi di dati dal cluster Kafka.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurazione dell'ambiente di sviluppo
> * Configurare l'ambiente di sviluppo
> * Informazioni sul codice
> * Compilare e distribuire l'applicazione
> * Eseguire l'applicazione nel cluster

Per altre informazioni sulle API, vedere la documentazione Apache sull'[API Producer](https://kafka.apache.org/documentation/#producerapi) e l'[API Consumer](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Nell'ambiente di sviluppo devono essere installati i componenti seguenti:

* [Java JDK 8](https://aka.ms/azure-jdks) o equivalente, ad esempio OpenJDK.

* [Apache Maven](https://maven.apache.org/)

* Client SSH e comando `scp`. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Un editor di testo o ambiente IDE Java.

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* `JAVA_HOME` - deve puntare alla directory dove è installato JDK.
* `PATH`: deve contenere i percorsi seguenti:

    * `JAVA_HOME` o il percorso equivalente.
    * `JAVA_HOME\bin` o il percorso equivalente.
    * Directory in cui è installato Maven.

## <a name="set-up-your-deployment-environment"></a>Configurare l'ambiente di sviluppo

Questa esercitazione richiede Apache Kafka in HDInsight 3.6. Per informazioni su come creare un cluster Kafka in HDInsight, vedere il documento su come [iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Informazioni sul codice

L'applicazione di esempio si trova in [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) nella sottodirectory `Producer-Consumer`. L'applicazione è costituita principalmente da quattro file:

* `pom.xml`: Questo file definisce le dipendenze progetto, la versione Java e i metodi di creazione pacchetti.
* `Producer.java`: Questo file invia frasi casuali a Kafka usando l'API Producer.
* `Consumer.java`: Questo file usa l'API Consumer per leggere i dati da Kafka ed esportarli in STDOUT.
* `Run.java`: L'interfaccia della riga di comando usata per eseguire il codice producer e consumer.

### <a name="pomxml"></a>Pom.xml

Gli aspetti importanti da comprendere nel file `pom.xml` sono:

* Dipendenze: Questo progetto si basa sulle API Producer e Consumer Kafka, che sono fornite dal pacchetto `kafka-clients`. Il codice XML seguente definisce questa dipendenza:

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

* Plug-in: I plug-in Maven offrono varie funzionalità. In questo progetto vengono usati i plug-in seguenti:

    * `maven-compiler-plugin`: Usato per impostare su 8 la versione Java usata dal progetto. Si tratta della versione di Java usata da HDInsight 3.6.
    * `maven-shade-plugin`: Usato per generare un file uberjar che contiene questa applicazione, nonché eventuali dipendenze. Viene inoltre usato per impostare il punto di ingresso dell'applicazione, in modo che sia possibile eseguire il file con estensione jar direttamente senza dover specificare la classe principale.

### <a name="producerjava"></a>Producer.java

Il producer comunica con gli host broker di Kafka (nodi di lavoro) e invia i dati a un argomento Kafka. Il frammento di codice seguente è tratto dal file [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) del [repository GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra come impostare le proprietà del producer:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Il consumer comunica con gli host broker Kafka (nodi di lavoro) e legge i record in un ciclo. Il frammento di codice seguente tratto dal file [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) imposta le proprietà del consumer:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

In questo codice, il consumer è configurato per la lettura dall'inizio dell'argomento (`auto.offset.reset` è impostato su `earliest`.)

### <a name="runjava"></a>Run.java

Il file [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornisce un'interfaccia della riga di comando che esegue il codice del producer o del consumer. È necessario fornire le informazioni sull'host broker di Kafka come parametro. È facoltativamente possibile includere un valore di id gruppo che viene usato dal processo consumer. Se si creano più istanze di consumer usando lo stesso id di gruppo, esse bilanceranno il carico durante la lettura dall'argomento.

## <a name="build-and-deploy-the-example"></a>Creare e distribuire l'esempio

È possibile ignorare i passaggi 1 e 2 per la compilazione e scaricare i file jar (kafka-producer-consumer.jar) predefiniti da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). È quindi possibile copiare il file jar nel cluster HDInsight.

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

2. Per creare gli argomenti Kafka usati in questo esempio, usare la procedura seguente:

    1. Per salvare il nome del cluster in una variabile e installare un'utilità di analisi JSON (`jq`), usare i comandi seguenti. Al prompt, immettere il nome del cluster Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Per ottenere gli host del broker Kafka e gli host Apache Zookeeper, usare i comandi seguenti. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.
    
        ```bash
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Per creare l'argomento `test`, usare il comando seguente:

        ```bash
        java -jar kafka-producer-consumer.jar create test $KAFKABROKERS
        ```

3. Per eseguire il producer e scrivere i dati nell'argomento, usare il comando seguente:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Quando l'esecuzione del producer è terminata, usare il comando seguente per leggere dall'argomento:

    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```

    Verranno visualizzati i record letti e il numero di record.

5. Usare __Ctrl + C__ per uscire dal consumer.

### <a name="multiple-consumers"></a>Consumer multipli

I consumer Kafka usano un gruppo di consumer durante la lettura dei record. L'uso dello stesso gruppo con più consumer consente di ottenere operazioni di lettura con bilanciamento del carico da un argomento. Ogni consumer nel gruppo riceve una parte dei record.

L'applicazione Consumer accetta un parametro che viene usato come ID del gruppo. Ad esempio, il comando seguente avvia un consumer usando un ID gruppo denominato `mygroup`:

```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Per verificare il funzionamento del processo, usare il comando seguente:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Questo comando usa `tmux` per suddividere il terminale in due colonne. Viene avviato un consumer in ogni colonna, con lo stesso valore di ID di gruppo. Dopo che i consumer hanno completato la lettura, si noti che ognuno di essi ha letto solo una parte dei record. Usare __Ctrl + C__ due volte per uscire da `tmux`.

Il consumo da parte dei client dello stesso gruppo viene gestito tramite le partizioni dell'argomento. In questo esempio di codice per l'argomento `test` creato in precedenza sono presenti otto partizioni. Se si avviano otto consumer, ognuno di essi legge i record da una singola partizione dell'argomento.

> [!IMPORTANT]  
> Un gruppo di consumer non può contenere un numero di istanze di consumer maggiore del numero di partizioni. In questo esempio, un gruppo di consumer può contenere fino a otto consumer perché è questo il numero di partizioni nell'argomento. In alternativa è possibile avere più gruppi di consumer, ognuno con al massimo otto consumer.

I record vengono archiviati in Kafka nell'ordine in cui vengono ricevuti in una partizione. Per ottenere il recapito dei record nell'ordine *all'interno di una partizione*, creare un gruppo di consumer con un numero di istanze corrispondente al numero di partizioni. Per ottenere il recapito dei record nell'ordine *all'interno dell'argomento*, creare un gruppo di consumer con una sola istanza.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare le API Apache Kafka Producer e Consumer con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Apache Kafka](apache-kafka-mirroring.md)
* [API Apache Kafka Streams con HDInsight](apache-kafka-streams-api.md)
