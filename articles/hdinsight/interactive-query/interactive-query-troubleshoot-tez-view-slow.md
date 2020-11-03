---
title: La visualizzazione Tez di Apache Ambari viene caricata lentamente in Azure HDInsight
description: La visualizzazione Tez di Apache Ambari può essere caricata lentamente o non può essere caricata in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288840"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: la visualizzazione Tez di Apache Ambari viene caricata lentamente in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

La visualizzazione Tez di Apache Ambari può essere caricata lentamente o non può essere caricata. Quando si carica la vista Ambari Tez, è possibile che i processi in nodi head non rispondono.

## <a name="cause"></a>Causa

L'accesso alle API di Yarn ATS può talvolta avere scarse prestazioni nei cluster creati prima del 2017 ottobre, quando il cluster ha un numero elevato di processi hive eseguito.

## <a name="resolution"></a>Soluzione

Si tratta di un problema che è stato risolto nel 2017 ottobre. Ricreando il cluster, il problema non verrà più riavviato.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]