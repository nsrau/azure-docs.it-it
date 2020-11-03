---
title: Timeout visualizzazione Apache Hive dal risultato della query-Azure HDInsight
description: Timeout della visualizzazione Apache Hive durante il recupero dei risultati di una query in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c642760a5f6bfa7e59d42237e1583617e322ece3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288827"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenario: timeout della visualizzazione Apache Hive durante il recupero dei risultati di una query in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si eseguono query specifiche dalla vista Apache Hive, è possibile che venga visualizzato l'errore seguente:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Causa

Il valore di timeout predefinito della visualizzazione hive potrebbe non essere adatto per la query in esecuzione. Il periodo di tempo specificato è troppo breve per la visualizzazione hive per recuperare il risultato della query.

## <a name="resolution"></a>Soluzione

Aumentare i timeout della visualizzazione hive di Apache Ambari impostando le proprietà seguenti in `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Il valore di `HIVE_VIEW_INSTANCE_NAME` è disponibile alla fine dell'URL della visualizzazione hive.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]