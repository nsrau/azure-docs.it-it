---
title: Eseguire la migrazione del cluster a una versione più recente
titleSuffix: Azure HDInsight
description: Informazioni sulle linee guida per eseguire la migrazione del cluster HDInsight di Azure a una versione più recente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023974"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Eseguire la migrazione del cluster HDInsight a una versione più recente

Per sfruttare le funzionalità più recenti di HDInsight, è consigliabile eseguire regolarmente la migrazione dei cluster HDInsight alla versione più recente. HDInsight non supporta gli aggiornamenti sul posto in cui un cluster esistente viene aggiornato a una versione più recente del componente. È necessario creare un nuovo cluster con il componente e la versione della piattaforma desiderati, quindi eseguire la migrazione delle applicazioni per l'uso del nuovo cluster. Seguire le linee guida seguenti per eseguire la migrazione delle versioni del cluster HDInsight.

> [!NOTE]  
> Per altre informazioni sulle versioni supportate di HDInsight, vedere [Versioni del componente HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Attività di migrazione

Il flusso di lavoro per eseguire l'aggiornamento del cluster HDInsight è il seguente.
![Diagramma del flusso di lavoro di aggiornamento HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Leggere interamente questo documento per comprendere le modifiche che potrebbero essere necessarie durante l'aggiornamento del cluster HDInsight.
2. Creare un cluster come ambiente di test/controllo qualità. Per altre informazioni sulla creazione di un cluster, vedere [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiare processi esistenti, origini dati e sink nel nuovo ambiente.
4. Eseguire il test di convalida per assicurarsi che i processi funzionino come previsto nel nuovo cluster.

Dopo avere verificato che tutto funzioni come previsto, pianificare i tempi di inattività per la migrazione. Durante questo periodo di inattività, eseguire le operazioni seguenti.

1. Eseguire il backup tutti i dati temporanei archiviati localmente sui nodi del cluster, ad esempio se i dati sono archiviati direttamente in un nodo head.
1. [Eliminare il cluster esistente](./hdinsight-delete-cluster.md).
1. Creare un cluster nella stessa subnet di rete virtuale con la versione più recente supportata di HDI, usando lo stesso archivio dati predefinito usato dal cluster precedente. In questo modo il nuovo cluster continuerà a lavorare con i dati di produzione esistenti.
1. Importare i dati temporanei di cui è stata eseguita una copia di backup.
1. Avviare processi/continuare l'elaborazione con il nuovo cluster.

## <a name="workload-specific-guidance"></a>Linee guida specifiche per il carico di lavoro

I documenti seguenti forniscono indicazioni su come eseguire la migrazione di carichi di lavoro specifici:

* [Eseguire la migrazione di HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Eseguire la migrazione di Kafka](./kafka/migrate-versions.md)
* [Eseguire la migrazione di query hive/interattive](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Backup e ripristino

Per altre informazioni sul backup e il ripristino del database, vedere [ripristinare un database SQL di Azure usando i backup automatici del database](../sql-database/sql-database-recovery-using-backups.md).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Connettersi a HDInsight tramite SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gestire un cluster basato su Linux tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Note sulla versione di HDInsight](./hdinsight-version-release.md)
