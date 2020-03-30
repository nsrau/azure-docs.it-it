---
title: Eccezione di archiviazione dopo la reimpostazione della connessione in Azure HDInsightStorage exception after connection reset in Azure HDInsight
description: Eccezione di archiviazione dopo la reimpostazione della connessione in Azure HDInsightStorage exception after connection reset in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887224"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenario: Storage exception after connection reset in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Impossibile creare una nuova tabella Apache HBase.

## <a name="cause"></a>Causa

Durante un processo di troncamento della tabella, si è verificato un problema di connessione di archiviazione. La voce della tabella è stata eliminata nella tabella dei metadati HBase.The table entry was deleted in HBase metadata table. Tutti i file BLOB tranne uno sono stati eliminati.

Anche se non c'era nessun BLOB di cartelle chiamato `/hbase/data/default/ThatTable` seduto nell'archivio. Il driver WASB ha rilevato l'esistenza del file BLOB precedente `/hbase/data/default/ThatTable` e non consente di creare alcun BLOB chiamato perché presupponechessero l'esistenza delle cartelle padre, pertanto la creazione della tabella avrà esito negativo.

## <a name="resolution"></a>Risoluzione

1. Dall'interfaccia utente di Apache Ambari, riavviare l'HMaster attivo. In questo modo uno dei due HMaster di standby diventerà quello attivo e il nuovo HMaster attivo ricarica le informazioni della tabella dei metadati. Pertanto non verrà `already-deleted` visualizzata la tabella nell'interfaccia utente HMaster.

1. È possibile trovare il file BLOB orfano dagli `hdfs dfs -ls /xxxxxx/yyyyy`strumenti dell'interfaccia utente come Cloud Explorer o eseguire il comando come . Eseguire `hdfs dfs -rmr /xxxxx/yyyy` per eliminare il BLOB. Ad esempio: `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

A questo punto è possibile creare una nuova tabella con lo stesso nome in HBase.Now you can create new table with the same name in HBase.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
