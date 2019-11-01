---
title: Problemi con JDBC/ODBC & Apache Thrifty Framework-Azure HDInsight
description: Non è possibile scaricare set di dati di grandi dimensioni con JDBC/ODBC e Apache Thrifty Software Framework in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 7423687cbca069792a584277699c6bf8ce904d5d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241749"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Non è possibile scaricare set di dati di grandi dimensioni con JDBC/ODBC e Apache Thrifty Software Framework in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si prova a scaricare set di dati di grandi dimensioni con JDBC/ODBC e Apache Thrifty Software Framework in Azure HDInsight, viene visualizzato un messaggio di errore simile al seguente:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Questa eccezione è causata dal processo di serializzazione che tenta di utilizzare più spazio del buffer rispetto a quello consentito. In Spark 2.0.0 la classe `org.apache.spark.serializer.KryoSerializer` viene usata per la serializzazione di oggetti quando si accede ai dati tramite il Framework software Apache Thrifty. Viene utilizzata una classe diversa per i dati che verranno inviati in rete o memorizzati nella cache in forma serializzata.

## <a name="resolution"></a>Risoluzione

Aumentare il valore del buffer `Kryoserializer`. Aggiungere una chiave denominata `spark.kryoserializer.buffer.max` e impostarla su `2048` nella configurazione di spark2 in `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* È possibile connettersi con [@AzureSupport](https://twitter.com/azuresupport) , l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
