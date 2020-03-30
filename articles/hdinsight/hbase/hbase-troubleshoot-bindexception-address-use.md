---
title: BindException - Address already in use in Azure HDInsight
description: BindException - Address already in use in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887343"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindException - Address already in use in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

L'operazione di riavvio su un Server di area Apache HBase non viene completata. Dalla `region-server.log` directory `/var/log/hbase` in nei nodi di lavoro in cui si avvia il server di livello server di tipo non riesce, è possibile che venga visualizzato un messaggio di errore simile al seguente:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Riavvio dei server area Apache HBase durante un'intensa attività sul carico di lavoro. Di seguito è riportato ciò che accade dietro le quinte quando un utente avvia l'operazione di riavvio sul server dell'area HBase dall'interfaccia utente di Apache Ambari:

1. L'agente Ambari invia una richiesta di arresto al server di area.

1. L'agente Ambari attende 30 secondi per l'arresto normale del server di area

1. Se l'applicazione continua a connettersi al server di area, il server non viene arrestato immediatamente. Il timeout di 30 secondi scade prima dell'arresto.

1. Dopo 30 secondi, l'agente Ambari invia un comando di terminazione forzata (`kill -9`) al server di area.

1. A causa di questo arresto improvviso, anche se il processo del server di area viene `AddressBindException`interrotto, la porta associata al processo potrebbe non essere rilasciata, il che alla fine porta a .

## <a name="resolution"></a>Risoluzione

Ridurre il carico sui server dell'area HBase prima di iniziare un riavvio. È anche opportuno scaricare prima tutte le tabelle. Per informazioni su come scaricare le tabelle, vedere [HDInsight HBase: How to improve the Apache HBase cluster restart time by flushing tables](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HBase in HDInsight: come migliorare i tempi di riavvio del cluster Apache HBase scaricando le tabelle).

In alternativa, provare a riavviare manualmente i server di area nei nodi di lavoro utilizzando i seguenti comandi:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
