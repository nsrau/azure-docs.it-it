---
title: Risoluzione dei problemi di STORM - Azure HDInsight | Microsoft Docs
description: Usare le domande frequenti su Storm per trovare le risposte alle domande comuni sull'uso di Storm sulla piattaforma Azure HDInsight.
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="storm-troubleshooting"></a>Risoluzione dei problemi di STORM

Questo articolo descrive i problemi principali che possono verificarsi quando si usano i payload di Storm in Apache Ambari unitamente alle risoluzioni.

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>Procedura per accedere all'interfaccia utente di Storm in un cluster

### <a name="issue"></a>Problema:
È possibile accedere all'interfaccia utente di Storm dal browser in due modi:

#### <a name="ambari-ui"></a>Tramite l'interfaccia utente di Ambari
1. Passare al dashboard di Ambari
1. Selezionare Storm dall'elenco dei servizi a sinistra
1. Selezionare l'opzione Storm UI (Interfaccia utente Storm) dal menu a discesa Collegamenti rapidi

#### <a name="direct-link"></a>Collegamento diretto
L'interfaccia utente di Storm è accessibile dall'URL:

https://\<ClusterDnsName\>/stormui

esempio: https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>Procedura per trasferire le informazioni sui checkpoint spout eventhub da una topologia a un'altra

### <a name="issue"></a>Problema:
Durante lo sviluppo di topologie che leggono dagli hub eventi usando un jar spout eventhub di Storm HDInsight, è possibile distribuire una topologia con lo stesso nome in un nuovo cluster, mantenendo i dati di checkpoint vincolati a Zookeeper nel vecchio cluster?

#### <a name="where-is-checkpoint-data-stored"></a>Dove vengono archiviati i dati di checkpoint
I dati di checkpoint per gli offset vengono archiviati dallo spout EventHub in due percorsi principali di Zookeeper:
- I checkpoint dello spout non transazionali vengono archiviati in: /eventhubspout
- I dati dei checkpoint dello spout transazionali vengono archiviati in: /transactional

#### <a name="how-to-restore"></a>Modalità di ripristino
Gli script e le librerie da usare per esportare i dati da Zookeeper e reimportarli con un nuovo nome sono disponibili all'indirizzo: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0

La cartella lib ha file Jar che contengono l'implementazione per l'operazione di importazione/esportazione.
La cartella bash contiene uno script che mostra come esportare i dati dal server Zookeeper in un cluster vecchio e reimportarli nel nuovo cluster del server Zookeeper.

È necessario eseguire lo script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) dai nodi Zookeeper per importare/esportare i dati.
È necessario aggiornare lo script per correggere la stringa della versione HDP al suo interno.
(HDInsight sta cercando di rendere questi script generici per consentire la loro esecuzione da qualsiasi nodo nel cluster ed evitare che debbano essere modificati dall'utente finale)

Il comando di esportazione scrive i metadati in un percorso HDFS (archivio BLOB o ADSL) nella posizione specificata.

### <a name="examples"></a>esempi

##### <a name="export-offset-metadata"></a>Esportazione dei metadati dell'offset:
1. SSH nel cluster Zookeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
1. Eseguire il seguente comando (dopo aver aggiornato la stringa della versione HDP) per esportare i dati dell'offset Zookeeper nel percorso HDFS /stormmetadta/zkdata.

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>Importare i metadati dell'offset
1. SSH nel cluster Zookeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
1. Eseguire il seguente comando (dopo aver aggiornato la stringa della versione HDP) per importare i dati dell'offset Zookeeper dal percorso HDFS /stormmetadata/zkdata nel cluster di destinazione del server Zookeeper).

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>Eliminare i metadati dell'offset per permettere alle topologie di avviare l'elaborazione dei dati (dall'inizio o dal timestamp scelto dall'utente)
1. SSH nel cluster Zookeeper del vecchio cluster da cui è necessario esportare l'offset dei checkpoint.
1. Eseguire il seguente comando (dopo aver aggiornato la stringa della versione HDP) per eliminare tutti i dati dell'offset Zookeeper per il cluster corrente.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Individuare i binari Storm in un cluster

### <a name="issue"></a>Problema:
 Conoscere la posizione dei binari per i servizi Storm nel cluster HDInsight

### <a name="resolution-steps"></a>Procedura per la risoluzione:

La posizione dei binari Storm per lo stack HDP corrente è: /usr/hdp/current/storm-client

Questa posizione è la stessa per i nodi head e dei ruoli di lavoro.
 
È possibile che ci siano più binari specifici della versione HDP in /usr/hdp (esempi: /usr/hdp/2.5.0.1233/storm)

Tuttavia, /usr/hdp/current/storm-client è collegato all'ultima versione eseguita nel cluster.

### <a name="further-reading"></a>Altre informazioni:
 [Connettersi al cluster HDInsight Cluster usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Procedura per determinare la topologia di distribuzione di un cluster Storm
 
### <a name="issue"></a>Problema:
 
Identificare tutti i componenti installati in HDInsight Storm.
 
Il cluster Storm comprende 4 categorie di nodi
1. Gateway
1. Nodi head
1. Nodi Zookeeper
1. Nodi di lavoro
 
#### <a name="gateway-nodes"></a>Nodi gateway
È un servizio gateway e proxy inverso che consente il pubblico accesso al servizio di gestione Ambari attivo e gestisce l'elezione di Ambari Leader.
 
#### <a name="zookeeper-nodes"></a>Nodi Zookeeper
HDInsight viene offerto con un quorum Zookeeper di 3 nodi.
La dimensione del quorum è fissa e non può essere configurata.
 
I servizi Storm nel cluster sono configurati per usare automaticamente il quorum ZK.
 
#### <a name="head-nodes"></a>Nodi head
I nodi head di Storm eseguono i seguenti servizi:
1. Nimbus
1. Server Ambari
1. Server delle metriche Ambari
1. Agente di raccolta delle metriche Ambari
 
#### <a name="worker-nodes"></a>Nodi di lavoro
 I nodi di lavoro di Storm eseguono i seguenti servizi:
1. Supervisore
1. JVM di lavoro per l'esecuzione di topologie
1. Agente Ambari
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>Procedura per individuare i binari Storm-EventHub-Spout per lo sviluppo
 
### <a name="issue"></a>Problema:
Dove trovare altre informazioni sull'uso dei jar spout eventhub di Storm per la topologia in uso.
 
#### <a name="msdn-articles-on-how-to"></a>Articoli MSDN sulle procedure
 
##### <a name="java-based-topology"></a>Topologia basata su Java
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>Topologia basata su C# (quando si usa Mono in cluster HDI 3.4+ Linux Storm)
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>Binari spout EventHub Storm più recenti per cluster HDI3.5+Linux Storm
Rivedere https://github.com/hdinsight/mvn-repo/blob/master/README.md per informazioni su come usare lo spout eventhub Storm più recente che funziona con i cluster HDI3.5+ Linux Storm.
 
##### <a name="source-code-examples"></a>Esempi di codice sorgente:
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Individuare i file di configurazione Storm Log4J nei cluster
 
### <a name="issue"></a>Problema:
 
Identificare i file di configurazione Log4J per i servizi Storm.
 
#### <a name="on-headnodes"></a>Nei nodi head:
La configurazione Log4J di Nimbus viene letta da: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
#### <a name="worker-nodes"></a>Nodi di lavoro
La configurazione Log4J di Supervisore viene letta da: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
Il file di configurazione Log4J di lavoro viene letto da: /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml
 
Esempio: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







