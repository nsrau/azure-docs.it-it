---
title: Debug WASB file operations in Azure HDInsight
description: Vengono descritti i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.Describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470718"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Debug WASB file operations in Azure HDInsight

In alcuni casi è possibile comprendere le operazioni avviate dal driver WASB con Archiviazione di Azure.There are times when you may want to understand what operations the WASB driver started with Azure Storage. Per il lato client, il driver WASB produce registri per ogni operazione del file system a livello **DI DEBUG.** Il driver WASB utilizza log4j per controllare il livello di registrazione e il valore predefinito è il livello **INFO.** Per i log di analisi lato server di Archiviazione di Azure, vedere [Registrazione dell'analisi](../../storage/common/storage-analytics-logging.md)di Archiviazione di Azure.For Azure Storage server-side analytics logs, see Azure Storage analytics logging .

Un registro prodotto sarà simile al seguente:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Attivare il registro di debug WASB per le operazioni sui file

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`passare `CLUSTERNAME` a , dove è il nome del cluster Spark.

1. Passare alle **proprietà avanzate spark2-log4j**.

    1. Modifica `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG`in .

    1. Aggiungere `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Passare a **Advanced livy2-log4j-properties**.

    Aggiungere `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Salvare le modifiche.

## <a name="additional-logging"></a>Registrazione aggiuntiva

I registri precedenti devono fornire una conoscenza generale delle operazioni del file system. Se i log precedenti non forniscono ancora informazioni utili o se `fs.azure.storage.client.logging=true` si `core-site`desidera analizzare le chiamate all'API di archiviazione BLOB, aggiungerle al metodo . Questa impostazione abiliterà i log sdk java per il driver di archiviazione wasb e stamperà ogni chiamata al server di archiviazione BLOB. Rimuovere l'impostazione dopo le indagini perché potrebbe riempire rapidamente il disco e potrebbe rallentare il processo.

Se il back-end è basato su Azure Data Lake, usare l'impostazione log4j seguente per il componente,ad esempio spark/tez/hdfs:If the backend is Azure Data Lake based, then use the following log4j setting for the component (for example, spark/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Cercare i registri `/var/log/adl/adl.log` per i registri.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
