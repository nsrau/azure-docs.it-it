---
title: Risolvere i problemi di Storm usando Azure HDInsight
description: Risposte alle domande frequenti sull'uso di Apache Storm con Azure HDInsight.
keywords: Azure HDInsight, Storm, domande frequenti, guida alla risoluzione dei problemi, problemi comuni
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: e2cc9dd81a0bbefa5cf37facb6067bda07117eaf
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903730"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Risolvere i problemi di Apache Storm tramite Azure HDInsight

Informazioni sui problemi principali che possono verificarsi quando si usano i payload di [Apache Storm](https://storm.apache.org/) in [Apache Ambari](https://ambari.apache.org/) unitamente alle risoluzioni.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Come accedere all'interfaccia utente di Storm in un cluster

Per accedere all'interfaccia utente di Storm da un browser, è possibile procedere in due modi:

### <a name="apache-ambari-ui"></a>Interfaccia utente di Apache Ambari

1. Andare al dashboard di Ambari.
2. Nell'elenco dei servizi selezionare **Storm**.
3. Scegliere **Storm UI** (Interfaccia utente di Storm) dal menu **Quick Links** (Collegamenti rapidi).

### <a name="direct-link"></a>Collegamento diretto

È possibile accedere all'interfaccia utente di Storm all'URL seguente:

`https://<cluster DNS name>/stormui`

Esempio: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Come trasferire informazioni di checkpoint dello spout dell'hub eventi di Storm da una topologia all'altra

Quando si sviluppano topologie che leggono da Hub eventi di Azure usando il file JAR dello spout dell'hub eventi di HDInsight Storm, è necessario distribuire una topologia con lo stesso nome in un nuovo cluster. È tuttavia necessario conservare i dati dei checkpoint di cui è stato eseguito il commit ad [Apache ZooKeeper](https://zookeeper.apache.org/) sul vecchio cluster.

### <a name="where-checkpoint-data-is-stored"></a>Dove vengono archiviati i dati dei checkpoint

I dati dei checkpoint per gli offset vengono archiviati dallo spout dell'hub eventi in due percorsi radice di ZooKeeper:

- I checkpoint del beccuccio non transazionali vengono archiviati in `/eventhubspout`.

- I dati del checkpoint del punto di arresto transazionale vengono archiviati in `/transactional`.

### <a name="how-to-restore"></a>Come eseguire il ripristino

Per ottenere gli script e le librerie usati per esportare i dati da ZooKeeper e quindi importarli nuovamente in ZooKeeper con un nuovo nome, vedere gli [esempi di HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

La cartella lib ha file JAR che contengono l'implementazione per l'operazione di esportazione/importazione. La cartella bash contiene uno script di esempio che mostra come esportare i dati dal server ZooKeeper sul vecchio cluster e quindi reimportarli nel nuovo cluster del server ZooKeeper.

Eseguire lo script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) dai nodi ZooKeeper per esportare e quindi importare i dati. Aggiornare lo script alla versione corretta di Hortonworks Data Platform (HDP). A breve questi script saranno generici in HDInsight. Gli script generici possono essere eseguiti da qualsiasi nodo del cluster senza modifiche da parte dell'utente.

Il comando di esportazione scrive i metadati in un percorso Apache Hadoop Distributed File System (HDFS) (in un archivio BLOB di Azure o in un archivio di Azure Data Lake Storage) nella posizione impostata.

### <a name="examples"></a>esempi

#### <a name="export-offset-metadata"></a>Esportare i metadati dell'offset

1. Usare SSH per andare al cluster ZooKeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
2. Eseguire il comando seguente (dopo aver aggiornato la stringa della versione HDP) per esportare i dati dell'offset ZooKeeper nel percorso di `/stormmetadta/zkdata` HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importare i metadati dell'offset

1. Usare SSH per andare al cluster ZooKeeper del vecchio cluster da cui è necessario importare l'offset dei checkpoint.
2. Eseguire il comando seguente (dopo aver aggiornato la stringa della versione HDP) per importare i dati di offset ZooKeeper dal percorso HDFS `/stormmetadata/zkdata` al server ZooKeeper nel cluster di destinazione:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Eliminare i metadati dell'offset per permettere alle topologie di avviare l'elaborazione dei dati dall'inizio o da un timestamp scelto dall'utente

1. Usare SSH per andare al cluster ZooKeeper del vecchio cluster da cui è necessario eliminare l'offset dei checkpoint.
2. Usare il comando seguente (dopo avere aggiornato la stringa della versione HDP) per eliminare tutti i dati dell'offset ZooKeeper nel cluster corrente:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Come individuare i file binari Storm in un cluster

I file binari Storm per lo stack HDP corrente sono in `/usr/hdp/current/storm-client`. Il percorso è lo stesso sia per i nodi head che per i nodi di lavoro.

Potrebbero essere presenti più file binari per versioni specifiche di HDP in/usr/HDP, ad esempio `/usr/hdp/2.5.0.1233/storm`. La cartella `/usr/hdp/current/storm-client` è collegati simbolicamente alla versione più recente in esecuzione nel cluster.

Per altre informazioni, vedere [Connettersi a un cluster HDInsight con SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Come determinare la topologia di distribuzione di un cluster Storm

Identificare prima tutti i componenti installati in HDInsight Storm. Un cluster Storm è costituito da quattro categorie di nodi:

* Nodi gateway
* Nodi head
* Nodi ZooKeeper
* Nodi di lavoro

### <a name="gateway-nodes"></a>Nodi gateway

Un nodo del gateway è un servizio gateway e proxy inverso che consente il pubblico accesso a un servizio di gestione Ambari attivo. Gestisce anche l'algoritmo di elezione di Ambari.

### <a name="head-nodes"></a>Nodi head

I nodi head di Storm eseguono i seguenti servizi:
* Nimbus
* Server Ambari
* Server delle metriche Ambari
* Agente di raccolta delle metriche Ambari
 
### <a name="zookeeper-nodes"></a>Nodi ZooKeeper

HDInsight viene offerto con un quorum ZooKeeper di tre nodi. La dimensione del quorum è fissa e non può essere riconfigurata.

I servizi Storm nel cluster sono configurati per usare automaticamente il quorum ZooKeeper.

### <a name="worker-nodes"></a>Nodi di lavoro

I nodi di lavoro di Storm eseguono i seguenti servizi:
* Supervisore
* Java Virtual Machine (JVM) di lavoro, per le topologie in esecuzione
* Agente Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Come individuare i file binari dello spout dell'hub eventi di Storm per lo sviluppo

Per altre informazioni sull'uso dei file JAR dello spout dell'hub eventi di Storm con la topologia, vedere le risorse seguenti.

### <a name="java-based-topology"></a>Topologia basata su Java

[Elaborare eventi di Hub eventi di Azure con Apache Storm in HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia basata su C# (Mono in cluster HDInsight 3.4+ Linux Storm)

[Elaborare eventi di Hub eventi di Azure con Apache Storm in HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>File binari dello spout dell'hub eventi di Apache Storm più recenti per cluster HDInsight 3.5+ Linux Storm

Per informazioni su come usare il beccuccio Hub eventi di Storm più recente che funziona con i cluster HDInsight 3.5 + Linux Storm, vedere il [file Leggimi MVN-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Esempi di codice sorgente

Vedere gli [esempi](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) su come leggere e scrivere da Hub eventi di Azure usando una topologia di Apache Storm (scritta in Java) in un cluster Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Come individuare i file di configurazione di Storm Log4J 2 nei cluster

Per identificare i file di configurazione di [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) per i servizi Storm.

### <a name="on-head-nodes"></a>Nei nodi head

La configurazione di Nimbus Log4J viene letta da `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>Nei nodi di lavoro

La configurazione del supervisore Log4J viene letta da `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

Il file di configurazione Log4J di lavoro viene letto da `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Esempi: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Non è un'eccezione leader

Quando si invia una topologia, l'utente può ricevere un messaggio di errore simile a: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Per risolvere il problema, è possibile che l'utente debba presentare un ticket per riavviarlo o riavviarlo. Per altre informazioni, vedere [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

- Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

- Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la fatturazione e la gestione della sottoscrizione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
