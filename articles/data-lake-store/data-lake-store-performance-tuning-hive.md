---
title: Linee guida per l'ottimizzazione delle prestazioni di Hive in Azure Data Lake Store | Microsoft Docs
description: Linee guida per l'ottimizzazione delle prestazioni di Hive in Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: e10bf8f7cbae2b81d22823ff74fe652c6bcb2da3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Linee guida per l'ottimizzazione delle prestazioni di Hive in HDInsight e di Azure Data Lake Store

Le impostazioni predefinite sono state impostate per fornire buone prestazioni per molti casi d'uso diversi.  Per le query con attività di I/O intensive, è possibile regolare Hive per ottenere prestazioni migliori con ADLS.  

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Esecuzione di Hive in HDInsight**.  Per ulteriori informazioni sull'esecuzione di processi Hive in HDInsight, vedere [Usare Hive in HDInsight] (https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-hive)
* **Linee guida per l'ottimizzazione delle prestazioni in ADLS**.  Per i concetti generali relativi alle prestazioni, vedere [Linee guida per l'ottimizzazione delle prestazioni in Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>parameters

Di seguito vengono elencate le impostazioni più importanti da regolare per ottenere prestazioni migliori in ADLS:

* **hive.tez.container.size**: la quantità di memoria usata da ciascuna attività

* **tez.grouping.min-size**: la dimensione minima di ciascun mapper

* **tez.grouping.max-size**: la dimensione massima di ciascun mapper

* **hive.exec.reducer.bytes.per.reducer**: la dimensione di ciascun riduttore

**hive.tez.container.size**: la dimensione del contenitore determina la quantità di memoria disponibile per ciascuna attività.  Questo è l'input principale per il controllo di concorrenza in Hive.  

**tez.grouping.min-size**: questo parametro consente di impostare la dimensione minima di ciascun mapper.  Se il numero di mapper scelti da Tez è inferiore al valore di questo parametro, Tez userà il valore impostato qui.  

**tez.grouping.max-size**: questo parametro consente di impostare la dimensione massima di ciascun mapper.  Se il numero di mapper scelti da Tez è superiore al valore di questo parametro, Tez userà il valore impostato qui.  

**hive.exec.reducer.bytes.per.reducer**: questo parametro consente di impostare la dimensione di ciascun riduttore.  Per impostazione predefinita, ogni riduttore ha una dimensione di 256 MB.  

## <a name="guidance"></a>Indicazioni

**Set hive.exec.reducer.bytes.per.reducer**: il valore predefinito funziona bene quando i dati non sono compressi.  In caso di dati compressi, è consigliabile ridurre la dimensione del riduttore.  

**Set hive.tez.container.size**: in ciascun nodo, la memoria viene specificata da yarn.nodemanager.resource.memory-mb e dovrebbe essere impostata correttamente nel cluster HDI per impostazione predefinita.  Per altre informazioni sull'impostazione della memoria appropriata in YARN, vedere questo [post](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I carichi di lavoro con numerose operazioni di I/O possono trarre vantaggi da un maggiore parallelismo riducendo la dimensione del contenitore Tez. In questo modo l'utente dispone di più contenitori e, quindi, di più concorrenza.  Tuttavia, alcune query di Hive richiedono una notevole quantità di memoria (ad esempio MapJoin).  Se l'attività non dispone di sufficiente memoria, si otterrà un'eccezione di memoria insufficiente durante la fase di runtime.  Se si ricevono eccezioni di memoria insufficiente, è necessario aumentare la memoria.   

Il numero di attività simultanee in esecuzione o il parallelismo verrà ristretto dalla memoria totale di YARN.  Il numero di contenitori YARN determinerà il numero di attività che è possibile eseguire contemporaneamente.  Per trovare la memoria YARN per ogni nodo è possibile passare ad Ambari.  Passare a YARN e visualizzare la scheda Configs (Configurazioni).  La memoria YARN è visualizzata in questa finestra.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
La chiave per migliorare le prestazioni usando ADLS consiste nell'aumentare il più possibile la concorrenza.  Tez calcola automaticamente il numero di attività da creare, in modo che non debba essere impostato manualmente.   

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster D14 a 8 nodi.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitazioni
**Limitazione ADLS** 

Se si raggiungono i limiti di larghezza di banda di ADLS, le attività inizieranno ad avere esiti negativi. Ciò può essere constatato verificando la presenza di errori di limitazione nei log delle attività.  È possibile ridurre il parallelismo aumentando la dimensione del contenitore Tez.  Se occorre maggiore concorrenza per il processo, contattare Microsoft.   

Per verificare la presenza di limitazioni, è necessario abilitare la registrazione di debug sul lato client. Di seguito viene indicato come procedere:

1. Inserire la seguente proprietà nelle proprietà log4j nella configurazione di Hive. Questa operazione può essere eseguita dalla vista Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Riavviare tutti i nodi/service per rendere effettiva la nuova configurazione.

2. In caso di limitazioni, verrà visualizzato il codice di errore HTTP 429 nel file di log di Hive. Il file di log di Hive si trova nel percorso /tmp/&lt;user&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Ulteriori informazioni sull'ottimizzazione di Hive

Ecco alcuni articoli di blog che consentiranno di ottimizzare le query di Hive:
* [Ottimizzare le query Hive per Hadoop in HDInsight](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Risoluzione dei problemi di prestazioni delle query Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Discussione Ignite sull'ottimizzazione di Hive in HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
