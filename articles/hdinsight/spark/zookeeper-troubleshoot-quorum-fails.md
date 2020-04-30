---
title: Apache ZooKeeper server non riesce a creare un quorum in Azure HDInsight
description: Apache ZooKeeper server non riesce a creare un quorum in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133280"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server non riesce a creare un quorum in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Apache ZooKeeper server non è integro, è possibile che si verifichino problemi: i nodi Resource Manager/nome sono in modalità standby, le semplici operazioni `zkFailoverController` di HDFS non funzionano, viene arrestato e non può essere avviato, i processi Yarn/Spark/Livio hanno esito negativo a causa di errori ZooKeeper. I daemon di LLAP potrebbero non essere avviati in cluster Spark o Interactive hive protetti. Potrebbe essere visualizzato un messaggio di errore simile al seguente:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

Nei log del server Zookeeper in qualsiasi host Zookeeper in/var/log/Zookeeper/Zookeeper-Zookeeper-server-\*. out, è possibile che venga visualizzato anche l'errore seguente:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Causa

Quando il volume dei file di snapshot è di grandi dimensioni o i file di snapshot sono danneggiati, il server ZooKeeper non riuscirà a formare un quorum, causando la mancata integrità dei servizi correlati a ZooKeeper. Il server ZooKeeper non rimuoverà i file di snapshot precedenti dalla relativa directory dati, bensì un'attività periodica da eseguire dagli utenti per mantenere la salubrità di ZooKeeper. Per altre informazioni, vedere [punti di forza e limitazioni di ZooKeeper](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Soluzione

Controllare la directory `/hadoop/zookeeper/version-2` dei dati `/hadoop/hdinsight-zookeeper/version-2` di ZooKeeper e per verificare se le dimensioni del file degli snapshot sono elevate. Se sono presenti snapshot di grandi dimensioni, seguire questa procedura:

1. Verificare lo stato di altri server ZooKeeper nello stesso quorum per assicurarsi che funzionino correttamente con il comando "`echo stat | nc {zk_host_ip} 2181 (or 2182)`".  

1. Accedere all'host ZooKeeper problematico, eseguire il backup degli snapshot e `/hadoop/zookeeper/version-2` dei `/hadoop/hdinsight-zookeeper/version-2`log delle transazioni in e quindi pulire i file nelle due directory. 

1. Riavviare il server ZooKeeper problematico in Ambari o nell'host ZooKeeper. Riavviare quindi il servizio con problemi.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

- Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

- Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
