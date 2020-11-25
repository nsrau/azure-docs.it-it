---
title: 'Ottimizzare le prestazioni: hive, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Informazioni sulle linee guida per l'ottimizzazione delle query con utilizzo intensivo di I/O con hive, HDInsight e Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912944"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Ottimizzare le prestazioni: hive, HDInsight & Azure Data Lake Storage Gen2

Le impostazioni predefinite sono state impostate per fornire buone prestazioni per molti casi d'uso diversi.  Per le query con attività di I/O intensive, è possibile regolare Hive per ottenere prestazioni migliori con Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Data Lake Storage Gen2**. Per istruzioni su come crearne uno, vedere [Guida introduttiva: creare un account di archiviazione Azure Data Lake storage Gen2](../common/storage-account-create.md)
* Un **cluster Azure HDInsight** con accesso a un account Data Lake Storage Gen2. Vedere [usare Azure Data Lake storage Gen2 con i cluster HDInsight di Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **Esecuzione di Hive in HDInsight**.  Per informazioni sull'esecuzione di processi Hive in HDInsight, vedere [Usare Hive in HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md)
* **Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen2**.  Per i concetti generali relativi alle prestazioni, vedere [Data Lake storage Gen2 linee guida per l'ottimizzazione delle prestazioni](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametri

Di seguito sono elencate le impostazioni più importanti da ottimizzare per ottenere prestazioni migliori in Data Lake Storage Gen2:

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

**Set hive.tez.container.size**: in ciascun nodo, la memoria viene specificata da yarn.nodemanager.resource.memory-mb e dovrebbe essere impostata correttamente nel cluster HDI per impostazione predefinita.  Per altre informazioni sull'impostazione della memoria appropriata in YARN, vedere questo [post](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

I carichi di lavoro con numerose operazioni di I/O possono trarre vantaggi da un maggiore parallelismo riducendo la dimensione del contenitore Tez. In questo modo l'utente dispone di più contenitori e, quindi, di più concorrenza.  Tuttavia, alcune query di Hive richiedono una notevole quantità di memoria (ad esempio MapJoin).  Se l'attività non dispone di sufficiente memoria, si otterrà un'eccezione di memoria insufficiente durante la fase di runtime.  Se si ricevono eccezioni di memoria insufficiente, è necessario aumentare la memoria.   

Il numero di attività simultanee in esecuzione o il parallelismo verrà ristretto dalla memoria totale di YARN.  Il numero di contenitori YARN determinerà il numero di attività che è possibile eseguire contemporaneamente.  Per trovare la memoria YARN per ogni nodo è possibile passare ad Ambari.  Passare a YARN e visualizzare la scheda configs (configurazioni).  La memoria YARN viene visualizzata in questa finestra.  

- Memoria totale YARN = nodi * memoria YARN per nodo
- \# di contenitori YARN = totale memoria YARN/dimensioni del contenitore Tez

La chiave per migliorare le prestazioni usando Data Lake Storage Gen2 consiste nell'aumentare il più possibile la concorrenza.  Tez calcola automaticamente il numero di attività da creare, in modo che non debba essere impostato manualmente.   

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster D14 a 8 nodi.  

- Memoria totale YARN = nodi * memoria YARN per nodo
- Memoria totale YARN = 8 nodi * 96 GB = 768GB
- \# dei contenitori YARN = 768GB/3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Ulteriori informazioni sull'ottimizzazione di Hive

Ecco alcuni articoli di blog che consentiranno di ottimizzare le query di Hive:
* [Ottimizzare le query Hive per Hadoop in HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Ottimizzare le query Apache Hive in Azure HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Discussione Ignite sull'ottimizzazione di Hive in HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)