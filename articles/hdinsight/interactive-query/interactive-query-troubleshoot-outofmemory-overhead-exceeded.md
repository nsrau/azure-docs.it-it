---
title: Join in Apache Hive porta all'errore OutOfMemory - Azure HDInsightJoins in Apache Hive leads to OutOfMemory error - Azure HDInsight
description: Gestione degli errori OutOfMemory "Errore del limite di sovraccarico del GC ha superato"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895183"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenario: Joins in Apache Hive leads to an OutOfMemory error in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti di query interattivi nei cluster di Azure HDInsight.This article describes troubleshooting steps and possible resolutions for issues when using Interactive Query components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Il comportamento predefinito per I join Apache Hive consiste nel caricare l'intero contenuto di una tabella in memoria in modo che un join possa essere eseguito senza dover eseguire un passaggio Mappa/Riduci. Se la tabella Hive è troppo grande per essere contenuta nella memoria, la query può avere esito negativo.

## <a name="cause"></a>Causa

Quando si eseguono join in hive di dimensioni sufficienti, si verifica il seguente errore:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Risoluzione

Impedire a Hive di caricare tabelle in memoria nei join (invece di eseguire un passaggio Mappa/Riduci) impostando il seguente valore di configurazione Hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Passaggi successivi

Se l'impostazione di questo valore non risolve il problema, visita una delle seguenti...

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
