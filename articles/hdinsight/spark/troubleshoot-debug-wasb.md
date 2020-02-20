---
title: Operazioni di debug di file WASB in Azure HDInsight
description: Descrive i passaggi di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470718"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Operazioni di debug di file WASB in Azure HDInsight

In alcuni casi può essere utile comprendere le operazioni avviate dal driver WASB con archiviazione di Azure. Per il lato client, il driver WASB produce log per ogni operazione file system a livello di **debug** . Il driver WASB USA log4j per controllare il livello di registrazione e il livello predefinito è **info** . Per i log di analisi lato server di archiviazione di Azure, vedere [registrazione di analisi archiviazione di Azure](../../storage/common/storage-analytics-logging.md).

Un log prodotto sarà simile al seguente:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Attivare il log di debug di WASB per le operazioni sui file

1. Da un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, dove `CLUSTERNAME` è il nome del cluster Spark.

1. Passare a **Advanced spark2-log4j-Properties**.

    1. Modificare `log4j.appender.console.Threshold=INFO` in `log4j.appender.console.Threshold=DEBUG`.

    1. Aggiungere `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Passare a **Advanced livy2-log4j-Properties**.

    Aggiungere `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Salvare le modifiche.

## <a name="additional-logging"></a>Registrazione aggiuntiva

I registri sopra indicati devono fornire informazioni di alto livello sulle operazioni file system. Se i log precedenti non forniscono ancora informazioni utili o se si vuole esaminare le chiamate API di archiviazione BLOB, aggiungere `fs.azure.storage.client.logging=true` al `core-site`. Questa impostazione consente di abilitare i log di Java SDK per il driver di archiviazione Wasb e di stampare ogni chiamata al server di archiviazione BLOB. Rimuovere l'impostazione dopo l'analisi perché potrebbe riempire rapidamente il disco e potrebbe rallentare il processo.

Se il back-end è basato su Azure Data Lake, usare l'impostazione log4j seguente per il componente, ad esempio Spark/Tez/HDFS:

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Cercare i log in `/var/log/adl/adl.log` per i log.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
