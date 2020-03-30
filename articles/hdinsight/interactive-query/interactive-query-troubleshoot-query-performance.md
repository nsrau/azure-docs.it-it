---
title: Riduzione delle prestazioni nelle query LLAP Apache Hive in Azure HDInsight
description: Le query in Apache Hive LLAP sono in esecuzione più lentamente del previsto in Azure HDInsight.Queries in Apache Hive LLAP are executs slower lower lowerer than expected in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895131"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: Poor performance in Apache Hive LLAP queries in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti di query interattivi nei cluster di Azure HDInsight.This article describes troubleshooting steps and possible resolutions for issues when using Interactive Query components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Le configurazioni cluster predefinite non sono sufficientemente sintonizzate per il carico di lavoro. Le query in Hive LLAP sono in esecuzione più lentamente del previsto.

## <a name="cause"></a>Causa

Questo può accadere a causa di una serie di motivi.

## <a name="resolution"></a>Risoluzione

LLAP è ottimizzato per le query che coinvolgono join e aggregazioni. Le query come le seguenti non funzionano bene in un cluster Hive interattivo:

```
select * from table where column = "columnvalue"
```

Per migliorare le prestazioni delle query punto in Hive LLAP, impostare le configurazioni seguenti:To improve point query performance in Hive LLAP, set the following configurations:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

È inoltre possibile aumentare l'utilizzo della cache LLAP per migliorare le prestazioni con la seguente modifica della configurazione:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
