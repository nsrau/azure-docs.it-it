---
title: Introduzione ad Apache Kafka in HDInsight| Microsoft Docs
description: Informazioni di base sulla creazione e sull&quot;uso di Kafka in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 34c1138a9c3b9313a12ffbf4bc4c3141db0a016e
ms.openlocfilehash: 98b11144c049f9db780c7665610c83a753f23b21

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Introduzione ad Apache Kafka (anteprima) in HDInsight

[Apache Kafka](https://kafka.apache.org) è una piattaforma di streaming distribuita open source disponibile con HDInsight. Viene spesso usata come broker di messaggi perché mette a disposizione funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione. Questo documento illustra come creare un cluster Kafka in HDInsight e quindi inviare e ricevere dati da un'applicazione Java.

> [!NOTE]
> Sono attualmente disponibili due versioni di Kafka con HDInsight; 0.9.0 (HDInsight 3.4) e 0.10.0 (HDInsight 3.5). I passaggi in questo documento presuppongono che si usi Kafka in HDInsight 3.5.

## <a name="prerequisite"></a>Prerequisito

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per completare correttamente questa esercitazione di Apache Storm, è necessario quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Familiarità con SSH e SCP**. Per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere i documenti seguenti:
  
   * **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
   
   * **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, ad esempio OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Creare un cluster Kafka

Per creare un cluster Kafka in HDInsight seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **+ Nuovo**, **Intelligence e analisi** e quindi selezionare **HDInsight**.
   
    ![Creazione di un cluster HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. Nel pannello **Nuovo cluster HDInsight** immettere un nome in **Nome cluster** e selezionare la **sottoscrizione** con la quale usare il cluster.
   
    ![Selezionare la sottoscrizione](./media/hdinsight-apache-kafka-get-started/new-hdinsight-cluster-blade.png)

3. Usare **Selezionare il tipo di cluster** e selezionare i valori seguenti nel pannello **Configurazione cluster**:
   
    * **Tipo di cluster**: Kafka

    * **Versione**: Kafka 0.10.0 (HDI 3.5)

    * **Livello cluster**: Standard
     
    Usare infine il pulsante **Seleziona** per salvare le impostazioni.
     
    ![Selezionare il tipo di cluster](./media/hdinsight-apache-kafka-get-started/cluster-type.png)

    > [!NOTE]
    > Se la sottoscrizione di Azure non dispone di accesso all'anteprima Kafka, vengono visualizzate le istruzioni su come ottenere l'accesso all'anteprima. Le istruzioni visualizzate sono simili a quelle presentate nell'immagine seguente:
    >
    > ![Messaggio di anteprima: se si desidera distribuire un cluster di Apache Kafka gestito in HDInsight, inviare un messaggio di posta elettronica per richiedere l'accesso all'anteprima](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. Usare **Credenziali** per configurare le credenziali per l'account di accesso del cluster e l'utente SSH.  Usare il pulsante **Seleziona** per salvare le impostazioni.
   
    > [!NOTE]
    > L'account di accesso del cluster viene usato per accedere al cluster tramite Internet con HTTPS. L'utente SSH viene usato per connettersi al cluster ed eseguire comandi in modo interattivo.
   
    ![Configurare l'account di accesso del cluster](./media/hdinsight-apache-kafka-get-started/cluster-credentials.png)
   
    Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:
   
    * [Usare SSH con HDInsight da un client Linux, Unix o MacOS](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

5. Usare **Origine dati** per configurare l'archivio dati primario per il cluster. Dal pannello **Origine dati** usare le informazioni seguenti per creare un archivio dati per il cluster:
   
    * Selezionare **Crea nuovo** e quindi immettere un nome per l'account di archiviazione.
    
    * Selezionare **Località** e scegliere una località geograficamente vicina. Questa località viene usata per creare l'account di archiviazione e il cluster HDInsight.
     
   Usare infine il pulsante **Seleziona** per salvare le impostazioni.
     
    ![Configurare l'archivio](./media/hdinsight-apache-kafka-get-started/configure-storage.png)

6. Usare **Prezzi** e impostare il **numero di nodi del ruolo di lavoro** su 2. L'uso di 2 nodi del ruolo di lavoro consente di ridurre il costo del cluster ed è sufficiente per questo esempio. Usare il pulsante **Seleziona** per salvare le impostazioni.
   
    ![Prezzi](./media/hdinsight-apache-kafka-get-started/pricing.png)
   
    > [!NOTE]
    > I prezzi visualizzati nel portale possono essere diversi da quelli visibili nello screenshot.

7. Usare **Gruppo di risorse** per creare un gruppo e immettere il nome del campo. Selezionare anche **Aggiungi al dashboard**. Al termine fare clic su **Crea** per creare il cluster.
   
    ![Campo Gruppo di risorse](./media/hdinsight-apache-kafka-get-started/resource-group.png)
   
    > [!NOTE]
    > La creazione del cluster può richiedere fino a 20 minuti.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Dal client usare SSH per connettersi al cluster. Se si usa un client Linux, Unix, MacOS o Bash in Windows 10, specificare il comando seguente:

    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net

Sostituire **SSHUSER** con il nome utente SSH specificato durante la creazione del cluster. Sostituire **CLUSTERNAME** con il nome del cluster.

Quando richiesto, immettere la password usata per l'account SSH.

Per informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:

* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Ottenere le informazioni sugli host Zookeeper e broker

Quando si usa Kafka è necessario conoscere due valori di host, ovvero *Zookeeper* e *broker*. Questi host vengono usati con l'API Kafka e molte delle utilità offerte con Kafka.

Seguire questa procedura per creare le variabili di ambiente che contengono le informazioni sugli host. Queste variabili di ambiente vengono usate nei passaggi di questo documento.

1. Da una connessione SSH al cluster usare questo comando per installare l'utilità `jq`. Questa utilità consente di analizzare documenti JSON e risulta utile nel recupero delle informazioni sugli host broker:
   
        sudo apt -y install jq

2. usare i comandi seguenti per impostare le variabili di ambiente con le informazioni recuperate da Ambari. Sostituire __KAFKANAME__ con il nome del cluster Kafka. Sostituire __PASSWORD__ con la password di accesso (amministratore) usata durante la creazione del cluster.

        export KAFKAZKHOSTS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

        export KAFKABROKERS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

        echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
        echo '$KAFKABROKERS='$KAFKABROKERS

    Il testo seguente è un esempio del contenuto di `$KAFKAZKHOSTS`:
   
        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
   
    Il testo seguente è un esempio del contenuto di `$KAFKABROKERS`:
   
        wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092
   
    > [!WARNING]
    > Le informazioni restituite da questa sessione non sono sempre precise. Se si ridimensiona il cluster, verranno aggiunti o rimossi broker. Se si verifica un errore e viene sostituito un nodo, il nome host per il nodo può cambiare. 
    > 
    > È consigliabile recuperare le informazioni sugli host Zookeeper e broker poco prima di usarle, per assicurare che siano valide.

## <a name="create-a-topic"></a>Creare un argomento

Kafka archivia i flussi di dati in categorie denominate *argomenti*. Da una connessione SSH a un nodo head del cluster, usare uno script fornito con Kafka per creare un argomento:

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

Questo comando si connette a Zookeeper usando le informazioni sull'host archiviate in `$KAFKAZKHOSTS` e quindi crea un argomento Kafka denominato **test**. Per verificare che l'argomento sia stato creato è possibile usare lo script seguente per elencare gli argomenti:

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS

L'output di questo comando elenca gli argomenti Kafka, incluso l'argomento **test**.

## <a name="produce-and-consume-records"></a>Produrre e utilizzare record

Kafka archivia i *record* negli argomenti. I record vengono prodotti da *producer* e usati da *consumer*. I producer recuperano i record da *broker* di Kafka. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka.

Seguire questa procedura per archiviare i record nell'argomento test creato in precedenza e quindi leggerli usando un consumer:

1. Dalla sessione SSH, usare uno script fornito con Kafka per scrivere record nell'argomento:
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
   
    Non si tornerà al prompt dopo questo comando. Digitare invece alcuni messaggi di testo e quindi usare **Ctrl + C** per arrestare l'invio all'argomento. Ogni riga viene inviata come record distinto.

2. Usare uno script fornito con Kafka per leggere record dall'argomento:
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
   
    I record verranno recuperati dall'argomento e visualizzati. L'uso di `--from-beginning` indica al consumer di partire dall'inizio del flusso, quindi verranno recuperati tutti i record.

3. Usare __Ctrl + C__ per arrestare il consumer.

## <a name="producer-and-consumer-api"></a>API per producer e consumer

È anche possibile produrre e usare record a livello di codice con le [API Kafka](http://kafka.apache.org/documentation#api). Seguire questa procedura per scaricare e compilare un producer e un consumer basati su Java:

1. Scaricare gli esempi da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Per l'esempio producer/consumer usare il progetto disponibile nella directory `Producer-Consumer`. Esaminare il codice per comprendere il funzionamento di questo esempio. Contiene le classi seguenti:
   
    * **Run**: avvia il consumer o il producer in base agli argomenti della riga di comando.

    * **Producer**: archivia 1.000.000 di record nell'argomento.

    * **Consumer**: legge i record dall'argomento.

2. Dalla riga di comando nell'ambiente di sviluppo, passare alla directory `Producer-Consumer` dell'esempio e quindi usare il comando seguente per creare un pacchetto jar:
   
        mvn clean package
   
    Questo comando crea una nuova directory denominata `target`, che contiene un file denominato `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Usare i comandi seguenti per copiare il file `kafka-producer-consumer-1.0-SNAPSHOT.jar` nel cluster HDInsight:
   
        scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
   
    Sostituire **SSHUSER** con l'utente SSH del cluster e **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'utente SSH.

4. Quando il comando `scp` avrà terminato di copiare il file, connettersi al cluster tramite SSH e quindi usare il comando seguente per scrivere i record nell'argomento test creato in precedenza.
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS
   
    Questo comando avvia il producer e scrive i record. Viene visualizzato un contatore che indica il numero di record scritti.

    > [!NOTE]
    > Se viene visualizzato un errore di autorizzazione negata, usare il comando seguente per rendere il file eseguibile: ```chmod +x kafka-producer-consumer.jar```

5. Quando il processo è terminato, usare il comando seguente per leggere dall'argomento:
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS
   
    Verranno visualizzati i record letti e il numero di record. È possibile che vengano archiviati più di 1.000.000 record perché nel passaggio precedente sono stati inviati diversi record all'argomento tramite uno script.

6. Usare __Ctrl + C__ per uscire dal consumer.

### <a name="multiple-consumers"></a>Consumer multipli

Un concetto importante con Kafka è che i consumer usano un gruppo di consumer definito da un ID gruppo durante la lettura dei record. Più consumer che usano lo stesso gruppo bilanciano il carico delle letture da un argomento. Ogni consumer riceve una parte dei record. Per vedere questa azione seguire questa procedura:

1. Aprire una nuova sessione SSH per il cluster, in modo da averne due. In ogni sessione usare il comando seguente per avviare un consumer con lo stesso ID gruppo:
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup

    > [!NOTE]
    > Dato che si tratta di una nuova sessione SSH, sarà necessario usare i comandi descritti nella sezione [Ottenere le informazioni sugli host Zookeeper e broker](#getkafkainfo) per impostare `$KAFKABROKERS`.

2. Si noti che ogni sessione indica il conteggio dei record ricevuti dall'argomento. Il totale di entrambe le sessioni deve corrispondere a quello ricevuto in precedenza da un solo consumer.

Il consumo da parte dei client dello stesso gruppo viene gestito tramite le partizioni dell'argomento. Per l'argomento `test` creato in precedenza sono presenti 8 partizioni. Se si aprono 8 sessioni SSH e si avvia un consumer in tutte le sessioni, ogni consumer leggerà i record da una singola partizione dell'argomento.

> [!IMPORTANT]
> Un gruppo di consumer non può contenere un numero di istanze di consumer maggiore del numero di partizioni. In questo esempio, un gruppo di consumer può contenere fino a 8 consumer perché è questo il numero di partizioni nell'argomento. In alternativa è possibile avere più gruppi di consumer, ognuno con al massimo 8 consumer.

I record vengono archiviati in Kafka nell'ordine in cui vengono ricevuti in una partizione. Per ottenere il recapito dei record nell'ordine *all'interno di una partizione*, creare un gruppo di consumer con un numero di istanze corrispondente al numero di partizioni. Per ottenere il recapito dei record nell'ordine *all'interno dell'argomento*, creare un gruppo di consumer con una sola istanza.

## <a name="streaming-api"></a>API di streaming

L'API di streaming è stata aggiunta a Kafka nella versione 0.10.0; le versioni precedenti si basano su Apache Spark o Storm per l'elaborazione dei flussi.

1. Se non è già stato fatto, scaricare gli esempi da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Per l'esempio di streaming usare il progetto disponibile nella directory `streaming`. Esaminare il codice per comprendere il funzionamento di questo esempio. 
   
    Questo progetto contiene una sola classe, `Stream`, che legge i record dall'argomento `test` creato in precedenza. Conta le parole lette ed emette ogni parola e il conteggio in un argomento denominato `wordcounts`. L'argomento `wordcounts` viene creato in un passaggio successivo di questa sezione.

2. Dalla riga di comando nell'ambiente di sviluppo, passare alla directory `Streaming` e quindi usare il comando seguente per creare un pacchetto jar:
   
        mvn clean package
   
    Questo comando crea una nuova directory denominata `target`, che contiene un file denominato `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Usare i comandi seguenti per copiare il file `kafka-streaming-1.0-SNAPSHOT.jar` nel cluster HDInsight:
   
        scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
   
    Sostituire **SSHUSER** con l'utente SSH del cluster e **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'utente SSH.

4. Quando il comando `scp` avrà terminato di copiare il file, connettersi al cluster tramite SSH e quindi usare il comando seguente per avviare il processo di streaming:
   
        ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
   
    Questo comando avvia il processo di streaming in background.

5. Usare il comando seguente per inviare messaggi all'argomento `test`. I messaggi verranno elaborati dall'esempio di streaming:
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &

6. Usare il comando seguente per visualizzare l'output scritto nell'argomento `wordcounts`:
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
   
    > [!NOTE]
    > È necessario indicare al consumer di stampare la chiave, che contiene il valore delle parole, e il deserializzatore da usare per la chiave e il valore per visualizzare i dati.
   
    L'output è simile al seguente:
   
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
        a       13805
        snow    13637
   
    Si noti che il conteggio viene incrementato ogni volta che viene rilevata una parola.

7. Usare __Ctrl + C__ per uscire dal consumer, quindi usare il comando `fg` per riportare in primo piano l'attività di streaming in background. Usare __Ctrl + C__ anche per uscire dall'attività.

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono state illustrate le nozioni di base per l'uso di Apache Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Documentazione di Apache Kafka](http://kafka.apache.org/documentation.html) in kafka.apache.org.
* [Usare MirrorMaker per creare una replica di Kafka in HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)




<!--HONumber=Jan17_HO2-->


