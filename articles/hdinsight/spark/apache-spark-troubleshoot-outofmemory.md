---
title: Eccezioni OutOfMemoryError per Apache Spark in Azure HDInsight
description: Varie eccezioni OutOfMemoryError per Apache Spark in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/15/2019
ms.openlocfilehash: f6ff654b8e51dfaf2697df69c7f220d41346c2bc
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543471"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Eccezioni OutOfMemoryError per Apache Spark in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenario: Eccezione OutOfMemoryError per Apache Spark

### <a name="issue"></a>Problema

L'applicazione Apache Spark non è riuscita con un'eccezione non gestita OutOfMemoryError. È possibile che venga visualizzato un messaggio di errore simile al seguente:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Causa

La causa più probabile per questa eccezione è costituita dall'allocazione di memoria heap insufficiente alle macchine virtuali Java (JVM). Questi JVM vengono avviati come Executor o driver come parte dell'applicazione Apache Spark.

### <a name="resolution"></a>Risoluzione

1. Determinare le dimensioni massime dei dati che possono essere gestiti dall'applicazione Spark. Eseguire una stima delle dimensioni in base al valore massimo della dimensione dei dati di input, i dati intermedi prodotti dalla trasformazione dei dati di input e dei dati di output prodotte ulteriormente la trasformazione dei dati intermedi. Se la stima iniziale non è sufficiente, aumentare leggermente le dimensioni e scorrere fino a quando non si verificano errori di memoria.

1. Assicurarsi che il cluster HDInsight da utilizzare disponga di sufficienti risorse in termini di memoria e core per supportare l'applicazione Spark. Questo può essere determinato visualizzando la sezione relativa alle metriche del cluster dell'interfaccia utente di YARN del cluster per i valori della **memoria usata** rispetto a. **Totale memoria** e **Vcore usati** rispetto a **VCores Total** (VCore totali).

    ![visualizzazione memoria core yarn](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Impostare le configurazioni di Spark seguenti sui valori appropriati. Bilanciare i requisiti dell'applicazione con le risorse disponibili nel cluster. Questi valori non devono superare il 90% della memoria e dei core disponibili visualizzati da YARN e devono soddisfare anche i requisiti minimi di memoria dell'applicazione Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Memoria totale usata da tutti gli esecutori =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Memoria totale usata dal driver =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenario: Errore di spazio heap Java durante il tentativo di aprire Apache Spark server di cronologia

### <a name="issue"></a>Problema

Viene visualizzato l'errore seguente durante l'apertura di eventi nel server della cronologia Spark:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Causa

Questo problema è spesso dovuto a una mancanza di risorse durante l'apertura di grandi file di eventi Spark. Per impostazione predefinita, le dimensioni dell'heap Spark sono impostate su 1 GB, ma i file di eventi Spark di grandi dimensioni potrebbero richiedere più di questo.

Se si desidera verificare le dimensioni dei file che si sta tentando di caricare, è possibile eseguire i comandi seguenti:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Risoluzione

È possibile aumentare la memoria del server della cronologia di Spark `SPARK_DAEMON_MEMORY` modificando la proprietà nella configurazione di Spark e riavviando tutti i servizi.

È possibile eseguire questa operazione dall'interfaccia utente del browser Ambari selezionando la sezione Spark2/config/Advanced Spark2-ENV.

![Sezione Advanced spark2-ENV](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Aggiungere la proprietà seguente per modificare la memoria del server della cronologia di Spark da 1g `SPARK_DAEMON_MEMORY=4g`a 4G:.

![Proprietà Spark](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Assicurarsi di riavviare tutti i servizi interessati da Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenario: Non è possibile avviare il server Livio nel cluster Apache Spark

### <a name="issue"></a>Problema

Non è possibile avviare il server Livio in un Apache Spark [(Spark 2,1 in Linux (HDI 3,6)]. Il tentativo di riavviare i risultati nel seguente stack di errori, dai log di Livio:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Causa

`java.lang.OutOfMemoryError: unable to create new native thread`Highlights OS non può assegnare più thread nativi a JVM. Ha confermato che questa eccezione è causata dalla violazione del limite del numero di thread per processo.

Quando il server Livio si interrompe in modo imprevisto, vengono interrotte anche tutte le connessioni ai cluster Spark, il che significa che tutti i processi e i dati correlati andranno perduti. In HDP 2,6 è stato introdotto il meccanismo di ripristino della sessione, Livio archivia i dettagli della sessione in Zookeeper da ripristinare dopo che il server Livio è stato ripristinato.

Quando un numero elevato di processi viene inviato tramite Titone, come parte della disponibilità elevata per il server Livio archivia questi stati di sessione in ZK (nei cluster HDInsight) e recupera tali sessioni quando il servizio Livio viene riavviato. Al riavvio dopo la terminazione imprevista, Livio crea un thread per sessione e questo accumula un certo numero di sessioni da ripristinare, causando la creazione di troppi thread.

### <a name="resolution"></a>Risoluzione

Eliminare tutte le voci usando i passaggi descritti di seguito.

1. Ottenere l'indirizzo IP dei nodi Zookeeper usando

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Il comando precedente elencava tutti i Zookeeper per il cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Ottenere tutti gli indirizzi IP dei nodi Zookeeper usando il comando ping oppure è possibile connettersi a Zookeeper da nodo head con il nome ZK

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Una volta stabilita la connessione a Zookeeper, eseguire il comando seguente per elencare tutte le sessioni di cui si è tentato il riavvio.

    1. La maggior parte dei casi può essere un elenco di più di 8000 sessioni ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Il comando seguente consente di rimuovere tutte le sessioni da ripristinare. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Attendere il completamento del comando precedente e il cursore per restituire la richiesta e quindi riavviare il servizio Livio da Ambari, che dovrebbe avere esito positivo.

> [!NOTE]
> `DELETE`la sessione di Livio dopo aver completato l'esecuzione. Le sessioni batch di Livio non verranno eliminate automaticamente non appena l'app Spark viene completata, ovvero in base alla progettazione. Una sessione di Livio è un'entità creata da una richiesta POST sul server REST di Livio. È `DELETE` necessaria una chiamata per eliminare l'entità. In alternativa, è necessario attendere l'avvio del GC.

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* [Panoramica sulla gestione della memoria di Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debug di un'applicazione Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
