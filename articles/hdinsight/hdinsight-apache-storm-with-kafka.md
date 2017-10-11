---
title: Usare Apache Kafka con Storm in HDInsight di Azure | Microsoft Docs
description: Apache Kafka viene installato con Apache Storm in HDInsight. Informazioni su come scrivere in Kafka e leggere da Kafka usando componenti KafkaBolt e KafkaSpout forniti con Storm. Informazioni su come usare il framework Flux per definire e inviare topologie di Storm.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/21/2017
ms.author: larryfr
ms.openlocfilehash: e8895ef3c11aea48513e4060a20f5f49b11fc961
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Usare Apache Kafka (anteprima) con Storm in HDInsight

Informazioni su come usare Apache Storm per leggere e scrivere in Apache Kafka. Questo esempio illustra anche come salvare i dati da una topologia di Storm nel file system compatibile con HDFS usato da HDInsight.

> [!NOTE]
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Storm in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Storm di comunicare direttamente con il cluster Kafka.
> 
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

## <a name="get-the-code"></a>Ottenere il codice

Il codice per l'esempio usato in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Per compilare questo progetto, è necessaria la seguente configurazione per l'ambiente di sviluppo:

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o versione successiva. Per HDInsight 3.5 o una versione successiva è necessario Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un client SSH (sono necessari i comandi `ssh` e `scp`). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un editor di testo o ambiente IDE.

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* `JAVA_HOME` - deve puntare alla directory dove è installato JDK.
* `PATH`: deve contenere i percorsi seguenti:
  
    * `JAVA_HOME` o il percorso equivalente.
    * `JAVA_HOME\bin` o il percorso equivalente.
    * Directory in cui è installato Maven.

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Storm e Kafka si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Storm e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Altri servizi nel cluster, ad esempio SSH e Ambari, sono accessibili tramite Internet. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Anche se è possibile creare manualmente cluster Storm e Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire cluster Storm e Kafka e una rete virtuale di Azure nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Il modello di Azure Resource Manager è disponibile all'indirizzo **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v1.json**. Crea le risorse seguenti:
    
    * Gruppo di risorse di Azure
    * Rete virtuale di Azure
    * Account di archiviazione di Azure
    * Kafka in HDInsight versione 3.6 con tre nodi di lavoro
    * Storm in HDInsight versione 3.6 con tre nodi di lavoro

  > [!WARNING]
  > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Questo modello crea un cluster Kafka contenente tre nodi di lavoro.

