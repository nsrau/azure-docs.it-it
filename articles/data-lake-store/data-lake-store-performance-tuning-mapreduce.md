---
title: Ottimizzazione delle prestazioni Azure Data Lake Storage Gen1-MapReduce
description: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Storage Gen1
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904582"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Linee guida per l'ottimizzazione delle prestazioni di MapReduce in HDInsight e Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, consultare [Introduzione ad Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Consultare [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Uso di MapReduce in HDInsight**. Per ulteriori informazioni, vedere [Usare MapReduce in Hadoop su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Esaminare le linee guida per l'ottimizzazione delle prestazioni per data Lake storage Gen1**. Per informazioni sui concetti generali relativi alle prestazioni, vedere [Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>parametri

Vengono ora illustrati i parametri più importanti da configurare per aumentare le prestazioni in Data Lake Storage Gen1 durante l'esecuzione di processi MapReduce:

|.      | DESCRIZIONE  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Quantità di memoria da allocare a ogni Mapper.  |
|`Mapreduce.job.maps`     |  Numero di attività mappa per processo.  |
|`Mapreduce.reduce.memory.mb`     |  Quantità di memoria da allocare a ogni riduttore.  |
|`Mapreduce.job.reduces`    |   Numero di attività di riduzione per processo.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. memory/MapReduce. reduce. memory

Modificare questo numero in base alla quantità di memoria necessaria per l'attività di mapping e/o riduzione. È possibile visualizzare i valori predefiniti di `mapreduce.map.memory` e `mapreduce.reduce.memory` in Ambari tramite la configurazione Yarn. In Ambari passare a YARN e visualizzare la scheda **configs (configurazioni** ). Verrà visualizzata la memoria YARN.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. Maps/MapReduce. job. Reduces

Questo determina il numero massimo di Mapper o riduttori da creare. Il numero di divisioni determina il numero di Mapper creati per il processo MapReduce. Pertanto, è possibile ottenere un numero inferiore di Mapper rispetto a quello richiesto se sono presenti meno divisioni rispetto al numero di Mapper richiesti.

## <a name="guidance"></a>Indicazioni

### <a name="step-1-determine-number-of-jobs-running"></a>Passaggio 1: determinare il numero di processi in esecuzione

Per impostazione predefinita, MapReduce userà l'intero cluster per il processo. È possibile utilizzare un numero minore di BizTalk Mapper rispetto ai contenitori disponibili. Le indicazioni fornite in questo documento presuppongono che l'applicazione sia l'unica in esecuzione nel cluster.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passaggio 2: impostare MapReduce. map. memory/MapReduce. reduce. memory

Le dimensioni della memoria per le attività di mapping e riduzione dipenderanno dal processo specifico. Se si desidera aumentare la concorrenza, è possibile ridurre le dimensioni della memoria. Il numero di attività in esecuzione contemporaneamente dipende dal numero di contenitori. Riducendo la quantità di memoria per ogni mapper o riduttore, verranno creati più contenitori, che permetteranno l'esecuzione simultanea di più mapper o riduttori. Riducendo eccessivamente la quantità di memoria, potranno verificarsi degli errori di memoria insufficiente per alcuni procesis. Se si verifica un errore dell'heap durante l'esecuzione del processo, aumentare la memoria per ogni Mapper o riduttore. Si consideri che l'aggiunta di più contenitori comporta un sovraccarico aggiuntivo per ogni contenitore aggiuntivo, che può potenzialmente peggiorare le prestazioni. In alternativa è possibile ottenere una maggiore quantità di memoria usando un cluster con maggiore memoria oppure aumentando il numero di nodi nel cluster. Una maggiore quantità di memoria consentirà di usare più contenitori, ottenendo così più concorrenza.

### <a name="step-3-determine-total-yarn-memory"></a>Passaggio 3: determinare la memoria totale di YARN

