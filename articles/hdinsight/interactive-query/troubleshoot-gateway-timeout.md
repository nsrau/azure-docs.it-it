---
title: Eccezione durante l'esecuzione di query dalla visualizzazione Apache Ambari Hive in Azure HDInsightException when running queries from Apache Ambari Hive View in Azure HDInsight
description: Passaggi di risoluzione dei problemi durante l'esecuzione di query Apache Hive tramite Apache Ambari Hive View in Azure HDInsight.Troubleshooting steps when running Apache Hive queries through Apache Ambari Hive View in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895040"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Eccezione durante l'esecuzione di query dalla visualizzazione Apache Ambari Hive in Azure HDInsightException when running queries from Apache Ambari Hive View in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si esegue una query Apache Hive da Apache Ambari Hive View, viene visualizzato il seguente messaggio di errore in modo discontinuo:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Causa

Timeout del gateway.

Il valore di timeout del gateway è 2 minuti. Le query dalla visualizzazione Hive Di `/hive2` Ambari vengono inviate all'endpoint tramite il gateway. Una volta che la query è stata compilata `queryid`e accettata correttamente, HiveServer restituisce un . I client continuano quindi il polling per lo stato della query. Durante questo processo, se HiveServer non restituisce una risposta HTTP entro 2 minuti, il gateway HDI genera un errore di timeout del gateway 502.3 al chiamante. Gli errori possono verificarsi quando la query viene inviata per l'elaborazione (più probabile) e anche nella chiamata di stato get (meno probabile). Gli utenti potevano vedere uno di loro.

Il thread del gestore http dovrebbe essere rapido: preparare il processo e restituire un `queryid`file . Tuttavia, a causa di diversi motivi, tutti i thread del gestore potrebbero essere occupati con conseguente timeout per le nuove query e le chiamate di stato get.

### <a name="responsibilities-of-the-http-handler-thread"></a>Responsabilità del thread del gestore HTTP

Quando il client invia una query a HiveServer, esegue le operazioni seguenti nel thread in primo piano:When the client submits a query to HiveServer, it does the following in the foreground thread:

* Analizzare la richiesta, eseguire la verifica semanticaParse the request, do semantic verification
* Acquisisci blocco
* Ricerca metastore se necessario
* Compilare la query (DDL o DML)Compile the query (DDL or DML)
* Preparare un piano di query
* Eseguire l'autorizzazione (Esegui tutti i criteri dei ranger applicabili nei cluster protetti)Perform authorization (Run all applicable ranger policies in secure clusters)

## <a name="resolution"></a>Risoluzione

Alcune raccomandazioni generali per migliorare la situazione:

* Se si usa un metastore dell'hive esterno, controllare le metriche del database e assicurarsi che il database non sia sovraccarico. Valutare la possibilità di ridimensionare il livello del database di metastore.

* Assicurarsi che le operazioni parallele siano attivate (in questo modo i thread del gestore HTTP vengono eseguiti in parallelo). Per verificare il valore, avviare [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e passare a **Hive** > **Configs** > **Advanced** > Custom**hive-site**. Il valore `hive.server2.parallel.ops.in.session` per `true`deve essere .

* Verificare che lo SKU della macchina virtuale del cluster non sia troppo piccolo per il carico. Valutare la possibilità di suddividere il lavoro tra più cluster. Per ulteriori informazioni, vedere [Scegliere un tipo di cluster](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Se Ranger è installato nel cluster, verificare se sono presenti troppi criteri Ranger che devono essere valutati per ogni query. Cercare criteri duplicati o non necessari.

* Controllare il valore **HiveServer2 Heap Size** da Ambari. Passare a **Hive** > **Configs** > **Settings** > **Optimization**. Assicurarsi che il valore sia maggiore di 10 GB. Regolare in base alle esigenze per ottimizzare le prestazioni.

* Verificare che la query Hive sia ben sintonizzata. Per altre informazioni, vedere [Ottimizzare le query Apache Hive in Azure HDInsight.For](../hdinsight-hadoop-optimize-hive-query.md)more information, see Optimize Apache Hive queries in Azure HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
