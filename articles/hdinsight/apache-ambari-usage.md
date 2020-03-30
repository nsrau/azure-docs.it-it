---
title: Apache Ambari usage in Azure HDInsight
description: Discussione sull'utilizzo di Apache Ambari in Azure HDInsight.Discussion of how Apache Ambari is used in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067396"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari usage in Azure HDInsight

HDInsight usa Apache Ambari per la distribuzione e la gestione dei cluster. Gli agenti Ambari vengono eseguiti su ogni nodo (nodo testa, nodo di lavoro, gestore dello zoo e edgenode, se esistente). Il server Ambari viene eseguito solo su headnode (hn0 o hn1). Verrà eseguita una sola istanza del server Ambari alla volta. Questo è controllato dal controller di failover HDInsight.This is controlled by HDInsight failover controller. Quando uno dei nodi head è inattivo per il riavvio o la manutenzione, l'altro headnode diventerà attivo e il server Ambari sul secondo nodo head verrà avviato.

Tutta la configurazione del cluster deve essere eseguita tramite [l'interfaccia utente di Ambari](./hdinsight-hadoop-manage-ambari.md), qualsiasi modifica locale verrà sovrascritta al riavvio del nodo.

## <a name="failover-controller-services"></a>Servizi del controller di failover

Il controller di failover HDInsight è anche responsabile dell'aggiornamento dell'indirizzo IP dell'host headnode, che punta al nodo head attivo corrente. Tutti gli agenti Ambari sono configurati per segnalare lo stato e l'heartbeat all'host headnode. Il controller di failover è un set di servizi in esecuzione su ogni nodo del cluster, se non sono in esecuzione, il failover headnode potrebbe non funzionare correttamente e si otterrà HTTP 502 quando si tenta di accedere al server Ambari.

Per verificare quale headnode è attivo, un modo è quello di `ping headnodehost` ssh a uno dei nodi nel cluster, quindi eseguire e confrontare l'IP con quello dei due headnode.

Se i servizi del controller di failover non sono in esecuzione, il failover headnode potrebbe non essere eseguito correttamente, il che potrebbe non essere in esecuzione con il server Ambari. Per verificare se i servizi del controller di failover sono in esecuzione, eseguire:To check if failover controller services are running, execute:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Log

Sul nodo head attivo, è possibile controllare i registri del server Ambari all'indirizzo:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

In qualsiasi nodo del cluster, è possibile controllare i registri dell'agente Ambari in:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sequenze di avvio del servizioService start sequences

Questa è la sequenza di avvio del servizio all'avvio:

1. L'agente Hdinsight avvia i servizi del controller di failover.
1. I servizi del controller di failover avviano l'agente Ambari su ogni nodo e il server Ambari sul nodo head attivo.

## <a name="ambari-database"></a>Banca dati Ambari

HDInsight crea il database di SQL Azure sotto il cofano da utilizzare come database per il server Ambari. Il livello di servizio predefinito [è S0](../sql-database/sql-database-elastic-pool-scale.md).

Per qualsiasi cluster con numero di nodi di lavoro maggiore di 16 durante la creazione del cluster, S2 è il livello di servizio del database.

## <a name="takeaway-points"></a>Punti da asporto

Non avviare/arrestare mai manualmente i servizi ambari-server o ambari-agent, a meno che non si stia tentando di riavviare il servizio per risolvere un problema. Per forzare un failover, è possibile riavviare il nodo head attivo.

Non modificare mai manualmente i file di configurazione in alcun nodo del cluster, lasciare che Ambari UI faccia il lavoro per te.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
