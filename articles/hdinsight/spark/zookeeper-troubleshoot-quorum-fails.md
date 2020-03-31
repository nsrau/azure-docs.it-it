---
title: Il server Apache non riesce a formare un quorum in Azure HDInsight
description: Il server Apache non riesce a formare un quorum in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250229"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Il server Apache non riesce a formare un quorum in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Il server Apache zooKeeper non è integro, i sintomi possono includere: entrambi i resource `zkFailoverController` manager/nodi nome sono in modalità standby, le semplici operazioni HDFS non funzionano, viene interrotto e non può essere avviato, i processi Yarn/Spark/Livy non riescono a causa di errori dello zoo. Daemons LLAP potrebbero anche non riuscire ad avviarsi su cluster Secure Spark o Interactive Hive. È possibile che venga visualizzato un messaggio di errore analogo al:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

Nel server di protezione dello zoo accede a qualsiasi host di lo zoo di\*/var/log/zookeeper/zookeeper-zookeeper-server-out, è inoltre possibile che venga visualizzato il seguente errore:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Causa

Quando il volume dei file di snapshot è di grandi dimensioni o i file di snapshot sono danneggiati, il server di  non riuscirà a formare un quorum, il che causa l'instazione dei servizi correlati a .ooKeeper. Il server zooKeeper non rimuoverà i vecchi file di snapshot dalla sua directory di dati, ma è un'attività periodica che deve essere eseguita dagli utenti per mantenere la salubrità di .ooKeeper. Per ulteriori informazioni, consultate [Punti di forza e limitazioni dello zoo.](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)

## <a name="resolution"></a>Risoluzione

Controllare la directory `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2` dei dati di e verificare se le dimensioni del file delle istantanee sono grandi. Se esistono snapshot di grandi dimensioni, eseguire le operazioni seguenti:

1. Eseguire il backup `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`delle istantanee in e .

1. Pulire le istantanee in `/hadoop/zookeeper/version-2` e `/hadoop/hdinsight-zookeeper/version-2`.

1. Riavviare tutti i server zooKeeper dall'interfaccia utente di Apache Ambari.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

- Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

- Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
