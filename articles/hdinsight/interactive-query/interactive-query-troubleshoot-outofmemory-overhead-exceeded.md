---
title: Join in Apache Hive genera un errore OutOfMemory-Azure HDInsight
description: Gestione degli errori OutOfMemory "limite sovraccarico GC superato errore"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 182ca8243b2e6050a72c22f52b9fcd0d2cef37c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494221"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenario: i join in Apache Hive generano un errore OutOfMemory in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il comportamento predefinito per i join Apache Hive consiste nel caricare l'intero contenuto di una tabella in memoria, in modo da poter eseguire un join senza dover eseguire un passaggio di mapping/riduzione. Se la tabella hive è troppo grande per adattarsi alla memoria, la query potrebbe non riuscire.

## <a name="cause"></a>Causa

Quando si eseguono join in hive di dimensioni sufficienti, viene visualizzato l'errore seguente:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Risoluzione

Impedire a hive di caricare le tabelle in memoria nei join, eseguendo invece un passaggio di mapping/riduzione, impostando il valore di configurazione hive seguente:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Passaggi successivi

Se l'impostazione di questo valore non ha risolto il problema, visitare uno dei seguenti...

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* È possibile connettersi con [@AzureSupport](https://twitter.com/azuresupport) , l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedi [Come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la fatturazione e la gestione della sottoscrizione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
