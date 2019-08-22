---
title: Scritture accelerate di Azure HDInsight per Apache HBase
description: Offre una panoramica della funzionalità di scrittura accelerata di Azure HDInsight, che usa dischi gestiti Premium per migliorare le prestazioni del log write-ahead di Apache HBase.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 8b24c7517402aa6f29c95c0cd0f58bb1d51e1082
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876476"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Scritture accelerate di Azure HDInsight per Apache HBase

Questo articolo illustra in background la funzionalità di **scrittura accelerata** per Apache HBase in Azure HDInsight e come può essere usata in modo efficace per migliorare le prestazioni di scrittura. **Scritture accelerate** USA [dischi gestiti di unità SSD Premium di Azure](../../virtual-machines/linux/disks-types.md#premium-ssd) per migliorare le prestazioni del log di scrittura Ahead di Apache HBase. Per altre informazioni su Apache HBase, vedere [che cos'è Apache HBase in HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Panoramica dell'architettura di HBase

In HBase una **riga** è costituita da una o più **colonne** ed è identificata da una **chiave di riga**. Più righe costituiscono una **tabella**. Le colonne contengono **celle**, ovvero versioni con timestamp del valore in tale colonna. Le colonne vengono raggruppate in **famiglie di colonne**e tutte le colonne in una famiglia di colonne vengono archiviate insieme nei file di archiviazione denominati **HFiles**.

Le **aree** in HBase vengono usate per bilanciare il carico di elaborazione dei dati. HBase archivia innanzitutto le righe di una tabella in una singola area. Le righe vengono distribuite in più aree con l'aumentare della quantità di dati nella tabella. I **server di area** possono gestire le richieste per più aree.

## <a name="write-ahead-log-for-apache-hbase"></a>Log write-ahead per Apache HBase

HBase scrive prima di tutto gli aggiornamenti dei dati in un tipo di log di commit denominato log write-ahead (WAL). Dopo che l'aggiornamento è stato archiviato in WAL, viene scritto nella **MemStore**in memoria. Quando i dati in memoria raggiungono la capacità massima, vengono scritti su disco come **HFile**.

Se un **RegionServer** si arresta in modo anomalo o non è più disponibile prima dello scaricamento del MemStore, è possibile usare il log write-ahead per riprodurre gli aggiornamenti. Senza WAL, se si verifica un arresto anomalo di **RegionServer** prima di scaricare gli aggiornamenti in un **HFile**, tutti gli aggiornamenti andranno perduti.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funzionalità Scritture accelerate in Azure HDInsight per Apache HBase

La funzionalità Scritture accelerate risolve il problema delle latenze di scrittura più elevate causate dall'uso di log write-ahead presenti nell'archiviazione cloud.  La funzionalità di scrittura accelerata per i cluster Apache HBase di HDInsight, collega i dischi Premium gestiti da unità SSD a ogni RegionServer (nodo del ruolo di lavoro). I log write-ahead vengono quindi scritti nel file System Hadoop (HDFS) montato su questi dischi gestiti Premium anziché sull'archiviazione cloud.  Managed disks Premium usa dischi a stato solido (SSD) e offre ottime prestazioni di I/O con tolleranza di errore.  A differenza dei dischi non gestiti, se un'unità di archiviazione si interrompe, non influirà sulle altre unità di archiviazione nello stesso set di disponibilità.  Di conseguenza, i dischi gestiti garantiscono una bassa latenza di scrittura e una migliore resilienza per le applicazioni. Per altre informazioni sui dischi gestiti di Azure, vedere [Introduzione a Managed Disks di Azure](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Come abilitare le Scritture accelerate per HBase in HDInsight

Per creare un nuovo cluster HBase con la funzionalità di scrittura accelerata, seguire la procedura descritta in [configurare i cluster in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) fino a raggiungere il **passaggio 3, archiviazione**. In **Impostazioni Metastore**fare clic sulla casella di controllo accanto a **Abilita scritture accelerate (anteprima)** . Quindi, continuare con i passaggi rimanenti per la creazione del cluster.

![Abilitare l'opzione di scrittura accelerata per HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Altre considerazioni

Per mantenere la durabilità dei dati, creare un cluster con un minimo di tre nodi di lavoro. Una volta creato, non è possibile ridurre il cluster a meno di tre nodi di lavoro.

Scaricare o disabilitare le tabelle di HBase prima di eliminare il cluster, in modo da non perdere i dati di log write-ahead.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Passaggi successivi

* Documentazione ufficiale di Apache HBase sulla [funzionalità log write-ahead](https://hbase.apache.org/book.html#wal)
* Per aggiornare il cluster Apache HBase di HDInsight per l'uso di scritture accelerate, vedere [eseguire la migrazione di un cluster Apache HBase a una nuova versione](apache-hbase-migrate-new-version.md).
