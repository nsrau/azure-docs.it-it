---
title: RpcTimeoutException per Apache Spark parsimonioso-Azure HDInsight
description: Vengono visualizzati 502 errori durante l'elaborazione di set di dati di grandi dimensioni con Apache Spark server di risparmio
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: fdf8a6d36d6b9d112ef5b3ea50991ac7d98fcbae
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547266"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException per il server di Apache Spark parsimonia in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

L'applicazione Spark ha esito negativo con un' `org.apache.spark.rpc.RpcTimeoutException` eccezione e un messaggio: `Futures timed out` , come nell'esempio seguente:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError``overhead limit exceeded`gli errori e possono anche essere visualizzati in `sparkthriftdriver.log` come nell'esempio seguente:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Questi errori sono causati da risorse di memoria insufficienti durante l'elaborazione dei dati. Se il processo di Garbage Collection Java viene avviato, l'applicazione Spark potrebbe smettere di rispondere. Le query inizieranno a timeout e arresteranno l'elaborazione. L' `Futures timed out` errore indica un cluster sottoposto a gravi stress.

## <a name="resolution"></a>Soluzione

Aumentare le dimensioni del cluster aggiungendo più nodi di lavoro o aumentando la capacità di memoria dei nodi del cluster esistenti. È anche possibile modificare la pipeline di dati per ridurre la quantità di dati elaborati in una sola volta.

`spark.network.timeout`Controlla il timeout per tutte le connessioni di rete. L'aumento del timeout di rete potrebbe consentire più tempo per il completamento di alcune operazioni critiche, ma il problema non verrà risolto completamente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).