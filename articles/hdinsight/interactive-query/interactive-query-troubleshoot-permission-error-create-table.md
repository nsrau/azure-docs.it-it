---
title: Errore di autorizzazione negata con Apache Hive tabella in Azure HDInsight
description: Errore di autorizzazione negata durante il tentativo di creare una tabella Apache Hive in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895154"
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

## <a name="resolution"></a>Soluzione

Questo problema è causato dalla mancanza di autorizzazioni sufficienti per il contenitore di archiviazione in uso. Per l'utente che crea la tabella hive è necessario disporre delle autorizzazioni di lettura, scrittura ed esecuzione per il contenitore. Per altre informazioni, vedere [procedure consigliate per l'autorizzazione hive usando Apache Ranger in HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
