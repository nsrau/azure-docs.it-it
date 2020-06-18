---
title: Problemi con JDBC/ODBC e il framework Apache Thrift - Azure HDInsight
description: Non è possibile scaricare set di dati di grandi dimensioni con JDBC/ODBC e il framework software Apache Thrift in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: a8dcd6ae844810213ed6706002cdb9a31de94f60
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653593"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Non è possibile scaricare set di dati di grandi dimensioni con JDBC/ODBC e il framework software Apache Thrift in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Quando si prova a scaricare set di dati di grandi dimensioni con JDBC/ODBC e il framework software Apache Thrift in Azure HDInsight, viene visualizzato un messaggio di errore simile al seguente:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Questa eccezione è causata dal processo di serializzazione, che prova a usare più spazio del buffer di quello consentito. In Spark 2.0.0 la classe `org.apache.spark.serializer.KryoSerializer` viene usata per la serializzazione di oggetti quando si accede ai dati tramite il framework software Apache Thrift. Viene usata una classe diversa per i dati che verranno inviati in rete o memorizzati nella cache in forma serializzata.

## <a name="resolution"></a>Risoluzione

Aumentare il valore del buffer `Kryoserializer`. Aggiungere una chiave con nome `spark.kryoserializer.buffer.max` e impostarla su `2047` nella configurazione di spark2 in `Custom spark2-thrift-sparkconf`. Riavviare tutti i componenti interessati.

> [!IMPORTANT]
> Il valore di `spark.kryoserializer.buffer.max` deve essere minore di 2048. I valori frazionari non sono supportati.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
