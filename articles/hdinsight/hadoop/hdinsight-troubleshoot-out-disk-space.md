---
title: Cluster node runs out of disk space in Azure HDInsight
description: Risoluzione dei problemi di spazio su disco del nodo del cluster Apache Hadoop in Azure HDInsight.Troubleshooting Apache Hadoop cluster node disk space issues in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894118"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: Cluster node runs out of disk space in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Un processo potrebbe non riuscire con un messaggio di errore simile al:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Oppure potresti ricevere un avviso Apache Ambari simile al: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Causa

È possibile che la cache dell'applicazione Apache Yarn abbia consumato tutto lo spazio disponibile su disco. È probabile che l'applicazione Spark sia in esecuzione in modo inefficiente.

## <a name="resolution"></a>Risoluzione

1. Utilizzare l'interfaccia utente di Ambari per determinare quale nodo sta esaurendo lo spazio su disco.

1. Determinare quale cartella nel nodo problematico contribuisce alla maggior parte dello spazio su disco. SSH al nodo prima, `df` quindi eseguire per elencare l'utilizzo del disco per tutte le cavalcature. Di solito `/mnt` è che è un disco temporaneo utilizzato da OSS. È possibile immettere una `sudo du -hs` cartella, quindi digitare per visualizzare le dimensioni dei file riepilogati in una cartella. Se viene visualizzata `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`una cartella simile a , significa che l'applicazione è ancora in esecuzione. Ciò potrebbe essere dovuto alla persistenza RDD o ai file intermedi di shuffle.

1. Per attenuare il problema, terminare l'applicazione, che rilascerà spazio su disco utilizzato dall'applicazione.

1. Per risolvere il problema, ottimizzare l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
