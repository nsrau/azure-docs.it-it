---
title: BindException-indirizzo già in uso in Azure HDInsight
description: BindException-indirizzo già in uso in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947851"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindException-indirizzo già in uso in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile completare l'operazione di riavvio in un server di area di Apache HBase. Dalla directory `/var/log/hbase` nei nodi di lavoro in cui l'area del server di avvio non riesce, è possibile che venga visualizzato un messaggio di errore simile al seguente: `region-server.log`

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Riavvio dei server di area HBase durante un'intensa attività di carico di lavoro. Di seguito viene illustrata la situazione che si verifica quando un utente avvia l'operazione di riavvio sul server dell'area di HBase dall'interfaccia utente di Ambari:

1. L'agente Ambari invia una richiesta di arresto al server di area.

1. L'agente Ambari attende quindi 30 secondi per l'arresto normale del server di area.

1. Se l'applicazione continua a connettersi al server di area, non verrà arrestata immediatamente e pertanto il timeout di 30 secondi scadrà prima.

1. Dopo il termine di 30 secondi, l'agente Ambari invia un comando force kill (kill -9) al server di area.

1. A causa di questo arresto improvviso, anche se il processo del server di area viene terminato, la porta associata al processo potrebbe non essere rilasciata, che alla fine conduce a `AddressBindException`.

## <a name="resolution"></a>Risoluzione

Ridurre il carico sui server di area di HBase prima di avviare un riavvio.

In alternativa, provare a riavviare manualmente i server di area nei nodi di lavoro usando i comandi seguenti:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
