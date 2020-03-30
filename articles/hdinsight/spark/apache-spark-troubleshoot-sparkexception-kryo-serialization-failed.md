---
title: Problemi con JDBC/ODBC & framework Apache Thrift - Azure HDInsight
description: Impossibile scaricare set di dati di grandi dimensioni utilizzando JDBC/ODBC e il framework software Apache Thrift in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894267"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Impossibile scaricare grandi set di dati utilizzando JDBC/ODBC e il framework software Apache Thrift in HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso dei componenti Apache Spark nei cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when using Apache Spark components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si tenta di scaricare set di dati di grandi dimensioni utilizzando JDBC/ODBC e il framework software Apache Thrift in Azure HDInsight, viene visualizzato un messaggio di errore simile al seguente:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Questa eccezione è causata dal processo di serializzazione che tenta di utilizzare più spazio nel buffer di quello consentito. In Spark 2.0.0, `org.apache.spark.serializer.KryoSerializer` la classe viene utilizzata per la serializzazione di oggetti quando si accede ai dati tramite il framework software Apache Thrift. Una classe diversa viene utilizzata per i dati che verranno inviati in rete o memorizzati nella cache in forma serializzata.

## <a name="resolution"></a>Risoluzione

Aumentare `Kryoserializer` il valore del buffer. Aggiungere una `spark.kryoserializer.buffer.max` chiave denominata `2048` e impostarla `Custom spark2-thrift-sparkconf`su in spark2 config in .

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
