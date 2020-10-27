---
title: Timeout visualizzazione Apache Hive dal risultato della query-Azure HDInsight
description: Timeout della visualizzazione Apache Hive durante il recupero dei risultati di una query in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2ed60dc6404d16e5d7df302a0cc255e18f1f5b34
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534652"
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

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).