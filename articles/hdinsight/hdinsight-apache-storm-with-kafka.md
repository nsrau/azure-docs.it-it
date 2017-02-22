---
title: Usare Apache Kafka con Storm in HDInsight | Documentazione Microsoft
description: Apache Kafka viene installato con Apache Storm in HDInsight. Informazioni su come scrivere in Kafka e leggere da Kafka usando componenti KafkaBolt e KafkaSpout forniti con Storm. Informazioni su come usare il framework Flux per definire e inviare topologie di Storm.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/09/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: c6171ad4077e833162bfad31b01abbc9947f8e45

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Usare Apache Kafka (anteprima) con Storm in HDInsight

Apache Kafka è una soluzione di messaggistica di pubblicazione-sottoscrizione disponibile con HDInsight. Apache Storm è un sistema distribuito che può essere usato per analizzare i dati in tempo reale. Questo documento illustra come usare Storm in HDInsight per leggere ed elaborare i dati da Kafka in HDInsight. L'esempio illustrato in questo documento usa una topologia di Storm basata su Java che si basa sui componenti Kafka spout e bolt disponibili con Apache Storm.

> [!NOTE]
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Storm in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Storm di comunicare direttamente con il cluster Kafka.
> 
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

## <a name="prerequisites"></a>Prerequisiti

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 o versione successiva o equivalente, ad esempio [OpenJDK](http://openjdk.java.net/).
  
    > [!NOTE]
    > La procedura descritta in questo documento fa uso di cluster HDInsight 3.5, che usa Java 8.

* [Maven 3. x](http://maven.apache.org/): pacchetto di gestione di compilazione per applicazioni Java.

* Un editor di testo o ambiente IDE Java

* Un client SSH, perché sono necessari i comandi `ssh` e `scp`. Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:
  
  * [Usare SSH con HDInsight basato su Linux da Linux, Unix e Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

  * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Storm e Kafka si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Storm e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Anche se Kafka è limitato alle comunicazioni all'interno della rete virtuale, è possibile accedere ad altri servizi del cluster tramite Internet, ad esempio SSH e Ambari. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).


Anche se è possibile creare manualmente cluster Storm e Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire cluster Storm e Kafka e una rete virtuale di Azure nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Il modello di Azure Resource Manager è disponibile all'indirizzo **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**.

2. Usare le linee guida seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
   
    ![Distribuzione personalizzata di HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.
   
    * **Località**: scegliere una località geograficamente vicina. La località deve corrispondere a quella indicata nella sezione __IMPOSTAZIONI__.

    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Storm e Kafka. Ad esempio, se si immette **hdi** viene creato un cluster Storm denominato **storm-hdi** e un cluster Kafka denominato **kafka-hdi**.
   
    * **Cluster Login User Name** (Nome utente di accesso del cluster): nome utente amministratore per i cluster Storm e Kafka.
   
    * **Cluster Login Password** (Password di accesso del cluster): password amministratore per i cluster Storm e Kafka.
    
    * **SSH User Name** (Nome utente SSH): utente SSH da creare per i cluster Storm e Kafka.
    
    * **SSH Password** (Password SSH): password dell'utente SSH per i cluster Storm e Kafka.
    
    * **Location** (Località): area in cui vengono creati i cluster.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

Dopo aver creato le risorse, si viene reindirizzati a un pannello del gruppo di risorse che contiene i cluster e il dashboard Web.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **storm-BASENAME** e **kafka-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="get-the-code"></a>Ottenere il codice

Il codice per l'esempio illustrato in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

## <a name="understanding-the-code"></a>Informazioni sul codice

Questo progetto contiene due topologie:

* **KafkaWriter**: questa topologia, definita dal file **writer.yaml**, scrive frasi casuali in Kafka usando il KafkaBolt fornito con Apache Storm.
  
    Questa topologia usa un componente **SentenceSpout** personalizzato per generare frasi casuali.

* **KafkaReader**: questa topologia, definita dal file **reader.yaml**, legge i dati da Kafka usando il KafkaSpout fornito con Apache Storm e quindi registra i dati in stdout.
  
    Questa topologia usa un componente **PrinterBolt** personalizzato per registrare i dati letti da Kafka.

### <a name="flux"></a>Flux

Le topologie vengono definite tramite [Flux](https://storm.apache.org/releases/1.0.1/flux.html). Flux è stato introdotto in Storm 0.10.x e consente di separare la configurazione della topologia dal codice. Per le topologie che fanno uso del framework Flux, la topologia viene definita in un file YAML. Il file YAML può essere incluso come parte della topologia o specificato quando si invia la topologia al server Storm. Flux supporta anche la sostituzione delle variabili in fase di esecuzione, caratteristica che viene usata in questo esempio.

Entrambe le topologie prevedono le variabili di ambiente seguenti:

* **KAFKATOPIC**: nome dell'argomento Kafka usato dalla topologie per la lettura/scrittura.

* **KAFKABROKERS**: host in cui vengono eseguiti i broker di Kafka. Le informazioni sui broker vengono usate da KafkaBolt durante la scrittura in Kafka.

* **KAFKAZKHOSTS**: host in cui viene eseguito Zookeeper.

La procedura descritta in questo documento illustra come impostare queste variabili di ambiente.

## <a name="create-a-kafka-topic"></a>Creare un argomento Kafka

1. Connettersi al cluster Kafka tramite SSH. Sostituire **USERNAME** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    Quando richiesto, immettere la password usata durante la creazione del cluster.
   
    Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:
   
    * [Usare SSH con HDInsight basato su Linux da Linux, Unix e Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Dalla connessione SSH al cluster Kafka, usare i comandi seguenti per ottenere i nodi Zookeeper da Ambari:

        # Install JQ to make working with JSON easier
        sudo apt -y install jq
        # Query Ambari for 
        KAFKAZKHOSTS=`curl -u admin:PASSWORD -G "http://headnodehost:8080/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
    
    Sostituire __PASSWORD__ con la password amministratore usata durante la creazione del cluster. Sostituire __BASENAME__ con il nome di base usato durante la creazione del cluster.

    Questo comando legge i valori per gli host di Zookeeper da Ambari e li archivia nella variabile KAFKAZKHOSTS. Per visualizzare tali valori, usare quanto segue:

        echo $KAFKAZKHOSTS
    
    L'output di questo comando è simile all'esempio seguente:

        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181

    Salvare i valori restituiti da questo comando, perché vengono usati per l'avvio della topologia nel cluster Storm.

    > [!NOTE]
    > I comandi precedenti usano __http://headnodehost:8080/__, che si connette direttamente ad Ambari. Per recuperare queste informazioni dall'esterno del cluster, tramite Internet, usare invece __https://kafka-BASENAME/__.

3. Usare il comando seguente per creare un argomento in Kafka:
   
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
   
    Questo comando si connette a Zookeeper usando le informazioni sull'host archiviate in `$KAFKAZKHOSTS` e quindi crea un argomento Kafka denominato **stormtest**. Per verificare che l'argomento sia stato creato è possibile usare il comando seguente per elencare gli argomenti:
   
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
   
    L'output di questo comando elenca gli argomenti Kafka, che devono includere il nuovo argomento **stormtest**.

Lasciare attiva la connessione SSH al cluster Kafka, perché è possibile usarla per verificare che la topologia di Storm scriva i messaggi nell'argomento.

## <a name="download-and-compile-the-project"></a>Scaricare e compilare il progetto

1. Nell'ambiente di sviluppo scaricare il progetto dall'indirizzo [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), aprire una riga di comando e passare al percorso in cui è stato scaricato il progetto.
   
    Esaminare il codice e il funzionamento del progetto.

2. Dalla directory **hdinsight-storm-java-kafka** usare il comando seguente per compilare il progetto e creare un pacchetto per la distribuzione:
   
        mvn clean package
   
    Il processo del pacchetto crea un file denominato `KafkaTopology-1.0-SNAPSHOT.jar` nella directory `target`.

3. Usare i comandi seguenti per copiare il pacchetto nel cluster Storm in HDInsight. Sostituire **USERNAME** con il nome utente SSH per il cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.
   
        scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   
    Quando richiesto, immettere la password usata durante la creazione del cluster.

4. Usare il comando seguente per copiare il file `set-env-variables.sh` dalla directory `scripts` del progetto nel cluster Storm:

        scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
    
    Questo script viene usato per impostare le variabili di ambiente usate dalle topologie di Storm per comunicare con il cluster Kafka.

## <a name="start-the-writer"></a>Avviare il writer

1. Usare quanto segue per connettersi al cluster Storm tramite SSH. Sostituire **USERNAME** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
   
    Quando richiesto, immettere la password usata durante la creazione del cluster.
   
    Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:
   
    * [Usare SSH con HDInsight basato su Linux da Linux, Unix e Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Dalla connessione SSH al cluster Storm usare i comandi seguenti per eseguire lo script `set-env-variables.sh`:

        chmod +x set-env-variables.sh
        . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD

    Sostituire __KAFKACLUSTERNAME__ con il nome del cluster Kafka. Sostituire __PASSWORD__ con la password dell'account di accesso amministratore per il cluster Kafka.

    Lo script si connette al cluster Kafka e recupera un elenco di broker Kafka e host Zookeeper. Le informazioni vengono quindi archiviate nelle variabili di ambiente usate dalle topologie Storm.

    L'output dello script è simile all'esempio seguente:

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Dalla connessione SSH al cluster Storm usare il comando seguente per avviare la topologia del writer:
   
        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e
   
    I parametri usati con questo comando sono i seguenti:
   
    * **org.apache.storm.flux.Flux**: permette di usare Flux per configurare ed eseguire questa topologia.
   
    * **--remote**: permette di inviare la topologia a Nimbus. La topologia viene distribuita ai nodi del ruolo di lavoro nel cluster.
   
    * **-R /writer.yaml**: permette di usare **writer.yaml** per configurare la topologia. `-R` indica che questa risorsa è inclusa nel file JAR. È nella radice del file JAR, quindi `/writer.yaml` è il relativo percorso.
   
    * **-e**: permette di usare la sostituzione della variabile di ambiente. Flux preleva i valori $KAFKABROKERS e $KAFKATOPIC impostati in precedenza e li usa nel file reader.yaml al posto delle voci `${ENV-KAFKABROKER}` e `${ENV-KAFKATOPIC}`.

5. Dopo aver avviato la topologia, passare alla connessione SSH al cluster Kafka e usare il comando seguente per visualizzare i messaggi scritti nell'argomento **stormtest**:
   
         /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
   
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
   
        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e

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

    storm kill kafka-writer
    storm kill kafka-reader

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. In questo modo vengono rimosse tutte le risorse create seguendo le istruzioni di questo documento, la rete virtuale di Azure e l'account di archiviazione usato dai cluster.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di topologie che possono essere usate con Storm in HDInsight, vedere [Esempi di topologie e componenti Storm per Apache Storm in HDInsight](hdinsight-storm-example-topology.md).

Per informazioni sulla distribuzione e sul monitoraggio di topologie in HDInsight basato su Linux, vedere [Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux](hdinsight-storm-deploy-monitor-topology-linux.md).




<!--HONumber=Jan17_HO3-->


