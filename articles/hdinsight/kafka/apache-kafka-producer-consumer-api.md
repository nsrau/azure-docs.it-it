---
title: 'Esercitazione: Usare le API Apache Kafka Producer e Consumer - Azure HDInsight '
description: Informazioni su come usare l'API Apache Kafka Producer e Consumer con Kafka in HDInsight. In questa esercitazione si apprenderà come usare queste API con Kafka in HDInsight da un'applicazione Java.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/16/2018
ms.openlocfilehash: f757db47ff91537405b04dbc949797f5855b7952
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416172"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Esercitazione: Usare le API Apache Kafka Producer e Consumer

Informazioni su come usare le API Kafka Producer e Consumer con Kafka in HDInsight.

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

* [Apache Maven](http://maven.apache.org/)

* Client SSH e comando `scp`. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Un editor di testo o ambiente IDE Java.

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* `JAVA_HOME` - deve puntare alla directory dove è installato JDK.
* `PATH`: deve contenere i percorsi seguenti:
  
    * `JAVA_HOME` o il percorso equivalente.
    * `JAVA_HOME\bin` o il percorso equivalente.
    * Directory in cui è installato Maven.

## <a name="set-up-your-deployment-environment"></a>Configurare l'ambiente di sviluppo

Questa esercitazione richiede Kafka in HDInsight 3.6. Per informazioni su come creare un cluster Kafka in HDInsight, vedere il documento su come [iniziare a usare Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Informazioni sul codice

L'applicazione di esempio si trova in [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) nella sottodirectory `Producer-Consumer`. L'applicazione è costituita principalmente da quattro file:

* `pom.xml`: definisce le dipendenze di progetto, la versione di Java e i metodi di creazione dei pacchetti.
* `Producer.java`: Questo file invia 1 milione (1.000.000) di frasi casuali a Kafka usando l'API Producer.
* `Consumer.java`: Questo file usa l'API Consumer per leggere i dati da Kafka ed esportarli in STDOUT.
* `Run.java`: L'interfaccia della riga di comando usata per eseguire il codice producer e consumer.

### <a name="pomxml"></a>Pom.xml

Gli aspetti importanti da comprendere nel file `pom.xml` sono:

* Le dipendenze: questo progetto si basa sulle API Producer e Consumer Kafka, che sono fornite dal pacchetto `kafka-clients`. Il codice XML seguente definisce questa dipendenza:

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

    * `maven-compiler-plugin`: usato per impostare su 8 la versione di Java usata dal progetto. Si tratta della versione di Java usata da HDInsight 3.6.
    * `maven-shade-plugin`: usato per generare un file uberjar che contiene questa applicazione, nonché eventuali dipendenze. Viene inoltre usato per impostare il punto di ingresso dell'applicazione, in modo che sia possibile eseguire il file con estensione jar direttamente senza dover specificare la classe principale.

### <a name="producerjava"></a>Producer.java

Il producer comunica con gli host broker di Kafka (nodi di lavoro) per archiviare i dati in un argomento Kafka. Il frammento di codice seguente proviene dal file `Producer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

Questo codice si connette agli host broker Kafka (nodi di lavoro) e quindi Invia 1.000.000 frasi a Kafka usando l'API Producer.

### <a name="consumerjava"></a>Consumer.java

Il consumer comunica con gli host broker Kafka (nodi di lavoro) e legge i record in un ciclo. Il frammento di codice seguente proviene dal file `Consumer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
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

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

In questo codice, il consumer è configurato per la lettura dall'inizio dell'argomento (`auto.offset.reset` è impostato su `earliest`.)

### <a name="runjava"></a>Run.java

Il file `Run.java` fornisce un'interfaccia della riga di comando che esegue il codice il producer o consumer. È necessario fornire le informazioni sull'host broker di Kafka come parametro. È facoltativamente possibile includere un valore di id gruppo che viene usato dal processo consumer. Se si creano più istanze di consumer usando lo stesso id di gruppo, esse bilanceranno il carico durante la lettura dall'argomento.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

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

2. Per creare gli argomenti Kafka usati in questo esempio, usare la procedura seguente:

    1. Per salvare il nome del cluster in una variabile e installare un'utilità di analisi JSON (`jq`), usare i comandi seguenti. Al prompt, immettere il nome del cluster Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Per ottenere gli host del broker Kafka e gli host Zookeeper, usare i comandi seguenti. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Per creare l'argomento `test`, usare il comando seguente:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```
    4. È anche possibile usare il file JAR per creare un argomento. Ad esempio, per creare l'argomento `test2`, usare il comando seguente:

        ```bash
        java -jar kafka-producer-consumer.jar create test2 $KAFKABROKERS
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

Questo comando usa `tmux` per suddividere il terminale in due colonne. Viene avviato un consumer in ogni colonna, con lo stesso valore di ID di gruppo. Dopo che i consumer hanno completato la lettura, si noti che ognuno di essi ha letto solo una parte dei record. Usare __Ctrl + __ C due volte per uscire da `tmux`.

Il consumo da parte dei client dello stesso gruppo viene gestito tramite le partizioni dell'argomento. Per l'argomento `test` creato in precedenza sono presenti otto partizioni. Se si avviano otto consumer, ognuno di essi legge i record da una singola partizione dell'argomento.

> [!IMPORTANT]
> Un gruppo di consumer non può contenere un numero di istanze di consumer maggiore del numero di partizioni. In questo esempio, un gruppo di consumer può contenere fino a otto consumer perché è questo il numero di partizioni nell'argomento. In alternativa è possibile avere più gruppi di consumer, ognuno con al massimo otto consumer.

I record vengono archiviati in Kafka nell'ordine in cui vengono ricevuti in una partizione. Per ottenere il recapito dei record nell'ordine *all'interno di una partizione*, creare un gruppo di consumer con un numero di istanze corrispondente al numero di partizioni. Per ottenere il recapito dei record nell'ordine *all'interno dell'argomento*, creare un gruppo di consumer con una sola istanza.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare le API Kafka Producer e Consumer con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Analizzare i log di Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Kafka](apache-kafka-mirroring.md)
* [API Kafka Streams con HDInsight](apache-kafka-streams-api.md)
