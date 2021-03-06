---
title: Ottimizzazione delle prestazioni-Spark con Azure Data Lake Storage Gen1
description: Informazioni sulle linee guida per l'ottimizzazione delle prestazioni per Spark in Azure HDInsight e Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 799a2d20513f331890bec0882fec852c462c5840
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108270"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Materiale sussidiario per l'ottimizzazione delle prestazioni di Spark in HDInsight e Azure Data Lake Storage Gen1

Per l'ottimizzazione delle prestazioni in Spark, è necessario considerare il numero di applicazioni che verranno eseguite nel cluster. Per impostazione predefinita, è possibile eseguire contemporaneamente quattro app nel cluster HDI. Nota: l'impostazione predefinita è soggetta a modifiche. È possibile decidere di usare un numero inferiore di app in modo da sostituire le impostazioni predefinite e usare una parte più ampia del cluster per tali applicazioni.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Introduzione a Azure Data Lake storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Esecuzione di cluster Spark in Data Lake Storage Gen1**. Per altre informazioni, vedere [Usare il cluster Spark di HDInsight per analizzare i dati in Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen1**. Per i concetti generali relativi alle prestazioni, vedere [Data Lake storage Gen1 linee guida per l'ottimizzazione delle prestazioni](./data-lake-store-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametri

Di seguito sono riportate le impostazioni più importanti che possono essere ottimizzate per migliorare le prestazioni in Data Lake Storage Gen1 durante l'esecuzione di processi Spark:

* **Num-executors**: numero di attività che è possibile eseguire simultaneamente.

* **Executor-memory**: quantità di memoria allocata per ogni executor.

* **Executor-cores**: numero di core allocati per ogni executor.

**Num-executors**. I Num-executors impostano il numero massimo di attività che è possibile eseguire in parallelo. Il numero effettivo di attività che possono essere eseguite in parallelo è limitato dalle risorse di memoria e CPU disponibili nel cluster.

**Executor-memory**. Si tratta della quantità di memoria che viene allocata per ogni executor. La memoria necessaria per ogni executor dipende dal processo. Per operazioni complesse, la quantità di memoria deve essere maggiore. Per operazioni semplici come la lettura e la scrittura, la quantità di memoria richiesta è inferiore. La quantità di memoria necessaria per ogni executor è disponibile in Ambari. In Ambari passare a Spark e visualizzare la scheda **configs (configurazioni** ).

**Executor-cores**. Definisce la quantità di core usati dall'executor predefinito, che determina il numero di thread paralleli che è possibile eseguire su ogni executor. Ad esempio, se executor-cores = 2, ogni executor può eseguire 2 attività parallele nell'executor. Gli executor-cores necessari dipendono dal processo. I processi I/O intensivi non richiedono una grande quantità di memoria per ogni attività. In questo modo, ogni executor può gestire più attività in parallelo.

Per impostazione predefinita, durante l'esecuzione di Spark in HDInsight, vengono definiti due core YARN virtuali per ogni core fisico. Questo numero fornisce un buon bilanciamento tra concorrenza e quantità di contesto nel passaggio tra più thread.

## <a name="guidance"></a>Indicazioni

Durante l'esecuzione di carichi di lavoro analitici di Spark per l'elaborazione dei dati in Data Lake Storage Gen1, si consiglia di usare la versione più recente del cluster HDInsight per ottenere prestazioni ottimali con Data Lake Storage Gen1. Quando il processo prevede un I/O più intensivo, è possibile configurare alcuni parametri per migliorare le prestazioni. Data Lake Storage Gen1 è una piattaforma di archiviazione altamente scalabile in grado di gestire un'elevata velocità effettiva. Se il processo è costituito principalmente da lettura o scrittura, l'aumento della concorrenza di I/O da e verso Data Lake Storage Gen1 potrebbe migliorare le prestazioni.

Esistono alcuni modi generali per aumentare la concorrenza per i processi con I/O intensivo.

**Passaggio 1: Determinare il numero di app in esecuzione nel cluster**. È necessario conoscere il numero di applicazioni in esecuzione nel cluster, inclusa quella corrente. I valori predefiniti per ogni impostazione Spark presumono che siano presenti 4 applicazioni in esecuzione contemporanea. Pertanto, si disporrà solo del 25% del cluster per ogni applicazione. Per ottenere prestazioni migliori, è possibile sostituire le impostazioni predefinite modificando il numero di executor.

**Passaggio 2: Impostare executor-memory**. La prima cosa da impostare è la memoria dell'executor. La memoria dipende dal processo da eseguire. È possibile aumentare la concorrenza allocando una quantità inferiore di memoria per ogni executor. Se vengono visualizzate eccezioni di memoria insufficiente quando si esegue il processo, è necessario aumentare il valore di questo parametro. In alternativa è possibile ottenere una maggiore quantità di memoria usando un cluster con maggiore memoria oppure aumentando le dimensioni del cluster. Una maggiore quantità di memoria consentirà di usare più executor, ottenendo così più concorrenza.

**Passaggio 3: Impostare executor-cores**. Per carichi di lavoro I/O intensivi che non prevedono operazioni complesse, è consigliabile iniziare con un numero elevato di core per ogni executor al fine di aumentare il numero di attività parallele per ognuno di essi. Per iniziare, è consigliabile impostare executor-cores su 4.

```console
executor-cores = 4
```

L'aumento del numero di core dell'executor offrirà maggior parallelismo in modo che sia possibile sperimentare diversi core dell'executor. Per i processi che includono operazioni più complesse, è necessario ridurre il numero di core per ogni executor. Se executor-cores viene impostato su un valore superiore a 4, la Garbage Collection può diventare inefficiente e incidere negativamente sulle prestazioni.

**Passaggio 4: Determinare la quantità di memoria YARN nel cluster**. Queste informazioni sono disponibili in Ambari. Passare a YARN e visualizzare la scheda contigs. La memoria YARN viene visualizzata in questa finestra.
Si noti che nella finestra è possibile visualizzare anche le dimensioni predefinite del contenitore YARN. Le dimensioni del contenitore YARN sono uguali alle dimensioni della memoria per ogni parametro executor.

Memoria totale YARN = nodi * memoria YARN per nodo

**Passaggio 5: Calcolare num-executors**

**Calcolare il vincolo della memoria**. Il parametro num-executors è vincolato dalla memoria o dalla CPU. Il vincolo di memoria è determinato dalla quantità di memoria YARN disponibile per l'applicazione. Prendere la memoria totale di YARN e dividerla per Executor-Memory. Il vincolo deve essere diviso per il numero di applicazioni.

Vincolo di memoria = (memoria totale YARN/memoria Executor)/numero di app

**Calcolare il vincolo della CPU**. Il vincolo della CPU viene calcolato come il numero totale dei core virtuali diviso per il numero di core per ogni executor. Per ogni core fisico sono presenti 2 core virtuali. Come per il vincolo di memoria, questo valore viene diviso per il numero di applicazioni.

core virtuali = (nodi del cluster * # di core fisici nel nodo * 2) vincolo CPU = (Totale core virtuali/numero di core per Executor)/numero di app

**Impostare num-executors**. Il parametro num-executors viene determinato dal valore minimo del vincolo della memoria e del vincolo della CPU. 

Num-eXecutors = min (Totale core virtuali/numero di core per Executor, memoria YARN disponibile/Executor-Memory). l'impostazione di un numero maggiore di numeri di esecutori non comporta necessariamente un miglioramento delle prestazioni. Si noti che l'aggiunta di esecutori può comportare un carico extra per ciascuno di questi ultimi, con una potenziale diminuzione delle prestazioni. I num-executors sono limitati dalle risorse del cluster.

## <a name="example-calculation"></a>Calcolo di esempio

Supponiamo di disporre di un cluster composto da 8 nodi D4v2 che eseguono due app, incluso quello che si intende eseguire.

**Passaggio 1: determinare il numero di app in esecuzione nel cluster** . si è certi che nel cluster sono presenti due app, inclusa quella che verrà eseguita.

**Passaggio 2: Impostare executor-memory**. Per questo esempio, si stabilisce che 6 GB di memoria per executor saranno sufficienti per il processo I/O intensivo.

```console
executor-memory = 6GB
```

**Passaggio 3: impostare Executor-Cores** : poiché si tratta di un processo di i/O intensivo, è possibile impostare il numero di core per ogni Executor su quattro. L'impostazione di core per Executor su più di quattro può causare problemi Garbage Collection.

```console
executor-cores = 4
```

**Passaggio 4: Determinare la quantità di memoria YARN nel cluster**. Aprendo Ambari, si scopre che ogni D4v2 dispone di 25 GB di memoria YARN. Poiché sono presenti 8 nodi, la memoria di YARN disponibile viene moltiplicata per 8.

Memoria totale YARN = nodi * memoria YARN * per nodo totale memoria YARN = 8 nodi * 25 GB = 200 GB

**Passaggio 5: Impostare num-executors**. Il parametro num-executors viene determinato dal valore minimo del vincolo della memoria e del vincolo della CPU, divisi per il numero di app in esecuzione su Spark.

**Calcolare il vincolo della memoria**. Il vincolo della memoria viene calcolato come la memoria totale di YARN divisa per la memoria di ogni executor.

Vincolo di memoria = (memoria totale YARN/memoria Executor)/numero di app vincoli di memoria = (200 GB/6 GB)/2 vincolo di memoria = 16 (arrotondato) **calcolo vincolo CPU** : il vincolo della CPU viene calcolato come i core yarn totali divisi per il numero di core per ogni Executor.

YARN Cores = nodi nel cluster * n. di core per nodo * 2 Core YARN = 8 nodi * 8 core per D14 * 2 = 128 vincolo CPU = (Total YARN Core/n. di core per Executor)/numero di app CPU vincolo = (128/4)/2 vincolo CPU = 16

**Impostare num-executors**

Num-eXecutors = min (vincolo di memoria, vincolo CPU) Num-eXecutors = min (16, 16) Num-eXecutors = 16