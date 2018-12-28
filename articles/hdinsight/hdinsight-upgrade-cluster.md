---
title: Eseguire l'aggiornamento del cluster HDInsight a una versione più recente - Azure
description: Informazioni su come eseguire l'aggiornamento del cluster HDInsight a una versione più recente
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 2ebfc4b0d9bb71cf51e0927e6399d30488c2f939
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409429"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Eseguire l'aggiornamento del cluster HDInsight a una versione più recente
Per poter sfruttare le funzionalità più recenti di HDInsight, è consigliabile che i cluster HDInsight siano aggiornati alla versione più recente. Seguire le linee guida per aggiornare le versioni dei cluster HDInsight.

> [!NOTE]  
> Per altre informazioni sulle versioni supportate di HDInsight, vedere [Versioni del componente HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
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

1.  Eseguire il backup tutti i dati temporanei archiviati localmente sui nodi del cluster, ad esempio se i dati sono archiviati direttamente in un nodo head.
2.  Eliminare il cluster esistente.
3.  Creare un cluster nella stessa subnet di rete virtuale con la versione più recente supportata di HDI, usando lo stesso archivio dati predefinito usato dal cluster precedente. In questo modo il nuovo cluster continuerà a lavorare con i dati di produzione esistenti.
4.  Importare i dati temporanei di cui è stata eseguita una copia di backup.
5.  Avviare processi/continuare l'elaborazione con il nuovo cluster.

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Connettersi a HDInsight tramite SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gestire un cluster basato su Linux tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)

