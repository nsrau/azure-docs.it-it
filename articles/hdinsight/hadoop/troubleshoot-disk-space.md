---
title: Gestire lo spazio su disco in Azure HDInsightManage disk space in Azure HDInsight
description: Passaggi per la risoluzione dei problemi e possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.Troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473012"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Gestire lo spazio su disco in Azure HDInsightManage disk space in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="hive-log-configurations"></a>Configurazioni del log Hive

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Passare a **Hive** > **Configs** > **Advanced** > **Hive-log4j**. Rivedere le seguenti impostazioni:

    * `hive.root.logger=DEBUG,RFA`. Questo è il valore predefinito, `INFO` modificare il livello di [registrazione](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) per stampare meno voci di registro.

    * `log4jhive.log.maxfilesize=1024MB`. Questo è il valore predefinito, modificare come desiderato.

    * `log4jhive.log.maxbackupindex=10`. Questo è il valore predefinito, modificare come desiderato. Se il parametro è stato omesso, i file di log generati saranno infiniti.

## <a name="yarn-log-configurations"></a>Configurazioni del log di filati

Esaminare le seguenti configurazioni:

* Apache Ambari

    1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

    1. Passare a **Hive** > **Configs** > **Advanced** > Resource**Manager**. Verificare che **l'opzione Abilita aggregazione log** sia selezionata. Se disabilitata, i nodi dei nomi manterranno i log localmente e non li aggregheranno nell'archivio remoto al completamento o alla chiusura dell'applicazione.

* Verificare che le dimensioni del cluster siano appropriate per il carico di lavoro. Il carico di lavoro potrebbe essere stato modificato di recente o il cluster potrebbe essere stato ridimensionato. [Aumentare](../hdinsight-scaling-best-practices.md) la scalabilità del cluster in modo che corrisponda a un carico di lavoro superiore.

* `/mnt/resource`potrebbe essere riempito con file orfani (come nel caso del riavvio del gestore delle risorse). Se necessario, `/mnt/resource/hadoop/yarn/log` pulire `/mnt/resource/hadoop/yarn/local`manualmente e .

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
