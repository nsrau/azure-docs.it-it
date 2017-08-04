---
title: Risoluzione dei problemi di HIVE - Azure HDInsight | Microsoft Docs
description: Usare le domande frequenti su Hive per ottenere le risposte alle domande comuni su Hive nella piattaforma Azure HDInsight.
keywords: Azure HDInsight, Hive, domande frequenti, guida alla risoluzione dei problemi, domande comuni
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="hive-troubleshooting"></a>Risoluzione dei problemi di HIVE

Questo articolo descrive i problemi principali e le relative soluzioni per l'uso dei payload HIVE in Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Come esportare un metastore Hive e importarlo in un altro cluster

### <a name="issue"></a>Problema:

Occorre esportare un metastore Hive e importarlo in un altro cluster HDInsight.  

### <a name="resolution-steps"></a>Procedura per la risoluzione: 

1. Connettersi al cluster HDInsight con un client Secure Shell (SSH). Vedere più avanti la sezione Altre informazioni.
1. Eseguire il comando seguente nel cluster HDInsight da cui si vuole esportare il metastore:

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

Verrà generato un file denominato `allatables.sql`.

- Copiare il file `alltables.sql` nel nuovo cluster HDInsight ed eseguire il comando seguente:

```apache
hive -f alltables.sql
```

Questo codice presuppone che i percorsi dei dati nel nuovo cluster siano uguali a quelli presenti nel vecchio cluster. In caso contrario, è possibile modificare manualmente il file  
`alltables.sql` generato in modo da riflettere le eventuali modifiche.

### <a name="further-reading"></a>Altre informazioni:

- [Connettersi al cluster HDInsight con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>Come individuare i log di Hive in un cluster

### <a name="issue"></a>Problema:

Occorre individuare i log del client, del metastore e del server Hive nel cluster HDInsight.  

### <a name="resolution-steps"></a>Procedura per la risoluzione: 

- Connettersi al cluster HDInsight con un client Secure Shell (SSH). Vedere più avanti la sezione Altre informazioni.
- I log del client Hive sono disponibili in:

```apache
/tmp/<username>/hive.log 
```

- I log del metastore Hive sono disponibili in:

```apache
/var/log/hive/hivemetastore.log 
```

- I log del server Hive sono disponibili in:

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>Altre informazioni:

- [Connettersi al cluster HDInsight con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>Come avviare la shell di Hive con configurazioni specifiche in un cluster

### <a name="issue"></a>Problema:

Occorre eseguire l'override o specificare le configurazioni della shell di Hive in fase di avvio nei cluster HDInsight.  

### <a name="resolution-steps"></a>Procedura per la risoluzione: 

- Specificare la coppia chiave-valore di una configurazione durante l'avvio della shell di Hive. Vedere più avanti la sezione Altre informazioni:

```apache
hive -hiveconf a=b 
```

- Elencare tutte le configurazioni valide nella shell di Hive con il comando seguente:

```apache
hive> set;
```

Ad esempio, usare il comando seguente per avviare la shell di Hive con la registrazione debug abilitata nella console:
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>Altre informazioni:

- [Hive configuration properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties) (Proprietà di configurazione di Hive)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Come analizzare i dati DAG di Tez nel percorso critico di un cluster

### <a name="issue"></a>Problema:

Occorre analizzare le informazioni del grafo aciclico diretto (Directed Acyclic Graph, DAG) di Tez, in particolare il percorso critico nel cluster HDInsight

### <a name="resolution-steps"></a>Procedura per la risoluzione:
 
- Connettersi al cluster HDInsight con un client Secure Shell (SSH). Vedere più avanti la sezione Altre informazioni.

- Al prompt dei comandi eseguire il comando seguente:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- Elencare altri analizzatori utili per l'analisi dei dati DAG di Tez con il comando seguente:

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

È necessario fornire un programma di esempio come primo argomento.

I nomi di programma validi sono: ContainerReuseAnalyzer: stampa i dettagli di riuso del contenitore in un DAG CriticalPath: trova il percorso critico di un DAG LocalityAnalyzer: stampa i dettagli della località in un DAG ShuffleTimeAnalyzer: analizza i dettagli della fase di shuffle in un DAG SkewAnalyzer: analizza i dettagli di asimmetria in un DAG SlowNodeAnalyzer: stampa i dettagli del nodo in un DAG SlowTaskIdentifier: stampa i dettagli di un'attività lenta in un DAG SlowestVertexAnalyzer: stampa i dettagli del vertice più lento in un DAG SpillAnalyzer: stampa i dettagli di spill in un DAG TaskConcurrencyAnalyzer: stampa i dettagli di concorrenza attività in un DAG VertexLevelCriticalPathAnalyzer: trova il percorso critico a livello di vertice in un DAG


### <a name="further-reading"></a>Altre informazioni:

- [Connettersi al cluster HDInsight con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Come scaricare i dati DAG di Tez da un cluster

#### <a name="issue"></a>Problema:

Occorre scaricare le informazioni del grafo aciclico diretto (Directed Acyclic Graph, DAG) di Tez dal cluster HDInsight.

#### <a name="resolution-steps"></a>Procedura per la risoluzione:

Esistono due modi per raccogliere i dati DAG di Tez.

- Dalla riga di comando:
 
    Connettersi al cluster HDInsight con un client Secure Shell (SSH). Al prompt dei comandi eseguire il comando seguente:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- Oppure è possibile usare la visualizzazione Tez di Ambari:
   
Passare ad Ambari --> Passare alla visualizzazione Tez (nascosta sotto l'icona dei riquadri nell'angolo superiore destro) --> Fare clic sul DAG a cui si è interessati--> Fare clic su Download data (Scarica dati).

#### <a name="further-reading"></a>Altre informazioni:

1) [Connettersi al cluster HDInsight con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








