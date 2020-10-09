---
title: Errore IllegalArgumentException per Apache Spark-Azure HDInsight
description: IllegalArgumentException per l'attività Apache Spark in Azure HDInsight per Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894388"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenario: IllegalArgumentException per l'attività Apache Spark in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Quando si tenta di eseguire un'attività Spark in una pipeline Azure Data Factory si riceve l'eccezione seguente:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Causa

Un processo Spark avrà esito negativo se il file jar dell'applicazione non si trova nell'archiviazione predefinita o primaria del cluster Spark.

Si tratta di un problema noto con il framework open source Spark rilevato in questo bug: il [processo Spark ha esito negativo se FS. defaultFS e il file jar dell'applicazione sono URL diversi](https://issues.apache.org/jira/browse/SPARK-22587).

Questo problema è stato risolto in Spark 2.3.0.

## <a name="resolution"></a>Soluzione

Verificare che il file jar dell'applicazione sia archiviato nell'archivio predefinito/primario per il cluster HDInsight. In caso di Azure Data Factory, assicurarsi che il servizio collegato di ADF faccia riferimento al contenitore predefinito HDInsight anziché a un contenitore secondario.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
