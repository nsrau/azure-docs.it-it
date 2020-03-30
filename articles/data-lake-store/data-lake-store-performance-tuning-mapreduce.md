---
title: Azure Data Lake Storage Gen1 performance tuning - MapReduce
description: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Storage Gen1
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904582"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Linee guida per l'ottimizzazione delle prestazioni di MapReduce in HDInsight e Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure.** Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere Introduzione a [Azure Data Lake Storage Gen1For](data-lake-store-get-started-portal.md) instructions on how to create one, see Get started with Azure Data Lake Storage Gen1
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Utilizzo di MapReduce su HDInsight**. Per ulteriori informazioni, vedere [Usare MapReduce in Hadoop su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Esaminare le linee guida per l'ottimizzazione delle prestazioni per Data Lake Storage Gen1.** Per informazioni generali sulle prestazioni, vedere Indicazioni per l'ottimizzazione delle prestazioni di Data Lake Storage Gen1ForFor general performance concepts, see [Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametri

Vengono ora illustrati i parametri più importanti da configurare per aumentare le prestazioni in Data Lake Storage Gen1 durante l'esecuzione di processi MapReduce:

|Parametro      | Descrizione  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Quantità di memoria da allocare a ogni mappatore.  |
|`Mapreduce.job.maps`     |  Numero di attività di mappa per processo.  |
|`Mapreduce.reduce.memory.mb`     |  Quantità di memoria da allocare a ogni riduttore.  |
|`Mapreduce.job.reduces`    |   Numero di attività di riduzione per processo.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Regolare questo numero in base alla quantità di memoria necessaria per la mappa e/o ridurre l'attività. È possibile visualizzare i `mapreduce.map.memory` `mapreduce.reduce.memory` valori predefiniti di e in Ambari tramite la configurazione Yarn. In Ambari, accedere a YARN e visualizzare la scheda **Configs (Configurazioni).** Verrà visualizzata la memoria YARN.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Questo determina il numero massimo di mappatori o riduttori da creare. Il numero di divisioni determina il numero di mappatori creati per il processo MapReduce. Pertanto, è possibile ottenere un numero inferiore di mappatori di quelli richiesti se sono presenti meno divisioni rispetto al numero di mappatori richiesti.

## <a name="guidance"></a>Materiale sussidiario

### <a name="step-1-determine-number-of-jobs-running"></a>Passaggio 1: Determinare il numero di processi in esecuzioneStep 1: Determine number of jobs running

Per impostazione predefinita, MapReduce userà l'intero cluster per il processo. È possibile utilizzare meno del cluster utilizzando un numero di mappatori inferiore a quello dei contenitori disponibili. Le indicazioni fornite in questo documento presuppongono che l'applicazione sia l'unica in esecuzione nel cluster.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passaggio 2: Impostare mapreduce.map.memory/mapreduce.reduce.memory

Le dimensioni della memoria per le attività di mapping e riduzione dipenderanno dal processo specifico. Se si desidera aumentare la concorrenza, è possibile ridurre le dimensioni della memoria. Il numero di attività in esecuzione contemporaneamente dipende dal numero di contenitori. Riducendo la quantità di memoria per ogni mapper o riduttore, verranno creati più contenitori, che permetteranno l'esecuzione simultanea di più mapper o riduttori. Riducendo eccessivamente la quantità di memoria, potranno verificarsi degli errori di memoria insufficiente per alcuni procesis. Se viene visualizzato un errore di heap durante l'esecuzione del processo, aumentare la memoria per mapper o riduttore. Si consideri che l'aggiunta di più contenitori aggiunge un sovraccarico aggiuntivo per ogni contenitore aggiuntivo, che può potenzialmente ridurre le prestazioni. In alternativa è possibile ottenere una maggiore quantità di memoria usando un cluster con maggiore memoria oppure aumentando il numero di nodi nel cluster. Una maggiore quantità di memoria consentirà di usare più contenitori, ottenendo così più concorrenza.

### <a name="step-3-determine-total-yarn-memory"></a>Passaggio 3: Determinare la memoria YARN totaleStep 3: Determine total YARN memory

Per ottimizzare mapreduce.job.maps/mapreduce.job.reduces, considerare la quantità totale di memoria YARN disponibile per l'utilizzo. Le informazioni sono disponibili in Ambari. Passare a YARN e visualizzare la scheda **Configs (Configurazioni).** In questa finestra viene visualizzata la memoria YARN. Moltiplicare la memoria YARN con il numero di nodi nel cluster per ottenere la memoria YARN totale.

`Total YARN memory = nodes * YARN memory per node`

Se si utilizza un cluster vuoto, la memoria può essere la memoria YARN totale per il cluster. Se altre applicazioni stanno usando memoria, è possibile scegliere di usare solo parte della memoria del cluster, riducendo e portando il numero dei mapper o riduttori al numero di contenitori da usare.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Passaggio 4: Calcolare il numero di contenitori YARNStep 4: Calculate number of YARN containers

I contenitori YARN indicano la quantità di concorrenza disponibile per il processo. Prendere il valore della memoria totale di YARN e dividerlo per mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces

Configurare mapreduce.job.maps/mapreduce.job.reduces come minimo in base al numero di contenitori disponibili. È possibile sperimentare ulteriormente aumentando il numero di mapper e riduttori e verificare se così facendo si ottengono prestazioni migliori. Tenere presente comunque che l'aggiunta di mapper comporterà un carico extra. Un numero eccessivo di mapper potrà causare una diminuzione nelle prestazioni.

La pianificazione e l'isolamento della CPU sono disattivate per impostazione predefinita, pertanto il numero di contenitori YARN è limitato dalla memoria.

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster costituito da 8 nodi D14 e di voler eseguire un processo con attività di I/O intensive. Di seguito sono riportati i calcoli che è necessario eseguire:

### <a name="step-1-determine-number-of-jobs-running"></a>Passaggio 1: Determinare il numero di processi in esecuzioneStep 1: Determine number of jobs running

Per il nostro esempio, supponiamo che il nostro lavoro sia l'unico in esecuzione.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passaggio 2: Impostare mapreduce.map.memory/mapreduce.reduce.memory

Per il nostro esempio, si esegue un processo con utilizzo intensivo di I/O e si decide che sono sufficienti 3 GB di memoria per le attività mappa.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Passaggio 3: Determinare la memoria YARN totaleStep 3: Determine total YARN memory

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Passaggio 4: calcolare il numero dei contenitori YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Limitazioni

**Limitazione della larghezza di banda della rete di Data Lake Storage Gen1**

Come servizio multi-tenant, Data Lake Storage Gen1 imposta dei limiti di larghezza di banda a livello di account. Se si raggiungono tali limiti, si inizieranno a riscontrare esiti negativi nelle attività. Ciò può essere constatato verificando la presenza di errori di limitazione nei log delle attività. Se occorre ulteriore larghezza di banda per il processo, contattare Microsoft.

Per verificare la presenza di limitazioni, è necessario abilitare la registrazione di debug sul lato client. Di seguito viene indicato come procedere:

1. Inserire la seguente proprietà nelle proprietà log4j in Ambari > YARN > Config (Configurazione) > Advanced yarn log4j (YARN log4j avanzate): log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Riavviare tutti i nodi o servizi per rendere effettiva la nuova configurazione.

3. Se si sta ottenendo limitato, vedrete il codice di errore HTTP 429 nel file di registro YARN. Il file di log di YARN si trova nel percorso /tmp/&lt;user&gt;/yarn.log

## <a name="examples-to-run"></a>Esempi di esecuzione

Per illustrare l'esecuzione di MapReduce in Data Lake Storage Gen1, di seguito è riportato codice di esempio che è stato eseguito in un cluster con le impostazioni seguenti:To demonstrate how MapReduce runs on Data Lake Storage Gen1, the following is some sample code that was run on a cluster with the following settings:

* D14v2 a 16 nodi
* Cluster Hadoop con HDI 3.6 in esecuzione

Per iniziare, ecco alcuni comandi di esempio per eseguire MapReduce Teragen, Terasort e Teravalidate. È possibile modificare questi comandi in base alle risorse.

### <a name="teragen"></a>Terageno

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
