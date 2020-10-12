---
title: Utilizzo di Apache Ambari in Azure HDInsight
description: Discussione sull'uso di Apache Ambari in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: fe7d6d4e70bc55a6a91d3c1a1b910db4b5469fe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84197083"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Utilizzo di Apache Ambari in Azure HDInsight

HDInsight usa Apache Ambari per la distribuzione e la gestione dei cluster. Gli agenti Ambari vengono eseguiti in ogni nodo (nodo Head, nodo del ruolo di lavoro, Zookeeper e nodo perimetrale se esistente). Il server Ambari viene eseguito solo in nodo Head (hn0 o HN1). Viene eseguita una sola istanza del server Ambari alla volta. Questa operazione è controllata dal controller di failover di HDInsight. Quando uno dei nodi head è inattivo per il riavvio o la manutenzione, le altre nodo head diventeranno attive e il server Ambari nel secondo nodo head verrà avviato.

Tutte le configurazioni del cluster devono essere eseguite tramite l' [interfaccia utente di Ambari](./hdinsight-hadoop-manage-ambari.md). eventuali modifiche locali verranno sovrascritte al riavvio del nodo.

## <a name="failover-controller-services"></a>Servizi del controller di failover

Il controller di failover di HDInsight è anche responsabile dell'aggiornamento dell'indirizzo IP dell'host nodo Head, che fa riferimento al nodo head attivo corrente. Tutti gli agenti Ambari sono configurati per segnalare lo stato e l'heartbeat all'host nodo head. Il controller di failover è un set di servizi in esecuzione in ogni nodo del cluster, se non sono in esecuzione, il failover di nodo head potrebbe non funzionare correttamente e si otterrà HTTP 502 quando si tenta di accedere al server di Ambari.

Per verificare quale nodo Head è attivo, è possibile eseguire ssh in uno dei nodi del cluster, quindi eseguire `ping headnodehost` e confrontare l'IP con quello delle due nodi head.

Se i servizi del controller di failover non sono in esecuzione, il failover di nodo head potrebbe non essere eseguito correttamente, il che potrebbe non essere in esecuzione nel server Ambari. Per verificare se i servizi del controller di failover sono in esecuzione, eseguire:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Log

Nel nodo head attivo è possibile controllare i log del server Ambari all'indirizzo:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

In tutti i nodi del cluster è possibile controllare i log dell'agente Ambari all'indirizzo:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sequenze di avvio del servizio

Questa è la sequenza di avvio del servizio al momento dell'avvio:

1. HDInsight-Agent avvia i servizi del controller di failover.
1. I servizi del controller di failover avviano l'agente Ambari in ogni nodo e server Ambari in nodo head attivo.

## <a name="ambari-database"></a>Database Ambari

HDInsight crea un database nel database SQL sotto la cappa per fungere da database per il server Ambari. Il [livello di servizio predefinito è S0](../azure-sql/database/elastic-pool-scale.md).

Per qualsiasi cluster con numero di nodi di lavoro maggiore di 16 quando si crea il cluster, S2 è il livello di servizio del database.

## <a name="takeaway-points"></a>Punti da asporto

Non eseguire mai manualmente i servizi Ambari-server o Ambari-Agent, a meno che non si stia tentando di riavviare il servizio per risolvere un problema. Per forzare un failover, è possibile riavviare il nodo head attivo.

Non modificare mai manualmente i file di configurazione in qualsiasi nodo del cluster, quindi consentire all'interfaccia utente di Ambari di eseguire il processo.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
