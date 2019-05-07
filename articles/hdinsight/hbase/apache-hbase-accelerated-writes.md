---
title: Azure HDInsight con accelerazione scritture per Apache HBase
description: Fornisce una panoramica della funzionalità scrive accelerata di Azure HDInsight, che usa dischi gestiti premium per migliorare le prestazioni di Apache HBase Write-Ahead Log.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: f9314355d3dce8d96b794cdb81ab9c3d77faab45
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159301"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight con accelerazione scritture per Apache HBase

Questo articolo illustra i **Accelerated scrive** funzionalità per Apache HBase in HDInsight di Azure e come può essere usato in modo efficace per migliorare le prestazioni di scrittura. **Accelerated scritture** viene utilizzato [dischi gestiti di Azure premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) per migliorare le prestazioni di Apache HBase Write-Ahead Log (WAL). Per altre informazioni su Apache HBase, vedere [che cos'è Apache HBase in HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Panoramica dell'architettura di HBase

In HBase, un **riga** costituito da uno o più **colonne** ed è identificato da un **chiave di riga**. Più righe costituiscono una **tabella**. Le colonne contengono **celle**, quali sono le versioni con timestamp del valore nella colonna. Sono raggruppate per colonne **famiglie di colonne**, e tutte le colonne in una famiglia di colonne vengono archiviate insieme nei file di archiviazione chiamati **HFiles**.

**Aree** in HBase vengono usate per bilanciare il carico di elaborazione dei dati. HBase Archivia prima di tutto le righe di una tabella in una singola area. Le righe vengono distribuite tra più aree come la quantità di dati nell'oggetto tabella aumenta. **Server di area** può gestire le richieste per più aree.

## <a name="write-ahead-log-for-apache-hbase"></a>Log Write-Ahead per Apache HBase

HBase scrive innanzitutto gli aggiornamenti dei dati in un tipo di log di commit denominato un Write-Ahead Log (log write-AHEAD). Dopo l'aggiornamento viene archiviato nel log write-AHEAD, non vengono scritti in memoria **MemStore**. Quando i dati in memoria raggiungono la capacità massima, che è scritta su disco come un **HFile**.

Se un **RegionServer** si blocca o diventa non disponibile prima che l'archivio MemStore viene scaricato, il Log Write-Ahead utilizzabile per riprodurre gli aggiornamenti. Senza il log write-AHEAD, se un **RegionServer** arresti anomali prima dello scaricamento degli aggiornamenti a un **HFile**, tutti gli aggiornamenti vengono persi.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funzionalità di scritture accelerata in Azure HDInsight per Apache HBase

La funzionalità di accelerazione scrive risolve il problema di scrittura-latenze elevate causato dall'utilizzo di log Write che sono nella memoria cloud.  La funzionalità di accelerazione scrive per HDInsight Apache HBase cluster, consente di collegare unità SSD, managed disks premium per ogni RegionServer (nodo di lavoro). Write-Ahead i log vengono quindi scritti per Hadoop File System (HDFS) montato su questi managed disks di premium invece di archiviazione cloud.  Managed disks Premium usano dischi SSD (Solid State Drive) e offrono prestazioni dei / o eccellenti con tolleranza di errore.  A differenza dei dischi non gestiti, se un'unità di archiviazione diventa inattiva, non influirà altre unità di archiviazione nello stesso set di disponibilità.  Di conseguenza, servizio managed disks fornire bassa latenza scrittura e una migliore resilienza per le applicazioni. Per altre informazioni sui dischi gestiti di Azure, vedere [Introduzione ad Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Come abilitare accelerata scrive per HBase in HDInsight

Per creare un nuovo cluster HBase con la funzionalità di accelerazione scrive, seguire i passaggi descritti in [configurare i cluster di HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) fino a raggiungere **passaggio 3, spazio di archiviazione**. Sotto **impostazioni Metastore**, fare clic sulla casella di controllo accanto a **abilitare accelerata scrive (anteprima)**. Continuare quindi con i passaggi rimanenti per la creazione del cluster.

![Abilitare l'opzione di scritture accelerata per Apache HBase di HDInsight](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Altre considerazioni

Per mantenere la durabilità dei dati, creare un cluster con un minimo di tre nodi di lavoro. Una volta creato, è possibile ridimensionare il cluster a meno di tre nodi di lavoro.

Scaricare o disabilitare le tabelle HBase prima di eliminare il cluster, in modo da non perdere i dati Write-Ahead Log.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Passaggi successivi

* Documentazione ufficiale di Apache HBase nel [funzionalità Write-Ahead Log](https://hbase.apache.org/book.html#wal)
* Per aggiornare il cluster di Apache HBase di HDInsight per l'uso con accelerazione scrive, vedere [eseguire la migrazione di un cluster Apache HBase in una nuova versione](apache-hbase-migrate-new-version.md).
