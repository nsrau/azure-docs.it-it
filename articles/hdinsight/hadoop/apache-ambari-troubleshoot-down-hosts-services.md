---
title: L'interfaccia utente di Apache Ambari Mostra gli host e i servizi in Azure HDInsight
description: Risoluzione dei problemi relativi a un problema dell'interfaccia utente di Apache Ambari quando vengono visualizzati gli host e i servizi in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 9c9676fa1457be014098b8190986c617262e628d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286502"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: l'interfaccia utente di Apache Ambari Visualizza gli host e i servizi in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

L'interfaccia utente di Apache Ambari è accessibile, ma l'interfaccia utente mostra che quasi tutti i servizi sono inattivi, tutti gli host che visualizzano heartbeat persi.

## <a name="cause"></a>Causa

Nella maggior parte degli scenari, si tratta di un problema relativo al server Ambari non in esecuzione nel nodo head attivo. Verificare quale nodo Head è il nodo head attivo e assicurarsi che il server Ambari venga eseguito su quello destro. Non avviare manualmente Ambari-server, lasciare che il servizio controller di failover sia responsabile dell'avvio di Ambari-server a destra nodo head. Riavviare il nodo head attivo per forzare un failover.

Problemi di rete possono anche causare questo problema. Da ogni nodo del cluster, vedere se è possibile eseguire il ping `headnodehost` . Si verifica una situazione rara in cui nessun nodo del cluster è in grado di connettersi a `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Soluzione

In genere, il riavvio del nodo head attivo ridurrà questo problema. In caso contrario, contattare il team di supporto di HDInsight.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]