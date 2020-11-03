---
title: Scarse prestazioni in Apache Hive query LLAP in Azure HDInsight
description: Le query in Apache Hive LLAP sono in esecuzione più lentamente del previsto in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288885"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: scarse prestazioni in Apache Hive query LLAP in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Le configurazioni del cluster predefinite non sono sufficientemente ottimizzate per il carico di lavoro. Le query in hive LLAP sono in esecuzione più lente del previsto.

## <a name="cause"></a>Causa

Questo problema può verificarsi a causa di diversi motivi.

## <a name="resolution"></a>Soluzione

LLAP è ottimizzato per le query che coinvolgono join e aggregazioni. Le query come le seguenti non vengono eseguite correttamente in un cluster Interactive hive:

```
select * from table where column = "columnvalue"
```

Per migliorare le prestazioni delle query del punto in hive LLAP, impostare le configurazioni seguenti:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

È anche possibile aumentare l'utilizzo della cache LLAP per migliorare le prestazioni con la modifica della configurazione seguente:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]