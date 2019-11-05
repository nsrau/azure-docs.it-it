---
title: Errore di autorizzazione negata con Apache Hive tabella in Azure HDInsight
description: Errore di autorizzazione negata durante il tentativo di creare una tabella Apache Hive in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: d9901132af992ea95a60773f404b1351386cfbcb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494202"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenario: errore di autorizzazione negata durante il tentativo di creare una tabella Apache Hive in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si tenta di creare una tabella, viene visualizzato l'errore seguente:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Se si esegue il comando di archiviazione HDFS seguente verrà visualizzato un messaggio di errore simile al seguente:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Causa

La possibilità di creare una tabella in Apache Hive viene decisa dalle autorizzazioni applicate all'account di archiviazione del cluster. Se le autorizzazioni dell'account di archiviazione del cluster non sono corrette, non sarà possibile creare tabelle. Ciò significa che è possibile avere i criteri Ranger corretti per la creazione di tabelle e visualizzare comunque gli errori "autorizzazione negata".

## <a name="resolution"></a>Risoluzione

Questo problema è causato dalla mancanza di autorizzazioni sufficienti per il contenitore di archiviazione in uso. Per l'utente che crea la tabella hive è necessario disporre delle autorizzazioni di lettura, scrittura ed esecuzione per il contenitore. Per altre informazioni, vedere [procedure consigliate per l'autorizzazione hive usando Apache Ranger in HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* È possibile connettersi con [@AzureSupport](https://twitter.com/azuresupport) , l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedi [Come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la fatturazione e la gestione della sottoscrizione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
