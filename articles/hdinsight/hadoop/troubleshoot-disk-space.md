---
title: Gestire lo spazio su disco in Azure HDInsight
description: Procedure di risoluzione dei problemi e possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77473012"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Gestire lo spazio su disco in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="hive-log-configurations"></a>Configurazioni del log hive

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net` dove `CLUSTERNAME` è il nome del cluster.

1. Passare a **hive**  >  **configs**  >  **Advanced**  >  **Advanced hive-log4j**. Esaminare le impostazioni seguenti:

    * `hive.root.logger=DEBUG,RFA`. Questo è il valore predefinito, modificare il [livello di registrazione](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) in `INFO` per stampare le voci di log less.

    * `log4jhive.log.maxfilesize=1024MB`. Si tratta del valore predefinito, che viene modificato nel modo desiderato.

    * `log4jhive.log.maxbackupindex=10`. Si tratta del valore predefinito, che viene modificato nel modo desiderato. Se il parametro è stato omesso, i file di log generati saranno infiniti.

## <a name="yarn-log-configurations"></a>Configurazioni di log Yarn

Esaminare le configurazioni seguenti:

* Apache Ambari

    1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net` dove `CLUSTERNAME` è il nome del cluster.

    1. Passare a **hive**  >  **configs**  >  **Advanced**  >  **Gestione risorse**. Assicurarsi che l'opzione **Abilita aggregazione log** sia selezionata. Se disabilitato, i nodi dei nomi manterranno i log localmente e non li aggregano nell'archivio remoto al completamento o alla chiusura dell'applicazione.

* Verificare che le dimensioni del cluster siano appropriate per il carico di lavoro. È possibile che il carico di lavoro sia stato modificato di recente o che il cluster sia stato ridimensionato. [Ridimensionare](../hdinsight-scaling-best-practices.md) il cluster in modo che corrisponda a un carico di lavoro più elevato.

* `/mnt/resource`potrebbe essere compilato con file orfani, come nel caso del riavvio di Resource Manager. Se necessario, pulire manualmente `/mnt/resource/hadoop/yarn/log` e `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
