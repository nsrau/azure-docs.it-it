---
title: L'interfaccia utente di Apache Ambari Mostra gli host e i servizi in Azure HDInsight
description: Risoluzione dei problemi relativi a un problema dell'interfaccia utente di Apache Ambari quando vengono visualizzati gli host e i servizi in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895643"
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

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
