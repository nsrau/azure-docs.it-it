---
title: Scritture accelerate di Azure HDInsight per Apache HBase
description: Viene fornita una panoramica della funzionalità di scrittura accelerata di Azure HDInsight, che usa dischi gestiti premium per migliorare le prestazioni del log di scrittura Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764601"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Scritture accelerate di Azure HDInsight per Apache HBase

Questo articolo fornisce informazioni generali sulla funzionalità **Di scritture accelerate** per Apache HBase in Azure HDInsight e su come può essere usata in modo efficace per migliorare le prestazioni di scrittura. **Le scritture accelerate** usano [i dischi gestiti SSD premium](../../virtual-machines/linux/disks-types.md#premium-ssd) di Azure per migliorare le prestazioni del log WAL (Apache HBase Write Ahead Log). Per ulteriori informazioni su Apache HBase, vedere [Che cos'è Apache HBase in HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Panoramica dell'architettura HBase

In HBase, una **riga** è costituita da una o più **colonne** ed è identificata da una chiave di **riga**. Più righe costituiscono una **tabella**. Le colonne contengono **celle**, ovvero versioni con timestamp del valore in tale colonna. Le colonne vengono raggruppate in famiglie di **colonne**e tutte le colonne di una famiglia di colonne vengono archiviate insieme in file di archiviazione **denominati HFiles**.

**Le aree** in HBase vengono utilizzate per bilanciare il carico di elaborazione dei dati. HBase archivia innanzitutto le righe di una tabella in una singola area. Le righe vengono distribuite su più aree man mano che aumenta la quantità di dati nella tabella. **I server di** area possono gestire le richieste per più aree.

## <a name="write-ahead-log-for-apache-hbase"></a>Scrivi ahead Log per Apache HBase

HBase scrive innanzitutto gli aggiornamenti dei dati in un tipo di log di commit denominato Write Ahead Log (WAL). Dopo l'aggiornamento è memorizzato nel WAL, viene scritto in memoria **MemStore**. Quando i dati in memoria raggiungono la capacità massima, vengono scritti su disco come **HFile**.

Se un **RegionServer** si arresta in modo anomalo o non è più disponibile prima che il MemStore venga scaricato, è possibile utilizzare il registro Write Ahead per riprodurre gli aggiornamenti. Senza WAL, se un **RegionServer** si arresta in modo anomalo prima di scaricare gli aggiornamenti a un **HFile**, tutti gli aggiornamenti vengono persi.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funzionalità di scrittura accelerata in Azure HDInsight per Apache HBaseAccelerated Writes feature in Azure HDInsight for Apache HBase

La funzionalità Scritture Accelerate d'accelerazione risolve il problema delle latenze di scrittura più elevate causate dall'utilizzo di write Ahead Logs presenti nell'archiviazione cloud.  La funzionalità di scrittura accelerata per i cluster HDInsight Apache HBase, collega dischi gestiti con SSD premium a ogni RegionServer (nodo di lavoro). I registri Write Ahead vengono quindi scritti nel file system Hadoop (HDFS) montato su questi dischi gestiti premium anziché nell'archiviazione cloud.  I dischi gestiti Premium utilizzano dischi a stato solido (SSD) e offrono prestazioni di I/O eccellenti con tolleranza di errore.  A differenza dei dischi non gestiti, se un'unità di archiviazione si arresta, non influirà sulle altre unità di archiviazione nello stesso set di disponibilità.  Di conseguenza, i dischi gestiti forniscono una bassa latenza di scrittura e una migliore resilienza per le applicazioni. Per altre informazioni sui dischi gestiti da Azure, vedere [Introduzione ai dischi gestiti](../../virtual-machines/windows/managed-disks-overview.md)di Azure.To learn more about Azure-managed disks, see Introduction to Azure managed disks .

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Come abilitare le scritture accelerate per HBase in HDInsightHow to enable Accelerated Writes for HBase in HDInsight

Per creare un nuovo cluster HBase con la funzionalità di scrittura accelerata, seguire i passaggi descritti in [Configurare i cluster in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) fino a raggiungere il **passaggio 3, Archiviazione**. In **Impostazioni Metastore**selezionare la casella di controllo accanto a **Abilita scritture accelerate HBase**. Quindi, continuare con i passaggi rimanenti per la creazione del cluster.

![Abilitare l'opzione di scrittura accelerata per HDInsight Apache HBaseEnable accelerated writes option for HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Altre considerazioni

Per mantenere la durabilità dei dati, creare un cluster con un minimo di tre nodi di lavoro. Una volta creato, non è possibile ridurre il cluster a meno di tre nodi di lavoro.

Svuotare o disabilitare le tabelle HBase prima di eliminare il cluster, in modo da non perdere i dati Write Ahead Log.

```
flush 'mytable'
```

```
disable 'mytable'
```

Seguire passaggi simili quando si riduce il cluster: svuotare le tabelle e disabilitare le tabelle per arrestare i dati in ingresso. Non è possibile ridurre il cluster a meno di tre nodi.

Seguire questi passaggi garantirà una scalabilità ridotta corretta ed eviterà la possibilità che un nodo name vada in modalità provvisoria a causa di file sottoreplicati o temporanei.

Se il namenode passa in modalità provvisoria dopo una scalabilità verticale, usa i comandi hdfs per replicare nuovamente i blocchi sottoreplicati e uscire dalla modalità provvisoria. Questa ri-replica consentirà di riavviare HBase correttamente.

## <a name="next-steps"></a>Passaggi successivi

* Documentazione ufficiale di Apache HBase sulla [funzione Write Ahead Log](https://hbase.apache.org/book.html#wal)
* Per aggiornare il cluster HDInsight Apache HBase per l'utilizzo di Scritture Accelerated Writes, vedere [Migrate an Apache HBase cluster to a new version.](apache-hbase-migrate-new-version.md)