2. Usare le linee guida seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
   
    ![Distribuzione personalizzata di HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.
   
    * **Località**: scegliere una località geograficamente vicina.

    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Storm e Kafka. Ad esempio, se si immette **hdi** viene creato un cluster Storm denominato **storm-hdi** e un cluster Kafka denominato **kafka-hdi**.
   
    * **Cluster Login User Name** (Nome utente di accesso del cluster): nome utente amministratore per i cluster Storm e Kafka.
   
    * **Cluster Login Password** (Password di accesso del cluster): password amministratore per i cluster Storm e Kafka.
    
    * **SSH User Name** (Nome utente SSH): utente SSH da creare per i cluster Storm e Kafka.
    
    * **SSH Password** (Password SSH): password dell'utente SSH per i cluster Storm e Kafka.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

Dopo avere creato le risorse, viene visualizzato il pannello del gruppo di risorse.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **storm-BASENAME** e **kafka-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="understanding-the-code"></a>Informazioni sul codice

Questo progetto contiene due topologie:

* **KafkaWriter**: questa topologia, definita dal file **writer.yaml**, scrive frasi casuali in Kafka usando il KafkaBolt fornito con Apache Storm.

    Questa topologia usa un componente **SentenceSpout** personalizzato per generare frasi casuali.

* **KafkaReader**: questa topologia, definita dal file **reader.yaml**, legge i dati da Kafka usando il KafkaSpout fornito con Apache Storm e quindi registra i dati in stdout.

    Questa topologia usa iStorm HdfsBolt per scrivere dati nell'archivio predefinito per il cluster Storm.
### <a name="flux"></a>Flux

Le topologie vengono definite tramite [Flux](https://storm.apache.org/releases/1.1.0/flux.html). Flux è stato introdotto in Storm 0.10.x e consente di separare la configurazione della topologia dal codice. Per le topologie che fanno uso del framework Flux, la topologia viene definita in un file YAML. Il file YAML può essere incluso come parte della topologia. Può essere anche un file autonomo usato quando si invia la topologia. Flux supporta anche la sostituzione delle variabili in fase di esecuzione, caratteristica che viene usata in questo esempio.

I parametri seguenti vengono impostati in fase di esecuzione per queste topologie:

* `${kafka.topic}`: nome dell'argomento Kafka usato dalla topologie per la lettura/scrittura.

* `${kafka.broker.hosts}`: host in cui vengono eseguiti i broker di Kafka. Le informazioni sui broker vengono usate da KafkaBolt durante la scrittura in Kafka.

* `${kafka.zookeeper.hosts}`: host in cui viene eseguito Zookeeper nel cluster di Kafka.

Per altre informazioni sulle topologie di Flux, vedere [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="download-and-compile-the-project"></a>Scaricare e compilare il progetto

1. Nell'ambiente di sviluppo scaricare il progetto dall'indirizzo [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), aprire una riga di comando e passare al percorso in cui è stato scaricato il progetto.

2. Dalla directory **hdinsight-storm-java-kafka** usare il comando seguente per compilare il progetto e creare un pacchetto per la distribuzione:

  ```bash
  mvn clean package
  ```

    Il processo del pacchetto crea un file denominato `KafkaTopology-1.0-SNAPSHOT.jar` nella directory `target`.

3. Usare i comandi seguenti per copiare il pacchetto nel cluster Storm in HDInsight. Sostituire **USERNAME** con il nome utente SSH per il cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Quando richiesto, immettere la password usata durante la creazione del cluster.

## <a name="configure-the-topology"></a>Configurare la topologia

1. Usare uno dei metodi seguenti per individuare gli host del broker Kafka:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash di esempio presuppone che `$CLUSTERNAME` contenga il nome del cluster HDInsight. Presuppone anche che [jq](https://stedolan.github.io/jq/) sia installato. Quando richiesto, immettere la password dell'account di accesso al cluster.

    Il valore restituito è simile al testo seguente:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Anche se possono essere presenti più di due host broker per il cluster, non è necessario fornire un elenco completo di tutti gli host ai client. È sufficiente specificarne uno o due.

2. Usare uno dei metodi seguenti per individuare gli host di Kafka Zookeeper:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash di esempio presuppone che `$CLUSTERNAME` contenga il nome del cluster HDInsight. Presuppone anche che [jq](https://stedolan.github.io/jq/) sia installato. Quando richiesto, immettere la password dell'account di accesso al cluster.

    Il valore restituito è simile al testo seguente:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Anche se sono presenti più di due nodi Zookeeper, non è necessario fornire un elenco completo di tutti gli host ai client. È sufficiente specificarne uno o due.

    Salvare questo valore, che verrà usato in un secondo momento.

3. Modificare il file `dev.properties` nella radice del progetto. Aggiungere le informazioni di host di Broker e Zookeeper per le righe corrispondenti in questo file. Nell'esempio seguente viene configurato con i valori di esempio dei passaggi precedenti:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Salvare il file `dev.properties` e quindi usare il comando seguente per caricarlo nel cluster Storm:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    Sostituire **USERNAME** con il nome utente SSH per il cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

## <a name="start-the-writer"></a>Avviare il writer

1. Usare quanto segue per connettersi al cluster Storm tramite SSH. Sostituire **USERNAME** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Quando richiesto, immettere la password usata durante la creazione del cluster.
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dalla connessione SSH, usare il comando seguente per creare un argomento Kafka usato dalla topologia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Sostituire `$KAFKAZKHOSTS` con l'informazione host di Zookeeper recuperata nella sezione precedente.

2. Dalla connessione SSH al cluster Storm usare il comando seguente per avviare la topologia del writer:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    I parametri usati con questo comando sono i seguenti:

    * `org.apache.storm.flux.Flux`: usa Flux per configurare ed eseguire questa topologia.

    * `--remote`: invia la topologia a Nimbus. La topologia viene distribuita ai nodi del ruolo di lavoro nel cluster.

    * `-R /writer.yaml`: usa il file `writer.yaml` per configurare la topologia. `-R` indica che questa risorsa è inclusa nel file JAR. È nella radice del file JAR, quindi `/writer.yaml` è il relativo percorso.

    * `--filter`: consente di compilare le voci nella topologia `writer.yaml` con i valori nel file `dev.properties`. Ad esempio, il valore della voce `kafka.topic` nel file viene usato per sostituire la voce `${kafka.topic}` nella definizione della topologia.

5. Dopo aver avviato la topologia, usare il comando seguente per verificare che nell'argomento Kafka vengano scritti i dati:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Sostituire `$KAFKAZKHOSTS` con l'informazione host di Zookeeper recuperata nella sezione precedente.

    Questo comando usa uno script fornito con Kafka per monitorare l'argomento. Dopo qualche secondo dovrebbe iniziare a restituire frasi casuali che sono state scritte nell'argomento. L'output è simile all'esempio seguente:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Usare CTRL+C per arrestare lo script.

## <a name="start-the-reader"></a>Avviare il reader

1. Dalla sessione SSH nel cluster Storm usare il comando seguente per avviare la topologia del reader:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Dopo aver avviato la topologia, aprire l'interfaccia utente di Storm. Questa interfaccia utente Web è disponibile all'indirizzo https://storm-BASENAME.azurehdinsight.net/stormui. Sostituire __BASENAME__ con il nome di base usato durante la creazione del cluster. 

    Quando richiesto, usare il nome dell'account di accesso amministratore, la cui impostazione predefinita è `admin`, e la password usati durante la creazione del cluster. Verrà visualizzata una pagina Web simile all'immagine seguente:

    ![Interfaccia utente di Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Dall'interfaccia utente di Storm, selezionare il collegamento __kafka-reader__ nella sezione __Topology Summary__ (Riepilogo topologia) per visualizzare informazioni sulla topologia __kafka-reader__.

    ![Sezione di riepilogo della topologia dell'interfaccia utente Web di Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Selezionare il collegamento __logger-bolt__ nella sezione __Bolts (All time)__ (Bolt - Intero periodo) per visualizzare informazioni sulle istanze del componente logger-bolt.

    ![Collegamento logger-bolt nella sezione dei bolt](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. Nella sezione __Executors__ (Esecutori) selezionare un collegamento nella colonna __Port__ (Porta) per visualizzare le informazioni di registrazione su questa istanza del componente.

    ![Collegamento esecutori](./media/hdinsight-apache-storm-with-kafka/executors.png)

    Il log contiene un elenco dei dati letti dall'argomento Kafka. Le informazioni contenute nel log sono simili all'esempio seguente:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Arrestare le topologie

Dalla sessione SSH nel cluster Storm usare i comandi seguenti per arrestare le topologie di Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. Se si elimina il gruppo di risorse, tutte le risorse create seguendo questo documento vengono rimosse.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di topologie che possono essere usate con Storm in HDInsight, vedere [Esempi di topologie e componenti Storm per Apache Storm in HDInsight](hdinsight-storm-example-topology.md).

Per informazioni sulla distribuzione e sul monitoraggio di topologie in HDInsight basato su Linux, vedere [Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux](hdinsight-storm-deploy-monitor-topology-linux.md).