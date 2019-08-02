---
title: Errore di spazio heap Java durante il tentativo di aprire Apache Spark server di cronologia in Azure HDInsight
description: Non è possibile avviare il server Apache Livio con Java. lang. OutOfMemoryError in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667794"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Scenario: Errore di spazio heap Java durante il tentativo di aprire Apache Spark server di cronologia in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Viene visualizzato l'errore seguente durante l'apertura di eventi nel server della cronologia Spark:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Causa

Questo problema è spesso dovuto a una mancanza di risorse durante l'apertura di grandi file di eventi Spark. Per impostazione predefinita, le dimensioni dell'heap Spark sono impostate su 1 GB, ma i file di eventi Spark di grandi dimensioni potrebbero richiedere più di questo.

Se si desidera verificare le dimensioni dei file che si sta tentando di caricare, è possibile eseguire i comandi seguenti:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Risoluzione

È possibile aumentare la memoria del server della cronologia di Spark `SPARK_DAEMON_MEMORY` modificando la proprietà nella configurazione di Spark e riavviando tutti i servizi.

È possibile eseguire questa operazione dall'interfaccia utente del browser Ambari selezionando la sezione Spark2/config/Advanced Spark2-ENV.

![Sezione Advanced spark2-ENV](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Aggiungere la proprietà seguente per modificare la memoria del server della cronologia di Spark da 1g `SPARK_DAEMON_MEMORY=4g`a 4G:.

![Proprietà Spark](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Assicurarsi di riavviare tutti i servizi interessati da Ambari.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
