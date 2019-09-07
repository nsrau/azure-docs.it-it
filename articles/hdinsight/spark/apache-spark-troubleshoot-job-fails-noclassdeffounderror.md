---
title: NoClassDefFoundError-Apache Spark con Apache Kafka dati in Azure HDInsight
description: Apache Spark processo di streaming che legge i dati da un cluster Apache Kafka ha esito negativo con un NoClassDefFoundError in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: b7cbf4d460f872f33c447d0eea8430916d65bd18
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736273"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>Scenario: Apache Spark processo di streaming che legge i dati da un cluster Apache Kafka ha esito negativo con un NoClassDefFoundError in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il cluster Apache Spark esegue un processo di Spark streaming che legge i dati da un cluster Apache Kafka. Il processo di Spark streaming non riesce se la compressione del flusso Kafka è attivata. In questo caso l'app application_1525986016285_0193 di Spark streaming Yarn non è riuscita a causa di un errore:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Causa

Questo errore può essere causato dalla specifica di una versione del `spark-streaming-kafka` file jar diversa dalla versione del cluster Kafka in esecuzione.

Se ad esempio si esegue una versione del cluster Kafka 0.10.1, il comando seguente genererà un errore:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Risoluzione

Usare il comando Spark-Submit con l' `–packages` opzione e assicurarsi che la versione del file jar di Spark-streaming-Kafka corrisponda alla versione del cluster Kafka in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
