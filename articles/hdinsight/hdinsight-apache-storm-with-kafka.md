---
title: 'Esercitazione: Apache Kafka con Storm in HDInsight - Azure | Microsoft Docs'
description: Informazioni su come creare una pipeline di flusso usando Apache Storm e Apache Kafka in HDInsight. In questa esercitazione si usano i componenti KafkaBolt e KafkaSpout per trasmettere dati da Kafka.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2018
ms.author: larryfr
ms.openlocfilehash: 8baafd69e45210b74db8b0bf41b765067b1251a8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-use-apache-storm-with-kafka-on-hdinsight"></a>Esercitazione: Usare Apache Storm con Kafka in HDInsight

Questa esercitazione illustra come usare una topologia di Apache Storm per leggere e scrivere dati con Apache Kafka in HDInsight. Questa esercitazione illustra inoltre come rendere persistenti i dati nella risorsa di archiviazione compatibile con HDFS nel cluster Storm.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Storm e Kafka
> * Informazioni sul codice
> * Creare cluster Kafka e Storm
> * Creare la topologia
> * Configurare la topologia
> * Creare l'argomento Kafka
> * Avviare le topologie
> * Arrestare le topologie
> * Pulire le risorse

## <a name="prerequisites"></a>prerequisiti

* Familiarità con la creazione di argomenti Kafka. Per altre informazioni, vedere il documento [Creare un cluster Kafka in HDInsight](./kafka/apache-kafka-get-started.md).

* Familiarità con la compilazione e la distribuzione di soluzioni Storm (topologie). In particolare, le topologie che usano il framework Flux. Per altre informazioni, vedere il documento [Creare una topologia Storm in Java](./storm/apache-storm-develop-java-topology.md).

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva. Per HDInsight 3.5 o una versione successiva è necessario Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un client SSH (sono necessari i comandi `ssh` e `scp`). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* `JAVA_HOME` - deve puntare alla directory dove è installato JDK.
* `PATH`: deve contenere i percorsi seguenti:
  
    * `JAVA_HOME` o il percorso equivalente.
    * `JAVA_HOME\bin` o il percorso equivalente.
    * Directory in cui è installato Maven.

> [!IMPORTANT]
> La procedura descritta in questo documento richiede l'uso di un gruppo di risorse di Azure che contiene sia un cluster Storm in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
> 
> Per comodità, questo documento si collega a un modello in grado di creare tutte le risorse di Azure necessarie. 
>
> Per altre informazioni sull'uso di HDInsight in una rete virtuale, vedere il documento [Estendere Azure HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="storm-and-kafka"></a>Storm e Kafka

Apache Storm include numerosi componenti da usare con Kafka. In questa esercitazione vengono usati i componenti seguenti:

* `org.apache.storm.kafka.KafkaSpout`: questo componente legge i dati da Kafka. Il componente si basa sui componenti seguenti:

    * `org.apache.storm.kafka.SpoutConfig`: include la configurazione per il componente spout.

    * `org.apache.storm.spout.SchemeAsMultiScheme` e `org.apache.storm.kafka.StringScheme`: modalità con cui i dati in Kafka vengono trasformati in una tupla Storm.

* `org.apache.storm.kafka.bolt.KafkaBolt`: questo componente scrive i dati in Kafka. Il componente si basa sui componenti seguenti:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: descrive l'argomento in cui viene scritto.

    * `org.apache.kafka.common.serialization.StringSerializer`: configura il bolt per serializzare i dati come valore stringa.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: esegue il mapping della struttura di dati della tupla usata all'interno della topologia Storm con i campi archiviati in Kafka.

Questi componenti sono disponibili nel pacchetto di `org.apache.storm : storm-kafka`. Occorre usare la versione del pacchetto che corrisponde alla versione di Storm. Per HDInsight 3.6, la versione di Storm è 1.1.0.
Occorre anche il pacchetto di `org.apache.kafka : kafka_2.10`, che contiene componenti Kafka aggiuntivi. Occorre usare la versione del pacchetto che corrisponde alla versione di Kafka. Per HDInsight 3.6, la versione di Kafka è 0.10.0.0.

Il codice XML seguente rappresenta la dichiarazione di dipendenza nel `pom.xml` per un progetto Maven:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>0.10.0.0</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Informazioni sul codice

