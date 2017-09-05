---
title: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Store | Microsoft Docs
description: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Store
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
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 9528148792f083cb0e48d356e61cf61762ee954f
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Linee guida per l'ottimizzazione delle prestazioni di MapReduce in HDInsight e in Azure Data Lake Store


## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Uso di MapReduce in HDInsight**.  Per ulteriori informazioni, vedere [Usare MapReduce in Hadoop su HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-mapreduce)  
* **Linee guida per l'ottimizzazione delle prestazioni in ADLS**.  Per i concetti generali relativi alle prestazioni, vedere [Linee guida per l'ottimizzazione delle prestazioni in Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="parameters"></a>parameters

Vengono ora illustrati i parametri più importanti da configurare per aumentare le prestazioni in ADLS durante l'esecuzione di processi MapReduce:

* **Mapreduce.map.memory.mb**: la quantità di memoria da allocare a ciascun mapper
* **Mapreduce.job.maps**: il numero di attività di mapping per processo
* **Mapreduce.reduce.memory.mb**: la quantità di memoria da allocare a ciascun riduttore
* **Mapreduce.job.reduces**: il numero di attività di riduzione per processo

**Mapreduce.map.memory / Mapreduce.reduce.memory**: numero da regolare in base alla quantità di memoria necessaria per le operazioni di mapping e/o riduzione.  È possibile visualizzare i valori predefiniti di mapreduce.map.memory e mapreduce.reduce.memory in Ambari tramite la configurazione Yarn.  In Ambari passare a YARN e visualizzare la scheda Configs (Configurazioni).  Viene visualizzata la memoria YARN.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Ciò determinerà il numero massimo di mapper o riduttori da creare.  Il numero di suddivisioni determinerà la quantità di mapper creati per il processo MapReduce.  Pertanto, se ci sono meno suddivisioni rispetto ai mapper richiesti, si potrebbero ottenere meno mapper.       

## <a name="guidance"></a>Indicazioni

**Passaggio 1: determinare il numero di processi in esecuzione.** Per impostazione predefinita, MapReduce userà l'intero cluster per il processo.  È possibile usare solo parte del cluster usando un numero di mapper inferiore a quello dei contenitori disponibili.  Le indicazioni fornite in questo documento presuppongono che l'applicazione sia l'unica in esecuzione nel cluster.      

**Passaggio 2: impostare mapreduce.map.memory/mapreduce.reduce.memory**. Le dimensioni della memoria per le attività di mapping e riduzione dipenderanno dal processo specifico.  Se si desidera aumentare la concorrenza, è possibile ridurre le dimensioni della memoria.  Il numero di attività in esecuzione contemporaneamente dipende dal numero di contenitori.  Riducendo la quantità di memoria per ogni mapper o riduttore, verranno creati più contenitori, che permetteranno l'esecuzione simultanea di più mapper o riduttori.  Riducendo eccessivamente la quantità di memoria, potranno verificarsi degli errori di memoria insufficiente per alcuni procesis.  Se si verifica un errore di heap durante l'esecuzione del processo, è necessario aumentare la memoria per ogni mapper o riduttore.  Si noti che l'aggiunta di contenitori può comportare un carico extra per ciascuno di questi ultimi, con una potenziale diminuzione delle prestazioni.  In alternativa è possibile ottenere una maggiore quantità di memoria usando un cluster con maggiore memoria oppure aumentando il numero di nodi nel cluster.  Una maggiore quantità di memoria consentirà di usare più contenitori, ottenendo così più concorrenza.  

**Passaggio 3: determinare la memoria totale di YARN**. Per ottimizzare mapreduce.job.maps/mapreduce.job.reduces è necessario tenere presente la quantità di memoria totale di YARN disponibile all'uso.  Le informazioni sono disponibili in Ambari.  Passare a YARN e visualizzare la scheda Configs (Configurazioni).  La memoria YARN è visualizzata in questa finestra.  Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per il numero di nodi nel cluster.

    Total YARN memory = nodes * YARN memory per node
Se si usa un cluster vuoto, la memoria può corrispondere alla memoria totale di YARN per il cluster.  Se altre applicazioni stanno usando memoria, è possibile scegliere di usare solo parte della memoria del cluster, riducendo e portando il numero dei mapper o riduttori al numero di contenitori da usare.  

**Passaggio 4: calcolare il numero di contenitori YARN**. I contenitori YARN indicano la quantità di concorrenza disponibile per il processo.  Prendere il valore della memoria totale di YARN e dividerlo per mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces** Configurare mapreduce.job.maps/mapreduce.job.reduces come minimo in base al numero di contenitori disponibili.  È possibile sperimentare ulteriormente aumentando il numero di mapper e riduttori e verificare se così facendo si ottengono prestazioni migliori.  Tenere presente comunque che l'aggiunta di mapper comporterà un carico extra. Un numero eccessivo di mapper potrà causare una diminuzione nelle prestazioni.  

La pianificazione e l'isolamento della CPU sono disattivate per impostazione predefinita, pertanto il numero di contenitori YARN è limitato dalla memoria.

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster costituito da 8 nodi D14 e di voler eseguire un processo con attività di I/O intensive.  Di seguito sono riportati i calcoli che è necessario eseguire:

**Passaggio 1: determinare il numero di processi in esecuzione**. Nell'esempio riportato si presuppone che il processo sia l'unico in esecuzione.  

**Passaggio 2: configurare mapreduce.map.memory/mapreduce.reduce.memory**. Nell'esempio riportato si esegue un processo con attività di I/O intensive e si decide che sono sufficienti 3 GB di memoria per le attività di mapping.

    mapreduce.map.memory = 3GB
**Passaggio 3: determinare la memoria totale di YARN**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passaggio 4: calcolare il numero dei contenitori YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitazioni

**Limitazione ADLS**

In quanto servizio multi-tenant, ADLS imposta dei limiti di larghezza di banda a livello di account.  Se si raggiungono tali limiti, si inizieranno a riscontrare esiti negativi nelle attività. Ciò può essere constatato verificando la presenza di errori di limitazione nei log delle attività.  Se occorre ulteriore larghezza di banda per il processo, contattare Microsoft.   

Per verificare la presenza di limitazioni, è necessario abilitare la registrazione di debug sul lato client. Di seguito viene indicato come procedere:

1. Inserire la seguente proprietà nelle proprietà log4j in Ambari > YARN > Config (Configurazione) > Advanced yarn log4j (YARN log4j avanzate): log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Riavviare tutti i nodi o servizi per rendere effettiva la nuova configurazione.

3. In caso di limitazioni, verrà visualizzato il codice di errore HTTP 429 nel file di log di YARN. Il file di log di YARN si trova nel percorso /tmp/&lt;user&gt;/yarn.log

## <a name="examples-to-run"></a>Esempi di esecuzione

Per illustrare la modalità di esecuzione di MapReduce su Azure Data Lake Store, di seguito viene riportato un codice di esempio eseguito in un cluster con le seguenti impostazioni:

* D14v2 a 16 nodi
* Cluster Hadoop con HDI 3.6 in esecuzione

Per iniziare, ecco alcuni comandi di esempio per eseguire MapReduce Teragen, Terasort e Teravalidate.  È possibile modificare questi comandi in base alle risorse.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate

