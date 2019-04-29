---
title: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Linee guida per l'ottimizzazione delle prestazioni di MapReduce in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: b9e5d034db4711384d2ac8a1083da5c93ea11900
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437243"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Linee guida per l'ottimizzazione delle prestazioni di MapReduce in HDInsight e Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Uso di MapReduce in HDInsight**.  Per ulteriori informazioni, vedere [Usare MapReduce in Hadoop su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen1**.  Per informazioni sui concetti generali relativi alle prestazioni, vedere [Linee guida per l'ottimizzazione delle prestazioni in Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametri

Vengono ora illustrati i parametri più importanti da configurare per aumentare le prestazioni in Data Lake Storage Gen1 durante l'esecuzione di processi MapReduce:

* **Mapreduce.map.memory.mb**: la quantità di memoria da allocare a ciascun mapper
* **Mapreduce.job.maps**: il numero di attività di mapping per processo
* **Mapreduce.reduce.memory.mb**: la quantità di memoria da allocare a ciascun riduttore
* **Mapreduce.job.reduces**: il numero di attività di riduzione per processo

**Mapreduce.map.memory / Mapreduce.reduce.memory**: numero da regolare in base alla quantità di memoria necessaria per le operazioni di mapping e/o riduzione.  È possibile visualizzare i valori predefiniti di mapreduce.map.memory e mapreduce.reduce.memory in Ambari tramite la configurazione Yarn.  In Ambari passare a YARN e visualizzare la scheda Configs (Configurazioni).  Viene visualizzata la memoria YARN.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Ciò determinerà il numero massimo di mapper o riduttori da creare.  Il numero di suddivisioni determinerà la quantità di mapper creati per il processo MapReduce.  Pertanto, se ci sono meno suddivisioni rispetto ai mapper richiesti, si potrebbero ottenere meno mapper.       

## <a name="guidance"></a>Materiale sussidiario

**Passaggio 1: Numero di processi in esecuzione** -per impostazione predefinita, MapReduce userà l'intero cluster per il processo.  È possibile usare solo parte del cluster usando un numero di mapper inferiore a quello dei contenitori disponibili.  Le indicazioni fornite in questo documento presuppongono che l'applicazione sia l'unica in esecuzione nel cluster.      

**Passaggio 2: Impostare mapreduce.map.memory/mapreduce.reduce.memory** – le dimensioni della memoria per la mappa e ridurre le attività saranno dipende dal processo specifico.  Se si desidera aumentare la concorrenza, è possibile ridurre le dimensioni della memoria.  Il numero di attività in esecuzione contemporaneamente dipende dal numero di contenitori.  Riducendo la quantità di memoria per ogni mapper o riduttore, verranno creati più contenitori, che permetteranno l'esecuzione simultanea di più mapper o riduttori.  Riducendo eccessivamente la quantità di memoria, potranno verificarsi degli errori di memoria insufficiente per alcuni procesis.  Se si verifica un errore di heap durante l'esecuzione del processo, è necessario aumentare la memoria per ogni mapper o riduttore.  Si noti che l'aggiunta di contenitori può comportare un carico extra per ciascuno di questi ultimi, con una potenziale diminuzione delle prestazioni.  In alternativa è possibile ottenere una maggiore quantità di memoria usando un cluster con maggiore memoria oppure aumentando il numero di nodi nel cluster.  Una maggiore quantità di memoria consentirà di usare più contenitori, ottenendo così più concorrenza.  

**Passaggio 3: Determinare la memoria di YARN totale** : per ottimizzare mapreduce.job.maps/mapreduce.job.reduces, è necessario considerare la quantità di memoria totale di YARN disponibile per l'uso.  Le informazioni sono disponibili in Ambari.  Passare a YARN e visualizzare la scheda Configs (Configurazioni).  La memoria YARN è visualizzata in questa finestra.  Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per il numero di nodi nel cluster.

    Total YARN memory = nodes * YARN memory per node
Se si usa un cluster vuoto, la memoria può corrispondere alla memoria totale di YARN per il cluster.  Se altre applicazioni stanno usando memoria, è possibile scegliere di usare solo parte della memoria del cluster, riducendo e portando il numero dei mapper o riduttori al numero di contenitori da usare.  

**Passaggio 4: Calcolare il numero di contenitori YARN** : i contenitori YARN indicano la quantità di concorrenza disponibile per il processo.  Prendere il valore della memoria totale di YARN e dividerlo per mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passaggio 5: Impostare mapreduce.job.maps/mapreduce.job.reduces** impostare mapreduce.job.maps/mapreduce.job.reduces almeno al numero di contenitori disponibili.  È possibile sperimentare ulteriormente aumentando il numero di mapper e riduttori e verificare se così facendo si ottengono prestazioni migliori.  Tenere presente comunque che l'aggiunta di mapper comporterà un carico extra. Un numero eccessivo di mapper potrà causare una diminuzione nelle prestazioni.  

La pianificazione e l'isolamento della CPU sono disattivate per impostazione predefinita, pertanto il numero di contenitori YARN è limitato dalla memoria.

## <a name="example-calculation"></a>Calcolo di esempio

Si supponga di disporre di un cluster costituito da 8 nodi D14 e di voler eseguire un processo con attività di I/O intensive.  Di seguito sono riportati i calcoli che è necessario eseguire:

**Passaggio 1: Numero di processi in esecuzione** -in questo esempio si presuppone che il processo sia l'unico in esecuzione.  

**Passaggio 2: Impostare mapreduce.map.memory/mapreduce.reduce.memory** – in questo esempio si esegue un processo con utilizzo intensivo dei / o e decidere che saranno sufficienti 3 GB di memoria per le attività di mapping.

    mapreduce.map.memory = 3GB
**Passaggio 3: determinare la memoria totale di YARN**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passaggio 4: calcolare il numero dei contenitori YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passaggio 5: configurare mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitazioni

**Limitazione della larghezza di banda della rete di Data Lake Storage Gen1**

Come servizio multi-tenant, Data Lake Storage Gen1 imposta dei limiti di larghezza di banda a livello di account.  Se si raggiungono tali limiti, si inizieranno a riscontrare esiti negativi nelle attività. Ciò può essere constatato verificando la presenza di errori di limitazione nei log delle attività.  Se occorre ulteriore larghezza di banda per il processo, contattare Microsoft.   

Per verificare la presenza di limitazioni, è necessario abilitare la registrazione di debug sul lato client. Di seguito viene indicato come procedere:

1. Inserire la seguente proprietà nelle proprietà log4j in Ambari > YARN > Config (Configurazione) > Advanced yarn log4j (YARN log4j avanzate): log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Riavviare tutti i nodi o servizi per rendere effettiva la nuova configurazione.

3. In caso di limitazioni, verrà visualizzato il codice di errore HTTP 429 nel file di log di YARN. Il file di log di YARN si trova nel percorso /tmp/&lt;user&gt;/yarn.log

## <a name="examples-to-run"></a>Esempi di esecuzione

Per illustrare la modalità di esecuzione di MapReduce sin Data Lake Storage Gen1, di seguito viene riportato un codice di esempio eseguito in un cluster con le seguenti impostazioni:

* D14v2 a 16 nodi
* Cluster Hadoop con HDI 3.6 in esecuzione

Per iniziare, ecco alcuni comandi di esempio per eseguire MapReduce Teragen, Terasort e Teravalidate.  È possibile modificare questi comandi in base alle risorse.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
