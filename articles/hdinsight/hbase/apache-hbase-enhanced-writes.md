---
title: Azure HDInsight avanzato scrive per Apache HBase (anteprima)
description: Fornisce una panoramica della funzionalità scrive avanzato di Azure HDInsight, che usa dischi gestiti premium per migliorare le prestazioni di Apache HBase Write-Ahead Log.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: b0c71d0f101ea262b6ce56c845ef9f375a7de85e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804208"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight avanzato scrive per Apache HBase (anteprima)

Questo articolo illustra i **migliorato scrive** funzionalità per Apache HBase in HDInsight di Azure e come può essere usato in modo efficace per migliorare le prestazioni di scrittura. **Operazioni di scrittura avanzato** viene utilizzato [dischi gestiti di Azure premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) per migliorare le prestazioni di Apache HBase Write-Ahead Log (WAL). Per altre informazioni su Apache HBase, vedere [che cos'è Apache HBase in HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Panoramica dell'architettura di HBase

In HBase, un **riga** costituito da uno o più **colonne** ed è identificato da un **chiave di riga**. Più righe costituiscono una **tabella**. Le colonne contengono **celle**, quali sono le versioni con timestamp del valore nella colonna. Sono raggruppate per colonne **famiglie di colonne**, e tutte le colonne in una famiglia di colonne vengono archiviate insieme nei file di archiviazione chiamati **HFiles**.

**Aree** in HBase vengono usate per bilanciare il carico di elaborazione dei dati. HBase Archivia prima le righe di una tabella in una singola area e quindi distribuisce le righe tra più aree come la quantità di dati nell'oggetto tabella aumenta. **Server di area** può gestire le richieste per più aree.

## <a name="write-ahead-log-for-apache-hbase"></a>Log Write-Ahead per Apache HBase

Gli aggiornamenti dei dati in HBase vengono prima scritti in un tipo di log di commit denominato un Write-Ahead Log (log write-AHEAD). Dopo l'aggiornamento viene archiviato nel log write-AHEAD, non vengono scritti in memoria **MemStore**. Quando i dati in memoria raggiungono la capacità massima, che è scritta su disco come un **HFile**.

Se un **RegionServer** si blocca o diventa non disponibile prima che l'archivio MemStore viene scaricato, il Log Write-Ahead utilizzabile per riprodurre gli aggiornamenti. Senza il log write-AHEAD, se un **RegionServer** arrestato in modo anomalo prima dello scaricamento degli aggiornamenti a un **HFile**, tutti gli aggiornamenti perduti.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funzionalità di operazioni di scrittura avanzato in Azure HDInsight per Apache HBase

La funzionalità avanzata scrive risolve il problema di scrittura-latenze elevate causato dall'utilizzo di log Write che sono nella memoria cloud.  La funzionalità avanzata scrive per i cluster HDInsight Apache HBase, managed disks per ogni RegionServer (nodo di lavoro) premium consente di collegare unità SSD. Write-Ahead i log vengono quindi scritti per Hadoop File System (HDFS) montato su tali dischi gestiti premium invece di archiviazione cloud.  Managed disks Premium usano dischi SSD (Solid State Drive) e offrono prestazioni dei / o eccellenti con tolleranza di errore.  A differenza dei dischi non gestiti, se un'unità di archiviazione diventa inattiva, non influirà altre unità di archiviazione nello stesso set di disponibilità.  Di conseguenza, managed disks offre bassa latenza scrittura e una migliore resilienza per le applicazioni. Per altre informazioni su Azure managed disks, vedere [Introduzione ad Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>Come abilitare avanzato scrive per HBase in HDInsight

Per creare un nuovo cluster HBase con la funzionalità avanzata scrive, seguire i passaggi descritti in [configurare i cluster di HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) fino a raggiungere **passaggio 3, spazio di archiviazione**. Sotto **impostazioni Metastore**, fare clic sulla casella di controllo accanto a **abilitare avanzato scrive (anteprima)**. Continuare quindi con i passaggi rimanenti per la creazione del cluster.

![Abilitare l'opzione di scrittura avanzata di Apache HBase di HDInsight](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

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
* Per aggiornare il cluster di Apache HBase di HDInsight per l'uso avanzato scrive, vedere [eseguire la migrazione di un cluster Apache HBase in una nuova versione](apache-hbase-migrate-new-version.md).
