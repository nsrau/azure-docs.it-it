---
title: Scarse prestazioni in Apache Hive query LLAP in Azure HDInsight
description: Le query in Apache Hive LLAP sono in esecuzione più lentamente del previsto in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: d05febbe6ba9e30ef46f5e961af155d37c76c205
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811371"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: Scarse prestazioni in Apache Hive query LLAP in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Le configurazioni del cluster predefinite non sono sufficientemente ottimizzate per il carico di lavoro. Le query in hive LLAP sono in esecuzione più lente del previsto.

## <a name="cause"></a>Causa

Questo problema può verificarsi a causa di diversi motivi.

## <a name="resolution"></a>Risoluzione

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

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
