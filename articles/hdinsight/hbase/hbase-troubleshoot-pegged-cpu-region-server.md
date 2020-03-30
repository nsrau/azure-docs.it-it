---
title: CPU sottoposta a pegging nel cluster Apache HBase - Azure HDInsight
description: Risolvere i problemi relativi alla CPU ancorata nel server di area nel cluster Apache HBase in Azure HDInsightTroubleshoot pegged CPU on region server in Apache HBase cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887309"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: Pegged CPU on region server in Apache HBase cluster in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Il processo del server dell'area Apache HBase inizia a occupare una CPU vicina al 200%, causando l'attivazione degli avvisi sul processo e sul cluster HBase Master e il cluster non funzionerà a piena capacità.

## <a name="cause"></a>Causa

Se si esegue il cluster HBase v3.4, è possibile che sia stato riscontrato da un potenziale bug causato dall'aggiornamento di jdk alla versione 1.7.0_151. Il sintomo che vediamo è processo del server di regione inizia `top` a occupare vicino al 200% della CPU (per verificare che questo `ps -aux | grep` eseguire il comando; se c'è un processo che occupa vicino al 200% CPU ottenere il suo pid e verificare che sia processo server di regione eseguendo ).

## <a name="resolution"></a>Risoluzione

1. Installare jdk 1.8 in TUTTI i nodi del cluster come indicato di seguito:

    * Eseguire l'azione `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`di script . Assicurarsi di selezionare l'opzione per l'esecuzione su tutti i nodi.

    * In alternativa, è possibile accedere a ogni `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`singolo nodo ed eseguire il comando .

1. Vai a Ambari `https://<clusterdnsname>.azurehdinsight.net`UI - .

1. Passare a **HBase->Configs->Advanced->** `hbase-env configs` `JAVA_HOME` Advanced `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`e impostare la variabile su . Salvare la modifica di configurazione.

1. [Facoltativo ma consigliato] [Scaricare tutte le tabelle nel cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Dall'interfaccia utente di Ambari riavviare nuovamente tutti i servizi HBase che devono essere riavviati.

1. A seconda dei dati del cluster, potrebbero essere stati alcuni minuti fino a un'ora prima che il cluster raggiunga lo stato stabile. Il modo in cui si conferma che il cluster raggiunge lo stato stabile consiste nel controllare l'interfaccia utente HMaster (tutti i server di area devono essere attivi) da Ambari (aggiornamento) o da headnode eseguire Shell HBase e quindi eseguire il comando di stato.

Per verificare che l'aggiornamento sia stato eseguito correttamente, verificare che i processi HBase pertinenti vengano avviati utilizzando la versione java appropriata, ad esempio per il controllo del server dell'area come:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
