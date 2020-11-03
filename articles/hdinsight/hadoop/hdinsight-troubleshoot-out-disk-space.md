---
title: Spazio su disco insufficiente nel nodo del cluster in Azure HDInsight
description: Risoluzione Apache Hadoop dei problemi relativi allo spazio su disco del nodo cluster in HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: f1a994ad07980c67e37d00bffb7e605ed610bb08
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289086"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: il nodo del cluster esaurisce lo spazio su disco in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Un processo potrebbe non riuscire con un messaggio di errore simile al seguente: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

In alternativa, è possibile ricevere un avviso di Apache Ambari simile a: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Causa

La cache dell'applicazione Apache Yarn potrebbe avere utilizzato tutto lo spazio su disco disponibile. È probabile che l'applicazione Spark venga eseguita in modo non efficiente.

## <a name="resolution"></a>Soluzione

1. Usare l'interfaccia utente di Ambari per determinare il nodo che sta esaurendo lo spazio su disco.

1. Determinare quale cartella del nodo preoccupante contribuisce alla maggior parte dello spazio su disco. Connettersi prima di tutto SSH al nodo, quindi eseguire `df` per elencare l'utilizzo del disco per tutti i montaggi. In genere si tratta `/mnt` di un disco temporaneo usato da OSS. È possibile immettere in una cartella, quindi digitare `sudo du -hs` per visualizzare le dimensioni dei file riepilogati in una cartella. Se viene visualizzata una cartella simile a `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , significa che l'applicazione è ancora in esecuzione. La causa potrebbe essere la persistenza RDD o file shuffle intermedi.

1. Per attenuare il problema, terminare l'applicazione, in modo da rilasciare lo spazio su disco utilizzato dall'applicazione.

1. Se il problema si verifica di frequente nei nodi del ruolo di lavoro, è possibile ottimizzare le impostazioni della cache locale YARN nel cluster.

    Aprire l'interfaccia utente di Ambari passare a YARN--> configs--> Advanced.  
    Aggiungere le due proprietà seguenti alla sezione yarn-site.xml personalizzata e salvare:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Se il problema non è risolto in modo permanente, ottimizzare l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]