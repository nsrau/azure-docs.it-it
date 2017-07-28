---
title: "Eseguire l&quot;aggiornamento del cluster HDInsight a una versione più recente: Azure | Microsoft Docs"
description: "Informazioni su come eseguire l&quot;aggiornamento del cluster HDInsight a una versione più recente"
services: hdinsight
documentationcenter: 
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: bhanupr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: fa2e37bd922690322ccc3d8f68128180d013b701
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Eseguire l'aggiornamento del cluster HDInsight a una versione più recente
Per poter sfruttare le funzionalità più recenti di HDInsight, è consigliabile che i cluster HDInsight siano aggiornati alla versione più recente. Seguire le linee guida per aggiornare le versioni dei cluster HDInsight.

> [!NOTE]
> Le versioni 3.2 e 3.3 dei cluster HDInsight sono prossime alla data di ritiro. Per altre informazioni sulle versioni di HDInsight vedere [Versioni del componente HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Procedure di aggiornamento
Il flusso di lavoro per eseguire l'aggiornamento del cluster HDInsight è il seguente.

![Diagramma del flusso di lavoro di aggiornamento](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Leggere interamente questo documento per comprendere le modifiche che potrebbero essere necessarie durante l'aggiornamento del cluster HDInsight.
2. Creare un cluster come ambiente di test/controllo qualità. Per altre informazioni sulla creazione di un cluster, vedere [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiare processi esistenti, origini dati e sink nel nuovo ambiente. Per altri dettagli vedere la sezione [Copiare i dati nell'ambiente di test](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment).
4. Eseguire il test di convalida per assicurarsi che i processi funzionino come previsto nel nuovo cluster.


Dopo avere verificato che tutto funzioni come previsto, pianificare i tempi di inattività per la migrazione. Durante questo periodo di inattività, eseguire le operazioni seguenti.

1.    Eseguire il backup tutti i dati temporanei archiviati localmente sui nodi del cluster, ad esempio se i dati sono archiviati direttamente in un nodo head.
2.    Eliminare il cluster esistente.
3.    Creare un cluster nella stessa subnet di rete virtuale con la versione più recente supportata di HDI, usando lo stesso archivio dati predefinito usato dal cluster precedente. In questo modo il nuovo cluster continuerà a lavorare con i dati di produzione esistenti.
4.    Importare i dati temporanei di cui è stata eseguita una copia di backup.
5.    Avviare processi/continuare l'elaborazione con il nuovo cluster.

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Connettersi a HDInsight tramite SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gestire un cluster basato su Linux tramite Ambari](hdinsight-hadoop-manage-ambari.md)


