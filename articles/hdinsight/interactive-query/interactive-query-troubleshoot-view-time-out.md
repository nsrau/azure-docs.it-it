---
title: Timeout della visualizzazione Apache Hive dal risultato della query - Azure HDInsightApache Hive View timeout from query result - Azure HDInsight
description: Apache Hive View times out when fetching a query result in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f00f70e674ac0b83b737d6b2a4bf9d20400736fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672022"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenario: Apache Hive View times out when fetching a query result in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti di query interattivi nei cluster di Azure HDInsight.This article describes troubleshooting steps and possible resolutions for issues when using Interactive Query components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si eseguono determinate query dalla visualizzazione Apache Hive, potrebbe verificarsi il seguente errore:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Causa

Il valore di timeout predefinito di Hive View potrebbe non essere adatto per la query in esecuzione. Il periodo di tempo specificato è troppo breve per la visualizzazione Hive per recuperare il risultato della query.

## <a name="resolution"></a>Risoluzione

Aumentare i timeout della vista Apache Ambari `/etc/ambari-server/conf/ambari.properties`Hive impostando le seguenti proprietà in .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Il valore `HIVE_VIEW_INSTANCE_NAME` di è disponibile alla fine dell'URL della visualizzazione Hive.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
