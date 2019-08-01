---
title: RpcTimeoutException per il server Apache Spark Thrifty in Azure HDInsight
description: Vengono visualizzati 502 errori durante l'elaborazione di set di dati di grandi dimensioni con Apache Spark server di risparmio
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 0d4f2f9c8392e5b81f5abb8c67548baefae611c9
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620831"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException per il server Apache Spark Thrifty in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

L'applicazione Spark ha esito negativo con un' `org.apache.spark.rpc.RpcTimeoutException` eccezione e un messaggio: `Futures timed out`, come nell'esempio seguente:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError``overhead limit exceeded` gli`sparkthriftdriver.log` errori e possono anche essere visualizzati in come nell'esempio seguente:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Questi errori sono causati da risorse di memoria insufficienti durante l'elaborazione dei dati. Se il processo di Garbage Collection Java viene avviato, può causare l'impiccagione dell'applicazione Spark. Le query inizieranno a timeout e arresteranno l'elaborazione. L' `Futures timed out` errore indica un cluster sottoposto a gravi stress.

## <a name="resolution"></a>Risoluzione

Aumentare le dimensioni del cluster aggiungendo più nodi di lavoro o aumentando la capacità di memoria dei nodi del cluster esistenti. È anche possibile modificare la pipeline di dati per ridurre la quantità di dati elaborati in una sola volta.

`spark.network.timeout` Controlla il timeout per tutte le connessioni di rete. L'aumento del timeout di rete potrebbe consentire più tempo per il completamento di alcune operazioni critiche, ma il problema non verrà risolto completamente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
