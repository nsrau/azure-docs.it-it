---
title: Risolvere i problemi di Storm usando Azure HDInsight | Microsoft Docs
description: Risposte alle domande frequenti sull'uso di Apache Storm con Azure HDInsight.
keywords: Azure HDInsight, Storm, domande frequenti, guida alla risoluzione dei problemi, problemi comuni
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.openlocfilehash: 70a3d762431d90acdd6ed2a432a569f34d0ce447
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Risolvere i problemi di Storm usando Azure HDInsight

Informazioni sui problemi principali che possono verificarsi quando si usano i payload di Apache Storm in Apache Ambari unitamente alle risoluzioni.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Come accedere all'interfaccia utente di Storm in un cluster
Per accedere all'interfaccia utente di Storm da un browser, è possibile procedere in due modi:

### <a name="ambari-ui"></a>Tramite l'interfaccia utente di Ambari
1. Andare al dashboard di Ambari.
2. Nell'elenco dei servizi selezionare **Storm**.
3. Scegliere **Storm UI** (Interfaccia utente di Storm) dal menu **Quick Links** (Collegamenti rapidi).

### <a name="direct-link"></a>Collegamento diretto
È possibile accedere all'interfaccia utente di Storm all'URL seguente:

https://\<nome DNS del cluster\>/stormui

Esempio:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Come trasferire informazioni di checkpoint dello spout dell'hub eventi di Storm da una topologia all'altra

Quando si sviluppano topologie che leggono da Hub eventi di Azure usando il file JAR dello spout dell'hub eventi di HDInsight Storm, è necessario distribuire una topologia con lo stesso nome in un nuovo cluster. È tuttavia necessario conservare i dati dei checkpoint di cui è stato eseguito il commit ad Apache ZooKeeper sul vecchio cluster.

### <a name="where-checkpoint-data-is-stored"></a>Dove vengono archiviati i dati dei checkpoint
I dati dei checkpoint per gli offset vengono archiviati dallo spout dell'hub eventi in due percorsi radice di ZooKeeper:
- I checkpoint dello spout non transazionali vengono archiviati in /eventhubspout.
- I dati dei checkpoint dello spout transazionali vengono archiviati in /transactional.

### <a name="how-to-restore"></a>Come eseguire il ripristino
Per ottenere gli script e le librerie usati per esportare i dati da ZooKeeper e quindi importarli nuovamente in ZooKeeper con un nuovo nome, vedere gli [esempi di HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

La cartella lib ha file JAR che contengono l'implementazione per l'operazione di esportazione/importazione. La cartella bash contiene uno script di esempio che mostra come esportare i dati dal server ZooKeeper sul vecchio cluster e quindi reimportarli nel nuovo cluster del server ZooKeeper.

Eseguire lo script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) dai nodi ZooKeeper per esportare e quindi importare i dati. Aggiornare lo script alla versione corretta di Hortonworks Data Platform (HDP). A breve questi script saranno generici in HDInsight. Gli script generici possono essere eseguiti da qualsiasi nodo del cluster senza modifiche da parte dell'utente.

Il comando di esportazione scrive i metadati in un percorso Apache Hadoop Distributed File System (HDFS) (in un archivio BLOB di Azure o in un archivio di Azure Data Lake Store) nella posizione impostata.

### <a name="examples"></a>esempi

#### <a name="export-offset-metadata"></a>Esportare i metadati dell'offset
1. Usare SSH per andare al cluster ZooKeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
2. Usare il comando seguente (dopo avere aggiornato la stringa della versione HDP) per esportare i dati dell'offset ZooKeeper nel percorso HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importare i metadati dell'offset
1. Usare SSH per andare al cluster ZooKeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
2. Usare il comando seguente (dopo avere aggiornato la stringa della versione HDP) per importare i dati dell'offset ZooKeeper dal percorso HDFS /stormmetadta/zkdata al cluster di destinazione del server ZooKeeper:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Eliminare i metadati dell'offset per permettere alle topologie di avviare l'elaborazione dei dati dall'inizio o da un timestamp scelto dall'utente
1. Usare SSH per andare al cluster ZooKeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
2. Usare il comando seguente (dopo avere aggiornato la stringa della versione HDP) per eliminare tutti i dati dell'offset ZooKeeper nel cluster corrente:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Individuare i binari Storm in un cluster
La posizione dei file binari Storm per lo stack HDP corrente è /usr/hdp/current/storm-client. Il percorso è lo stesso sia per i nodi head che per i nodi di lavoro.
 
Potrebbero essere presenti più file binari per versioni HDP specifiche in /usr/hdp (ad esempio, /usr/hdp/2.5.0.1233/storm). La cartella /usr/hdp/current/storm-client è collegata alla versione più recente in esecuzione nel cluster.

Per altre informazioni, vedere [Connettersi a un cluster HDInsight con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Procedura per determinare la topologia di distribuzione di un cluster Storm
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
[Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia basata su C# (Mono in cluster HDInsight 3.4+ Linux Storm)
[Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>File binari dello spout dell'hub eventi di Storm più recenti per cluster HDInsight 3.5+ Linux Storm
Per informazioni su come usare lo spout dell'hub eventi di Storm più recente che utilizza i cluster HDInsight 3.5+ Linux Storm, vedere il [mvn-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Esempi di codice sorgente
Vedere gli [esempi](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) su come leggere e scrivere da Hub eventi di Azure usando una topologia di Apache Storm (scritta in Java) in un cluster Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Individuare i file di configurazione Storm Log4J nei cluster
 
Per identificare i file di configurazione di Apache Log4J per i servizi Storm.
 
### <a name="on-head-nodes"></a>Nei nodi head
La configurazione Log4J di Nimbus viene letta da /usr/hdp/\<versione HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Nei nodi di lavoro
La configurazione Log4J del supervisore viene letta da /usr/hdp/\<versione HDP\>/storm/log4j2/cluster.xml.
 
Il file di configurazione Log4J di lavoro viene letto da /usr/hdp/\<versione HDP\>/storm/log4j2/worker.xml.
 
Esempi: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

