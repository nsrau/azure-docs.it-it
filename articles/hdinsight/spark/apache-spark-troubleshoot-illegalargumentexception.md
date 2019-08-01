---
title: IllegalArgumentException per l'attività Apache Spark in Azure HDInsight
description: IllegalArgumentException per l'attività Apache Spark in Azure HDInsight per Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 54e07a840aac1e754db68d9a14403750757f4bcf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620844"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenario: IllegalArgumentException per l'attività Apache Spark in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si tenta di eseguire un'attività Spark in una pipeline Azure Data Factory si riceve l'eccezione seguente:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Causa

Un processo Spark avrà esito negativo se il file jar dell'applicazione non si trova nell'archiviazione predefinita o primaria del cluster Spark.

Si tratta di un problema noto con il framework open source Spark rilevato in questo bug: Il [processo Spark ha esito negativo se FS. defaultFS e il file jar dell'applicazione sono URL diversi](https://issues.apache.org/jira/browse/SPARK-22587).

Questo problema è stato risolto in Spark 2.3.0.

## <a name="resolution"></a>Risoluzione

Verificare che il file jar dell'applicazione sia archiviato nell'archivio predefinito/primario per il cluster HDInsight. In caso di Azure Data Factory, assicurarsi che il servizio collegato di ADF faccia riferimento al contenitore predefinito HDInsight anziché a un contenitore secondario.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
