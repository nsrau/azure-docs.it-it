---
title: Errore RequestBodyTooLarge dall'app Apache Spark-Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge viene visualizzato nel log per Apache Spark app di streaming in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 38d6e5bfea1ae7ad4eead3a3f614007d31f0a7cb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287923"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "viene visualizzato nel registro applicazioni di Apache Spark streaming in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Errore: `NativeAzureFileSystem ... RequestBodyTooLarge` viene visualizzato nel log driver per un'app di Apache Spark streaming.

## <a name="cause"></a>Causa

È probabile che il file di registro eventi Spark raggiunga il limite di lunghezza del file per WASB.

In Spark 2,3 ogni app Spark genera un file di log eventi Spark. Il file di registro eventi Spark per un'app Spark streaming continua a crescere mentre l'app è in esecuzione. Attualmente un file in WASB ha un limite di 50000 blocchi e le dimensioni predefinite del blocco sono pari a 4 MB. Quindi, nella configurazione predefinita le dimensioni massime del file sono di 195 GB. Tuttavia, archiviazione di Azure ha aumentato le dimensioni massime del blocco a 100 MB, che hanno consentito il limite di file singolo a 4,75 TB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Soluzione

Sono disponibili tre soluzioni per questo errore:

* Aumentare le dimensioni del blocco fino a 100 MB. Nell'interfaccia utente di Ambari modificare la proprietà `fs.azure.write.request.size` di configurazione HDFS (o crearla nella `Custom core-site` sezione). Impostare la proprietà su un valore più grande, ad esempio: 33554432. Salvare la configurazione aggiornata e riavviare i componenti interessati.

* Arrestare periodicamente e inviare nuovamente il processo di Spark-streaming.

* Usare HDFS per archiviare i log eventi di Spark. L'uso di HDFS per l'archiviazione può comportare la perdita di dati evento Spark durante il ridimensionamento del cluster o gli aggiornamenti di Azure

    1. Apportare modifiche a `spark.eventlog.dir` e `spark.history.fs.logDirectory` tramite l'interfaccia utente di Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Creare directory in HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Riavviare tutti i servizi interessati tramite l'interfaccia utente di Ambari.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]