Per ottimizzare MapReduce. job. Maps/MapReduce. job. Reduces, prendere in considerazione la quantità di memoria totale YARN disponibile per l'uso. Le informazioni sono disponibili in Ambari. Passare a YARN e visualizzare la scheda **configs (configurazioni** ). La memoria YARN viene visualizzata in questa finestra. Moltiplicare la memoria di YARN con il numero di nodi nel cluster per ottenere la memoria totale di YARN.

`Total YARN memory = nodes * YARN memory per node`

Se si usa un cluster vuoto, la memoria può essere la memoria totale di YARN per il cluster. Se altre applicazioni stanno usando memoria, è possibile scegliere di usare solo parte della memoria del cluster, riducendo e portando il numero dei mapper o riduttori al numero di contenitori da usare.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Passaggio 4: calcolare il numero di contenitori YARN

I contenitori YARN indicano la quantità di concorrenza disponibile per il processo. Prendere il valore della memoria totale di YARN e dividerlo per mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passaggio 5: impostare MapReduce. job. Maps/MapReduce. job. Reduces

Configurare mapreduce.job.maps/mapreduce.job.reduces come minimo in base al numero di contenitori disponibili. È possibile sperimentare ulteriormente aumentando il numero di mapper e riduttori e verificare se così facendo si ottengono prestazioni migliori. Tenere presente comunque che l'aggiunta di mapper comporterà un carico extra. Un numero eccessivo di mapper potrà causare una diminuzione nelle prestazioni.

La pianificazione e l'isolamento della CPU sono disattivate per impostazione predefinita, pertanto il numero di contenitori YARN è limitato dalla memoria.

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster costituito da 8 nodi D14 e di voler eseguire un processo con attività di I/O intensive. Di seguito sono riportati i calcoli che è necessario eseguire:

### <a name="step-1-determine-number-of-jobs-running"></a>Passaggio 1: determinare il numero di processi in esecuzione

Per questo esempio, si presuppone che il processo sia l'unico in esecuzione.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passaggio 2: impostare MapReduce. map. memory/MapReduce. reduce. memory

Per questo esempio, si esegue un processo con utilizzo intensivo di I/O e si decide che è sufficiente 3 GB di memoria per le attività della mappa.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Passaggio 3: determinare la memoria totale di YARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Passaggio 4: calcolare il numero di contenitori YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passaggio 5: impostare MapReduce. job. Maps/MapReduce. job. Reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Limitazioni

**Limitazione della larghezza di banda della rete di Data Lake Storage Gen1**

Come servizio multi-tenant, Data Lake Storage Gen1 imposta dei limiti di larghezza di banda a livello di account. Se si raggiungono tali limiti, si inizieranno a riscontrare esiti negativi nelle attività. Ciò può essere constatato verificando la presenza di errori di limitazione nei log delle attività. Se occorre ulteriore larghezza di banda per il processo, contattare Microsoft.

Per verificare la presenza di limitazioni, è necessario abilitare la registrazione di debug sul lato client. A tale scopo, è possibile procedere come segue:

1. Inserire la seguente proprietà nelle proprietà log4j in Ambari > YARN > Config (Configurazione) > Advanced yarn log4j (YARN log4j avanzate): log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Riavviare tutti i nodi o servizi per rendere effettiva la nuova configurazione.

3. Se viene applicata la limitazione, verrà visualizzato il codice di errore HTTP 429 nel file di log YARN. Il file di log di YARN si trova nel percorso /tmp/&lt;user&gt;/yarn.log

## <a name="examples-to-run"></a>Esempi di esecuzione

Per illustrare l'esecuzione di MapReduce in Data Lake Storage Gen1, di seguito è riportato un codice di esempio eseguito in un cluster con le impostazioni seguenti:

* D14v2 a 16 nodi
* Cluster Hadoop con HDI 3.6 in esecuzione

Per iniziare, ecco alcuni comandi di esempio per eseguire MapReduce Teragen, Terasort e Teravalidate. È possibile modificare questi comandi in base alle risorse.

### <a name="teragen"></a>Teragen

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
