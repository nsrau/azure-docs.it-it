---
title: 'Ottimizzare le prestazioni: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327920"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Ottimizzare le prestazioni: MapReduce, HDInsight & Azure Data Lake Storage Gen2

È opportuno conoscere i fattori da tenere in considerazione quando si ottimizzano le prestazioni di processi MapReduce. Questo articolo illustra una serie di linee guida per l'ottimizzazione delle prestazioni.

## <a name="prerequisites"></a>prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Azure Data Lake Storage Gen2**. Per istruzioni su come crearne uno, vedere [Guida introduttiva: creare un account di archiviazione Azure Data Lake storage Gen2](data-lake-storage-quickstart-create-account.md).
* Un **cluster Azure HDInsight** con accesso a un account Data Lake Storage Gen2. Vedere [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Uso di MapReduce in HDInsight**.  Per ulteriori informazioni, vedere [Usare MapReduce in Hadoop su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen2**.  Per informazioni sui concetti generali relativi alle prestazioni, vedere [Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md) (Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen2).

## <a name="parameters"></a>parametri

Vengono ora illustrati i parametri da configurare per aumentare le prestazioni in Data Lake Storage Gen2 durante l'esecuzione di processi MapReduce:

* **Mapreduce.map.memory.mb**: la quantità di memoria da allocare a ciascun mapper
* **Mapreduce.job.maps**: il numero di attività di mapping per processo
* **Mapreduce.reduce.memory.mb**: la quantità di memoria da allocare a ciascun riduttore
* **Mapreduce.job.reduces**: il numero di attività di riduzione per processo

**Mapreduce.map.memory / Mapreduce.reduce.memory**: numero da regolare in base alla quantità di memoria necessaria per le operazioni di mapping e/o riduzione.  È possibile visualizzare i valori predefiniti di mapreduce.map.memory e mapreduce.reduce.memory in Ambari tramite la configurazione Yarn.  In Ambari passare a YARN e visualizzare la scheda configs (configurazioni).  Verrà visualizzata la memoria YARN.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Ciò determinerà il numero massimo di mapper o riduttori da creare.  Il numero di suddivisioni determinerà la quantità di mapper creati per il processo MapReduce.  Pertanto, se ci sono meno suddivisioni rispetto ai mapper richiesti, si potrebbero ottenere meno mapper.       

## <a name="guidance"></a>Indicazioni

> [!NOTE]
> Le indicazioni fornite in questo documento presuppongono che l'applicazione sia l'unica in esecuzione nel cluster.

**Passaggio 1: determinare il numero di processi in esecuzione**

Per impostazione predefinita, MapReduce userà l'intero cluster per il processo.  È possibile usare solo parte del cluster usando un numero di mapper inferiore a quello dei contenitori disponibili.        

**Passaggio 2: impostare MapReduce. map. memory/MapReduce. reduce. memory**

Le dimensioni della memoria per le attività di mapping e riduzione dipenderanno dal processo specifico.  Se si desidera aumentare la concorrenza, è possibile ridurre le dimensioni della memoria.  Il numero di attività in esecuzione contemporaneamente dipende dal numero di contenitori.  Riducendo la quantità di memoria per ogni mapper o riduttore, verranno creati più contenitori, che permetteranno l'esecuzione simultanea di più mapper o riduttori.  Riducendo eccessivamente la quantità di memoria, potranno verificarsi degli errori di memoria insufficiente per alcuni procesis.  Se si verifica un errore di heap durante l'esecuzione del processo, è necessario aumentare la memoria per ogni mapper o riduttore.  Si noti che l'aggiunta di contenitori può comportare un carico extra per ciascuno di questi ultimi, con una potenziale diminuzione delle prestazioni.  In alternativa è possibile ottenere una maggiore quantità di memoria usando un cluster con maggiore memoria oppure aumentando il numero di nodi nel cluster.  Una maggiore quantità di memoria consentirà di usare più contenitori, ottenendo così più concorrenza.  

**Passaggio 3: determinare la memoria totale di YARN**

Per ottimizzare mapreduce.job.maps/mapreduce.job.reduces è necessario tenere presente la quantità di memoria totale di YARN disponibile all'uso.  Le informazioni sono disponibili in Ambari.  Passare a YARN e visualizzare la scheda configs (configurazioni).  La memoria YARN viene visualizzata in questa finestra.  Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per il numero di nodi nel cluster.

    Total YARN memory = nodes * YARN memory per node

Se si usa un cluster vuoto, la memoria può corrispondere alla memoria totale di YARN per il cluster.  Se altre applicazioni stanno usando memoria, è possibile scegliere di usare solo parte della memoria del cluster, riducendo e portando il numero dei mapper o riduttori al numero di contenitori da usare.  

**Passaggio 4: calcolare il numero di contenitori YARN**

I contenitori YARN indicano la quantità di concorrenza disponibile per il processo.  Prendere il valore della memoria totale di YARN e dividerlo per mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces**

Configurare mapreduce.job.maps/mapreduce.job.reduces come minimo in base al numero di contenitori disponibili.  È possibile sperimentare ulteriormente aumentando il numero di mapper e riduttori e verificare se così facendo si ottengono prestazioni migliori.  Tenere presente comunque che l'aggiunta di mapper comporterà un carico extra. Un numero eccessivo di mapper potrà causare una diminuzione nelle prestazioni.  

La pianificazione e l'isolamento della CPU sono disattivate per impostazione predefinita, pertanto il numero di contenitori YARN è limitato dalla memoria.

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster costituito da 8 nodi D14 e di voler eseguire un processo con attività di I/O intensive.  Di seguito sono riportati i calcoli che è necessario eseguire:

**Passaggio 1: determinare il numero di processi in esecuzione**

In questo esempio, si supponga che il processo sia l'unico processo in esecuzione.  

**Passaggio 2: impostare MapReduce. map. memory/MapReduce. reduce. memory**

In questo esempio, viene eseguito un processo con uso intensivo di I/O e viene deciso che saranno sufficienti 3GB di memoria per le attività di mapping.

    mapreduce.map.memory = 3GB

**Passaggio 3: determinare la memoria totale di YARN**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passaggio 4: calcolare il numero dei contenitori YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Esempi di esecuzione

Per illustrare la modalità di esecuzione di MapReduce in Data Lake Storage Gen2, di seguito viene riportato un codice di esempio eseguito in un cluster con le seguenti impostazioni:

* D14v2 a 16 nodi
* Cluster Hadoop con HDI 3.6 in esecuzione

Per iniziare, ecco alcuni comandi di esempio per eseguire MapReduce Teragen, Terasort e Teravalidate.  È possibile modificare questi comandi in base alle risorse.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
