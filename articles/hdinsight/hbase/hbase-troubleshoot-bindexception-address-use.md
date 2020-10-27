---
title: BindException-indirizzo già in uso in Azure HDInsight
description: BindException-indirizzo già in uso in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 60f95292d517277f607c968629c55641e0eecce1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540262"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindingException-indirizzo già in uso in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile completare l'operazione di riavvio in un server di area di Apache HBase. Dalla `region-server.log` Directory nei `/var/log/hbase` nodi di lavoro in cui l'area del server di avvio non riesce, è possibile che venga visualizzato un messaggio di errore simile al seguente:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Riavvio dei server di area di Apache HBase durante un'intensa attività di carico di lavoro. Di seguito viene riportato il risultato che si verifica in background quando un utente avvia l'operazione di riavvio sul server dell'area di HBase dall'interfaccia utente di Apache Ambari:

1. L'agente Ambari invia una richiesta di arresto al server di area.

1. L'agente Ambari attende 30 secondi per l'arresto normale del server di area

1. Se l'applicazione continua a connettersi al server di area, il server non viene arrestato immediatamente. Il timeout di 30 secondi scade prima dell'arresto.

1. Dopo 30 secondi, l'agente Ambari invia un comando di terminazione forzata (`kill -9`) al server di area.

1. A causa di questo arresto improvviso, anche se il processo del server di area viene terminato, la porta associata al processo potrebbe non essere rilasciata, che alla fine conduce a `AddressBindException` .

## <a name="resolution"></a>Soluzione

Ridurre il carico sui server di area di HBase prima di avviare un riavvio. È anche opportuno scaricare prima tutte le tabelle. Per informazioni su come scaricare le tabelle, vedere [HDInsight HBase: How to improve the Apache HBase cluster restart time by flushing tables](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HBase in HDInsight: come migliorare i tempi di riavvio del cluster Apache HBase scaricando le tabelle).

In alternativa, provare a riavviare manualmente i server di area nei nodi di lavoro usando i comandi seguenti:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).