---
title: Errore di autorizzazione negata con Apache Hive tabella in Azure HDInsight
description: Errore di autorizzazione negata durante il tentativo di creare una tabella Apache Hive in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288897"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]