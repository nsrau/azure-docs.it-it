---
title: Errore IllegalArgumentException per Apache Spark-Azure HDInsight
description: IllegalArgumentException per l'attività Apache Spark in Azure HDInsight per Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 61190a932389b429040d6b643db2dd2732b2c41d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287907"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]