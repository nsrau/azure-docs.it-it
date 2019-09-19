---
title: L'interfaccia utente di Apache Ambari Mostra gli host e i servizi in Azure HDInsight
description: Risoluzione dei problemi relativi a un problema dell'interfaccia utente di Apache Ambari quando vengono visualizzati gli host e i servizi in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: eebf9a7c3df2e5956d7926cbdf93f473897dbf44
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087852"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: L'interfaccia utente di Apache Ambari Mostra gli host e i servizi in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

L'interfaccia utente di Apache Ambari è accessibile, ma l'interfaccia utente mostra che quasi tutti i servizi sono inattivi, tutti gli host che visualizzano heartbeat persi.

## <a name="cause"></a>Causa

Nella maggior parte degli scenari, si tratta di un problema relativo al server Ambari non in esecuzione nel nodo head attivo. Verificare quale nodo Head è il nodo head attivo e assicurarsi che il server Ambari venga eseguito su quello destro. Non avviare manualmente Ambari-server, lasciare che il servizio controller di failover sia responsabile dell'avvio di Ambari-server a destra nodo head. Riavviare il nodo head attivo per forzare un failover.

Problemi di rete possono anche causare questo problema. Da ogni nodo del cluster, vedere se è possibile `headnodehost`eseguire il ping. Si verifica una situazione rara in cui nessun nodo del cluster è `headnodehost`in grado di connettersi a:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Risoluzione

In genere, il riavvio del nodo head attivo ridurrà questo problema. In caso contrario, contattare il team di supporto di HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
