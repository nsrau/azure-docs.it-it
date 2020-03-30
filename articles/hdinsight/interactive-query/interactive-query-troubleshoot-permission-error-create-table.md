---
title: Errore di autorizzazione negata con la tabella Apache Hive in Azure HDInsightPermission denied error with Apache Hive table in Azure HDInsight
description: Errore di autorizzazione negata durante il tentativo di creare una tabella Apache Hive in Azure HDInsightPermission denied error when trying to create an Apache Hive table in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895154"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenario: Permission denied error when trying to create an Apache Hive table in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti di query interattivi nei cluster di Azure HDInsight.This article describes troubleshooting steps and possible resolutions for issues when using Interactive Query components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si tenta di creare una tabella, verrà visualizzato il seguente errore:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Se si esegue il seguente comando di archiviazione HDFS, verrà visualizzato un messaggio di errore simile:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Causa

La possibilità di creare una tabella in Apache Hive è decisa dalle autorizzazioni applicate all'account di archiviazione del cluster. Se le autorizzazioni dell'account di archiviazione del cluster non sono corrette, non sarà possibile creare tabelle. Ciò significa che è possibile disporre dei criteri Ranger corretti per la creazione di tabelle e visualizzare ancora gli errori "Autorizzazione negata".

## <a name="resolution"></a>Risoluzione

Ciò è causato dalla mancanza di autorizzazioni sufficienti per il contenitore di archiviazione in uso. L'utente che crea la tabella Hive deve disporre delle autorizzazioni di lettura, scrittura ed esecuzione per il contenitore. Per ulteriori informazioni, vedere [Procedure consigliate per l'autorizzazione dell'hive tramite Apache Ranger in HDP 2.2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
