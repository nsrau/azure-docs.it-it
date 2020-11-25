---
title: CPU agganciata nel cluster Apache HBase-Azure HDInsight
description: Risolvere i problemi di CPU in un server di area nel cluster Apache HBase in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: bed73c3ccc7f514ffc9ff8f97534ae4b249834ce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017016"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: CPU ancorata sul server di area nel cluster Apache HBase in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il processo del server di area di Apache HBase inizia a occuparsi di quasi il 200% della CPU, causando l'attivazione degli avvisi in HBase Master processo e il cluster per non funzionare a piena capacità.

## <a name="cause"></a>Causa

Se si esegue il cluster HBase v 3.4, è possibile che sia stato raggiunto un bug potenziale causato dall'aggiornamento di JDK alla versione 1.7.0 _151. Il sintomo visualizzato è che il processo del server di area inizia a occuparsi di quasi il 200% della CPU (per verificare l'esecuzione del `top` comando; se è presente un processo che occupa quasi il 200% della CPU, ottenere il PID e confermare che si tratta di un processo del server di area eseguendo `ps -aux | grep` ).

## <a name="resolution"></a>Risoluzione

1. Installare JDK 1,8 in tutti i nodi del cluster come indicato di seguito:

    * Eseguire l'azione script `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Assicurarsi di selezionare l'opzione per l'esecuzione in tutti i nodi.

    * In alternativa, è possibile accedere a ogni singolo nodo ed eseguire il comando `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Passare all'interfaccia utente di Ambari- `https://<clusterdnsname>.azurehdinsight.net` .

1. Passare a **HBase->configs->Advanced->Advanced** `hbase-env configs` e modificare la variabile `JAVA_HOME` in `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Salvare la modifica della configurazione.

1. [Facoltativo ma consigliato] [Svuotare tutte le tabelle nel cluster](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Dall'interfaccia utente di Ambari, riavviare tutti i servizi HBase che richiedono il riavvio.

1. A seconda dei dati nel cluster, potrebbe essere necessario attendere qualche minuto prima che il cluster raggiunga lo stato stabile. Il modo in cui viene verificato che il cluster raggiunga lo stato stabile consiste nel controllare l'interfaccia utente di HMaster (tutti i server di area devono essere attivi) da Ambari (Aggiorna) o da nodo head eseguire HBase Shell e quindi eseguire il comando stato.

Per verificare che l'aggiornamento abbia avuto esito positivo, controllare che i processi HBase pertinenti vengano avviati usando la versione di Java appropriata, ad esempio per il controllo del server di area:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]