Il codice usato in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Questa esercitazione include due topologie:

* Kafka-writer: genera frasi casuali e le archivia in Kafka.

* Kafka-reader: legge i dati in Kafka e quindi li memorizza nell'archivio file compatibile con HDFS per il cluster Storm.

    > [!WARNING] 
    > Per consentire a Storm di usare la risorsa di archiviazione compatibile con HDFS usata da HDInsight, è necessaria un'azione script. Lo script installa vari file con estensione jar nel percorso `extlib` per Storm. Il modello in questa esercitazione usa automaticamente lo script durante la creazione del cluster.
    >
    > Se non si usa il modello in questo documento per creare il cluster Storm, è necessario applicare manualmente l'azione script al cluster.
    >
    > L'azione script si trova in `https://hdiconfigactions2.blob.core.windows.net/stormextlib/stormextlib.sh` e viene applicata ai nodi supervisore e nimbus del cluster Storm. Per altre informazioni sull'uso di azioni script, vedere il documento [Customize HDInsight using script actions](hdinsight-hadoop-customize-cluster-linux.md) (Personalizzare HDInsight tramite azioni script).

Le topologie vengono definite tramite [Flux](https://storm.apache.org/releases/1.1.2/flux.html). Flux è stato introdotto in Storm 0.10.x e consente di separare la configurazione della topologia dal codice. Per le topologie che fanno uso del framework Flux, la topologia viene definita in un file YAML. Il file YAML può essere incluso come parte della topologia. Può essere anche un file autonomo usato quando si invia la topologia. Flux supporta anche la sostituzione delle variabili in fase di esecuzione, caratteristica che viene usata in questo esempio.

I parametri seguenti vengono impostati in fase di esecuzione per queste topologie:

* `${kafka.topic}`: nome dell'argomento Kafka usato dalla topologie per la lettura/scrittura.

* `${kafka.broker.hosts}`: host in cui vengono eseguiti i broker di Kafka. Le informazioni sui broker vengono usate da KafkaBolt durante la scrittura in Kafka.

* `${kafka.zookeeper.hosts}`: host in cui viene eseguito Zookeeper nel cluster di Kafka.

* `${hdfs.url}`: URL del file system per il componente HDFSBolt. Indica se i dati vengono scritti in un account di Archiviazione di Azure o in Azure Data Lake Store.

* `${hdfs.write.dir}`: directory in cui vengono scritti i dati.

Per altre informazioni sulle topologie di Flux, vedere [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

### <a name="kafka-writer"></a>Kafka-writer

Nella topologia Kafka-writer il componente bolt di Kafka accetta due valori stringa come parametri. Questi parametri indicano quali campi di tupla vengono inviati a Kafka dal bolt come valori di __chiave__ e __messaggio__. La chiave viene usata per partizionare i dati in Kafka. Il messaggio corrisponde ai dati da archiviare.

In questo esempio, il componente `com.microsoft.example.SentenceSpout` genera una tupla che contiene due campi, `key` e `message`. Il bolt di Kafka estrae questi campi e ne invia i dati a Kafka.

I campi non devono necessariamente chiamarsi `key` e `message`. Questi nomi vengono usati in questo progetto per facilitare la comprensione del mapping.

Il codice YAML seguente è la definizione del componente Kafka-writer:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

Nella topologia Kafka-reader il componente spout legge i dati di Kafka come valori stringa. I dati vengono quindi scritti nel log di Storm dal componente di registrazione e nel file system compatibile con HDFS per il cluster Storm dal componente bolt HDFS.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Sostituzioni di proprietà

Il progetto contiene un file denominato `dev.properties` che viene usato per passare i parametri usati dalle topologie. Questo file definisce le proprietà seguenti:

| File dev.properties | DESCRIZIONE |
| --- | --- |
| `kafka.zookeeper.hosts` | Host Zookeeper per il cluster Kafka. |
| `kafka.broker.hosts` | Host broker Kafka (nodi ruolo di lavoro). |
| `kafka.topic` | Argomento Kafka usato dalle topologie. |
| `hdfs.write.dir` | Directory in cui scrive la topologia Kafka-reader. |
| `hdfs.url` | File system usato dal cluster Storm. Per gli account di Archiviazione di Azure, usare un valore di `wasb:///`. Per Azure Data Lake Store, usare un valore di `adl:///`. |

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Qualunque elemento che usa Kafka deve trovarsi nella stessa rete virtuale di Azure. In questa esercitazione, entrambi i cluster Kafka e Storm si trovano nella stessa rete virtuale di Azure. 

Il diagramma seguente illustra il flusso delle comunicazioni tra Storm e Kafka:

![Diagramma dei cluster Storm e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Altri servizi nel cluster, ad esempio SSH e Ambari, sono accessibili tramite Internet. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per creare una Rete virtuale di Microsoft Azure e quindi crearvi i cluster Kafka e Storm, procedere come segue:

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Il modello di Azure Resource Manager è disponibile in **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Crea le risorse seguenti:
    
    * Gruppo di risorse di Azure
    * Rete virtuale di Azure
    * Account di archiviazione di Azure
    * Kafka in HDInsight versione 3.6 con tre nodi di lavoro
    * Storm in HDInsight versione 3.6 con tre nodi di lavoro

  > [!WARNING]
  > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Questo modello crea un cluster Kafka contenente tre nodi di lavoro.

2. Usare le linee guida seguenti per popolare le voci nella sezione **Distribuzione personalizzata**:

    2. Usare le informazioni seguenti per popolare le voci nella sezione **Modello personalizzato**:

    | Impostazione | Valore |
    | --- | --- |
    | Sottoscrizione | Sottoscrizione di Azure |
    | Gruppo di risorse | Gruppo di risorse che contiene le risorse. |
    | Località | Area di Azure in cui vengono create le risorse. |
    | Nome del cluster Kafka | Nome del cluster Kafka. |
    | Nome del cluster Storm | Nome del cluster Storm. |
    | Nome utente dell'account di accesso del cluster | Nome utente dell'amministratore per i cluster. |
    | Password di accesso al cluster | Password dell'utente amministratore per i cluster. |
    | Nome utente SSH | Utente SSH da creare per i cluster. |
    | Password SSH | Password per l'utente SSH. |
   
    ![Immagine dei parametri del modello](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**.

> [!NOTE]
> La creazione dei cluster può richiedere fino a 20 minuti.

## <a name="build-the-topology"></a>Creare la topologia

1. Nell'ambiente di sviluppo scaricare il progetto dall'indirizzo [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), aprire una riga di comando e passare al percorso in cui è stato scaricato il progetto.

2. Dalla directory **hdinsight-storm-java-kafka** usare il comando seguente per compilare il progetto e creare un pacchetto per la distribuzione:

  ```bash
  mvn clean package
  ```

    Il processo del pacchetto crea un file denominato `KafkaTopology-1.0-SNAPSHOT.jar` nella directory `target`.

3. Usare i comandi seguenti per copiare il pacchetto nel cluster Storm in HDInsight. Sostituire `sshuser` con il nome utente SSH per il cluster. Sostituire `stormclustername` con il nome del cluster __Storm__.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Quando richiesto, immettere la password usata durante la creazione del cluster.

## <a name="configure-the-topology"></a>Configurare la topologia

1. Usare uno dei metodi seguenti per individuare gli host del broker Kafka per il cluster **Kafka** in HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > L'esempio di Bash seguente presuppone che `$CLUSTERNAME` contenga il nome del cluster __Kafka__. Presuppone anche che sia installato [jq](https://stedolan.github.io/jq/) versione 1.5 o una versione successiva. Quando richiesto, immettere la password dell'account di accesso al cluster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Il valore restituito è simile al testo seguente:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Anche se possono essere presenti più di due host broker per il cluster, non è necessario fornire un elenco completo di tutti gli host ai client. È sufficiente specificarne uno o due.

2. Usare uno dei metodi seguenti per individuare gli host Zookeeper per il cluster __Kafka__ in HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > L'esempio di Bash seguente presuppone che `$CLUSTERNAME` contenga il nome del cluster __Kafka__. Presuppone anche che [jq](https://stedolan.github.io/jq/) sia installato. Quando richiesto, immettere la password dell'account di accesso al cluster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Il valore restituito è simile al testo seguente:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Anche se sono presenti più di due nodi Zookeeper, non è necessario fornire un elenco completo di tutti gli host ai client. È sufficiente specificarne uno o due.

    Salvare questo valore, che verrà usato in un secondo momento.

3. Modificare il file `dev.properties` nella radice del progetto. Aggiungere le informazioni degli host Broker e Zookeeper del cluster __Kafka__ alle righe corrispondenti in questo file. Nell'esempio seguente viene configurato con i valori di esempio dei passaggi precedenti:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]
    > La voce `hdfs.url` viene configurata per un cluster che usa un account di Archiviazione di Azure. Per usare questa topologia con un cluster Storm che usa Data Lake Store, modificare questo valore da `wasb` in `adl`.

4. Salvare il file `dev.properties` e quindi usare il comando seguente per caricarlo nel cluster **Storm**:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Sostituire **USERNAME** con il nome utente SSH per il cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

## <a name="create-the-kafka-topic"></a>Creare l'argomento Kafka

Kafka archivia i dati in un _argomento_. È necessario creare l'argomento prima di avviare le topologie Storm. Per creare la topologia, seguire questa procedura:

1. Connettersi al cluster __Kafka__ tramite SSH usando il comando seguente. Sostituire `sshuser` con il nome utente SSH usato durante la creazione del cluster. Sostituire `kafkaclustername` con il nome del cluster Kafka:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Quando richiesto, immettere la password usata durante la creazione del cluster.
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Per creare l'argomento di Kafka, usare il comando seguente. Sostituire `$KAFKAZKHOSTS` con le informazioni dell'host Zookeeper usate durante la configurazione della topologia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando si connette a Zookeeper per il cluster Kafka e crea un nuovo argomento denominato `stormtopic`. Questo argomento viene usato dalle topologie Storm.

## <a name="start-the-writer"></a>Avviare il writer

1. Usare il codice seguente per connettersi al cluster **Storm** tramite SSH. Sostituire `sshuser` con il nome utente SSH usato durante la creazione del cluster. Sostituire `stormclustername` con il nome del cluster Storm:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Quando richiesto, immettere la password usata durante la creazione del cluster.
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dalla connessione SSH al cluster Storm usare il comando seguente per avviare la topologia del writer:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    I parametri usati con questo comando sono i seguenti:

    * `org.apache.storm.flux.Flux`: usa Flux per configurare ed eseguire questa topologia.

    * `--remote`: invia la topologia a Nimbus. La topologia viene distribuita ai nodi del ruolo di lavoro nel cluster.

    * `-R /writer.yaml`: usa il file `writer.yaml` per configurare la topologia. `-R` indica che questa risorsa è inclusa nel file JAR. È nella radice del file JAR, quindi `/writer.yaml` è il relativo percorso.

    * `--filter`: consente di compilare le voci nella topologia `writer.yaml` con i valori nel file `dev.properties`. Ad esempio, il valore della voce `kafka.topic` nel file viene usato per sostituire la voce `${kafka.topic}` nella definizione della topologia.

## <a name="start-the-reader"></a>Avviare il reader

1. Dalla sessione SSH nel cluster Storm usare il comando seguente per avviare la topologia del reader:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Attendere un minuto e quindi usare il comando seguente per visualizzare i file creati dalla topologia reader:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    L'output è simile al testo seguente:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. Per visualizzare il contenuto del file, usare il comando seguente. Sostituire `filename.txt` con il nome di un file:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    Il testo seguente è un esempio del contenuto del file:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Arrestare le topologie

Dalla sessione SSH nel cluster Storm usare i comandi seguenti per arrestare le topologie di Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa esercitazione, eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

> [!WARNING]
> La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più.
> 
> Se si elimina un cluster Kafka su HDInsight vengono eliminati anche eventuali dati archiviati in Kafka.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come usare una topologia Storm per scrivere e leggere dati da Kafka in HDInsight. Si è inoltre appreso come archiviare dati nella risorsa di archiviazione compatibile con HDFS usata da HDInsight.

Per altre informazioni sull'uso di Kafka in HDInsight, vedere il documento [Use Kafka Producer and Consumer API](kafka/apache-kafka-producer-consumer-api.md) (Usare la API Kafka Producer e Consumer).

Per informazioni sulla distribuzione e sul monitoraggio di topologie in HDInsight basato su Linux, vedere [Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux](storm/apache-storm-deploy-monitor-topology-linux.md).
