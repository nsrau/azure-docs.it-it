---
title: Eseguire l'aggiornamento del cluster HDInsight a una versione più recente - Azure
description: Informazioni sulle linee guida per aggiornare il cluster Azure HDInsight a una versione più recente.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951157"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Eseguire l'aggiornamento del cluster HDInsight a una versione più recente

Per poter sfruttare le funzionalità più recenti di HDInsight, è consigliabile che i cluster HDInsight siano aggiornati alla versione più recente. Seguire le linee guida per aggiornare le versioni dei cluster HDInsight.

> [!NOTE]  
> Per altre informazioni sulle versioni supportate di HDInsight, vedere [Versioni del componente HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Procedure di aggiornamento

Il flusso di lavoro per eseguire l'aggiornamento del cluster HDInsight è il seguente.
![diagramma del flusso di lavoro di aggiornamento HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

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

## <a name="next-steps"></a>Fasi successive

* [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Connettersi a HDInsight tramite SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gestire un cluster basato su Linux tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Note sulla versione di HDInsight](./hdinsight-version-release.md)
