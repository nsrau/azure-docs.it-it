---
title: Non è possibile avviare il server Livio nel cluster Apache Spark in Azure HDInsight
description: Non è possibile avviare il server Livio nel cluster Apache Spark in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 540b5d2a20764daf2a7f762ac7381f72ce4aabcd
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668911"
---
# <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster-in-azure-hdinsight"></a>Scenario: Non è possibile avviare il server Livio nel cluster Apache Spark in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

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

## <a name="cause"></a>Causa

`java.lang.OutOfMemoryError: unable to create new native thread`Highlights OS non può assegnare più thread nativi a JVM. Ha confermato che questa eccezione è causata dalla violazione del limite del numero di thread per processo.

Quando il server Livio si interrompe in modo imprevisto, vengono interrotte anche tutte le connessioni ai cluster Spark, il che significa che tutti i processi e i dati correlati andranno perduti. In HDP 2,6 è stato introdotto il meccanismo di ripristino della sessione, Livio archivia i dettagli della sessione in Zookeeper da ripristinare dopo che il server Livio è stato ripristinato.

Quando un numero elevato di processi viene inviato tramite Titone, come parte della disponibilità elevata per il server Livio archivia questi stati di sessione in ZK (nei cluster HDInsight) e recupera tali sessioni quando il servizio Livio viene riavviato. Al riavvio dopo la terminazione imprevista, Livio crea un thread per sessione e questo accumula un certo numero di sessioni da ripristinare, causando la creazione di troppi thread.

## <a name="resolution"></a>Risoluzione

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

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
