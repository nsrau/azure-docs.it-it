---
title: RequestBodyTooLarge errore dall'app Apache Spark - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge viene visualizzato nel log per l'app di streaming Apache Spark in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894390"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" viene visualizzato nel log dell'app di streaming Apache Spark in HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso dei componenti Apache Spark nei cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when using Apache Spark components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

L'errore: `NativeAzureFileSystem ... RequestBodyTooLarge` viene visualizzato nel log del driver per un'app di streaming Apache Spark.

## <a name="cause"></a>Causa

È probabile che il file di registro degli eventi Spark stia raggiungendo il limite di lunghezza del file per WASB.

In Spark 2.3 ogni app Spark genera un file di log eventi Spark. Il file di log degli eventi Spark per un'app Spark streaming continua a crescere mentre l'app è in esecuzione. Oggi un file su WASB ha un limite di blocco 50000 e la dimensione predefinita del blocco è 4 MB. Quindi nella configurazione predefinita la dimensione massima del file è 195 GB. Tuttavia, Archiviazione di Azure ha aumentato la dimensione massima del blocco a 100 MB, che ha portato in modo efficace il limite di singoli file a 4,75 TB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Risoluzione

Sono disponibili tre soluzioni per questo errore:There are three solutions available for this error:

* Aumentare la dimensione del blocco fino a 100 MB. Nell'interfaccia utente di Ambari, modificare la proprietà `fs.azure.write.request.size` di configurazione HDFS (o crearla nella `Custom core-site` sezione). Impostare la proprietà su un valore maggiore, ad esempio: 33554432. Salvare la configurazione aggiornata e riavviare i componenti interessati.

* Arrestare e inviare nuovamente il processo di spark streaming.

* Utilizzare HDFS per archiviare i registri eventi spark. L'uso di HDFS per l'archiviazione può comportare la perdita dei dati degli eventi Spark durante la scalabilità del cluster o gli aggiornamenti di Azure.Using HDFS for storage may result in loss of Spark event data during cluster scaling or Azure upgrades.

    1. Apportare `spark.eventlog.dir` modifiche `spark.history.fs.logDirectory` a e tramite Ambari UI:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Creare directory su HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Riavviare tutti i servizi interessati tramite Ambari UI.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
