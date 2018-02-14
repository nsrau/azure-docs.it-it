---
title: "Pianificazione della capacità dei cluster in Azure HDInsight | Microsoft Docs"
description: "Come specificare un cluster HDInsight per capacità e prestazioni."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: 391b691e895c672ef872f8b98c88567175ad8030
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Pianificazione della capacità per cluster HDInsight

Prima di distribuire un cluster HDInsight, pianificare la capacità desiderata per il cluster determinando la scalabilità e le prestazioni necessarie. Con questa azione di pianificazione è possibile ottimizzare sia la semplicità di utilizzo sia i costi. Alcune decisioni relative alla capacità del cluster non possono essere modificate in seguito alla distribuzione. Se i parametri delle prestazioni cambiano, è comunque possibile smontare e ricreare un cluster senza perdere i dati archiviati.

Di seguito sono elencate alcune domande che consentono di pianificare correttamente la capacità:

* In quale area geografica si intende distribuire il cluster?
* Quanto spazio di archiviazione è necessario?
* Quale tipo di cluster è opportuno distribuire?
* Che tipo di macchina virtuale, e di quali dimensioni, dovrebbero usare i nodi del cluster?
* Quanti nodi di lavoro dovrebbe avere il cluster?

## <a name="choose-an-azure-region"></a>Scegliere un'area di Azure

L'area di Azure determina il luogo in cui viene fisicamente eseguito il provisioning del cluster. Per ridurre al minimo la latenza di lettura e scrittura, è opportuno che il cluster si trovi vicino ai dati.

HDInsight è disponibile in molte aree di Azure. Per trovare l'area più vicina, vedere la voce *HDInsight per Linux* nella sezione *Dati e analisi* della pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Scegliere le dimensioni e la posizione di archiviazione

### <a name="location-of-default-storage"></a>Posizione della risorsa di archiviazione predefinita

La risorsa di archiviazione predefinita, che può essere un account di archiviazione di Azure o Azure Data Lake Store, deve trovarsi nella stessa posizione del cluster. Archiviazione di Azure è disponibile in tutte le posizioni, mentre Data Lake Store è disponibile solo in alcune aree. Per vedere la disponibilità corrente di Data Lake Store, consultare la sezione *Archiviazione* della pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Posizione dei dati esistenti

Se si dispone già di Data Lake Store o di un account di archiviazione contenente i dati personali e si vuole usare questa risorsa di archiviazione come risorsa di archiviazione predefinita del cluster, è necessario distribuire il cluster nella stessa posizione.

### <a name="storage-size"></a>Dimensioni della risorsa di archiviazione

Dopo aver distribuito un cluster HDInsight, è possibile associare altri account di archiviazione di Azure o accedere ad altri archivi Data Lake Store. Tutti gli account di archiviazione devono trovarsi nella stessa posizione del cluster, mentre un Data Lake Store può trovarsi anche in una posizione diversa, ma questo può comportare una certa latenza di lettura/scrittura dei dati.

Archiviazione di Azure presenta inoltre alcuni [limiti di capacità](../azure-subscription-service-limits.md#storage-limits), mentre Data Lake Store è pressoché illimitato.

Un cluster può accedere a una combinazione di account di archiviazione diversi. Di seguito sono riportati alcuni esempi comuni:

* Quando è probabile che la quantità di dati superi la capacità di archiviazione di un singolo contenitore di archiviazione BLOB.
* Quando è possibile che la frequenza di accesso al contenitore BLOB superi la soglia in cui è in funzione la limitazione.
* Quando si vuole rendere disponibili per il cluster i dati già caricati in un contenitore BLOB.
* Quando si preferisce isolare le varie parti della risorsa di archiviazione per motivi di sicurezza o per semplificare le attività di amministrazione.

Per un cluster a 48 nodi si consigliano 4 o 8 account di archiviazione. Anche se è possibile che lo spazio di archiviazione complessivo sia già sufficiente, ogni account di archiviazione fornisce larghezza di banda di rete aggiuntiva per i nodi di calcolo. Se si ha più di un account di archiviazione, usare un nome casuale per ognuno di essi, senza prefisso. La denominazione casuale ha lo scopo di diminuire le probabilità di colli di bottiglia di archiviazione (limitazione) e di errori di modo comune negli account. Per prestazioni ottimali, usare solo un contenitore per ogni account archiviazione.

## <a name="choose-a-cluster-type"></a>Scegliere un tipo di cluster

Il tipo di cluster determina il carico di lavoro per il quale il cluster HDInsight è configurato, ad esempio Hadoop, Storm, Kafka o Spark. Per una descrizione dettagliata dei tipi di cluster disponibili, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Ogni tipo di ogni cluster ha una topologia di distribuzione specifica che include i requisiti relativi alle dimensioni e al numero di nodi.

## <a name="choose-the-vm-size-and-type"></a>Scegliere il tipo e le dimensioni della macchina virtuale

Ogni tipo di cluster contiene un set di tipi di nodo, a cui sono associate opzioni specifiche per il tipo e la dimensione della macchina virtuale.

Per determinare le dimensioni ottimali del cluster per il tipo di applicazione, è possibile effettuare un benchmark della capacità del cluster e aumentare le dimensioni in base a quanto specificato. È possibile, ad esempio, usare un carico di lavoro simulato o una *query canary*. Se si sceglie un carico di lavoro simulato, i carichi di lavoro previsti vengono eseguiti in cluster di varie dimensioni, aumentando progressivamente le dimensioni fino a quando non si raggiungono le prestazioni desiderate. È possibile inserire periodicamente una query canary tra le altre query di produzione per mostrare se il cluster dispone o meno di risorse sufficienti.

Il tipo e le dimensioni della macchina virtuale variano in base alla potenza di elaborazione della CPU, alle dimensioni della RAM e alla latenza di rete.

* CPU: le dimensioni della macchina virtuale determinano il numero di core. Maggiore è il numero di core, più alto è il grado di calcolo parallelo che ogni nodo può raggiungere. Alcuni tipi di macchine virtuali hanno core più veloci.

* RAM: le dimensioni della macchina virtuale determinano anche la quantità di RAM disponibile nella macchina virtuale. Per i carichi di lavoro che archiviano i dati in memoria per essere elaborati, anziché leggerli dal disco, accertarsi che i nodi di lavoro abbiano memoria sufficiente per contenere i dati.

* Rete: nella maggior parte dei tipi di cluster, i dati elaborati dal cluster non si trovano sul disco locale, ma in un servizio di archiviazione esterna come Data Lake Store o Archiviazione di Azure. È necessario quindi tenere conto della larghezza di banda di rete e della velocità effettiva tra la macchina virtuale del nodo e il servizio di archiviazione. In genere, la larghezza di banda di rete disponibile per una macchina virtuale aumenta in caso di macchine di grandi dimensioni. Per informazioni dettagliate, vedere [Panoramica delle dimensioni di VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Scegliere la scalabilità del cluster

La scalabilità di un cluster è determinata dalla quantità di nodi della macchina virtuale. Per tutti i tipi di cluster, esistono tipi di nodo che hanno una scalabilità specifica e tipi di nodo che supportano la scalabilità orizzontale. È possibile, ad esempio, che un cluster richieda esattamente tre nodi ZooKeeper o due nodi Head. I nodi di lavoro che eseguono l'elaborazione dei dati in modalità distribuita possono usufruire della scalabilità orizzontale aggiungendo nodi di lavoro supplementari.

In base al tipo di cluster, aumentando il numero di nodi di lavoro si introduce capacità di calcolo aggiuntiva (ad esempio, più core), ma si aumenta anche la quantità totale di memoria necessaria all'intero cluster per supportare l'archiviazione in memoria dei dati in elaborazione. Come per la scelta del tipo e della dimensione della macchina virtuale, anche la scelta del grado di scalabilità del cluster viene eseguita in modo empirico usando carichi di lavoro simulati o query canary.

È possibile aumentare le istanze del cluster per soddisfare richieste di picchi di carico e ridurle quando non sono più necessari nodi aggiuntivi.
<!-- - see [Scaling - best practices](hdinsight-scaling-best-practices.md). -->

### <a name="cluster-lifecycle"></a>Ciclo di vita del cluster

In genere, i costi vengono addebitati per l'intera durata di un cluster. Se è necessario che il cluster sia attivo e operativo solo in alcuni casi, è possibile [creare cluster on demand usando Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). In alternativa, è possibile creare script di PowerShell che eseguono il provisioning ed eliminano il cluster e quindi pianificare gli script tramite [Automazione di Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Quando viene eliminato un cluster, viene eliminato anche il metastore Hive predefinito. Per mantenere il metastore per la creazione del cluster successivo, usare un archivio di metadati esterno come Database di Azure o Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolare gli errori di processo del cluster

L'esecuzione parallela di più componenti di mapping e riduzione su un cluster multi-nodo può generare talvolta degli errori. Per isolare il problema, provare a effettuare test distribuiti eseguendo prima più processi simultanei su un cluster a singolo nodo e quindi espandere questo approccio eseguendo contemporaneamente più processi su cluster a più nodi. Per creare un cluster HDInsight a singolo nodo in Azure, usare l'opzione *Avanzate*.

È possibile anche installare un ambiente di sviluppo a singolo nodo nel computer locale ed eseguire qui il test della soluzione. Hortonworks offre un ambiente di sviluppo locale a singolo nodo per soluzioni basate su Hadoop, particolarmente utile per le attività iniziali di sviluppo, la creazione di un modello di verifica e il testing. Per altre informazioni, vedere [Hortonworks Sandbox](http://hortonworks.com/products/hortonworks-sandbox/).

Per identificare il problema in un cluster locale a singolo nodo, è possibile ripetere l'esecuzione dei processi non riusciti modificando i dati di input o usando set di dati più piccoli. Le modalità di esecuzione dei processi dipendono dalla piattaforma e dal tipo di applicazione.

## <a name="quotas"></a>Quote

Dopo aver determinato il tipo, la dimensione e la scalabilità della macchina virtuale del cluster di destinazione, è necessario verificare i limiti di capacità di quota della sottoscrizione. Quando si raggiunge un limite di quota, è possibile che non sia più consentito distribuire nuovi cluster o aumentare le istanze dei cluster esistenti aggiungendo più nodi di lavoro. Il limite di quota più comunemente raggiunto è la quota di core CPU, esistente a livello di sottoscrizione, di area e di serie di macchine virtuali. Una sottoscrizione, ad esempio, può avere un limite totale di 200 core, con un limite di 30 core relativo all'area geografica e un limite di 30 core per le istanze della macchina virtuale. È possibile [contattare il supporto tecnico per richiedere un aumento dei limiti di quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Esistono tuttavia alcuni limiti di quota fissi: una singola sottoscrizione di Azure, ad esempio, non può avere più di 10.000 core. Per informazioni dettagliate sui limiti, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come impostare e configurare i cluster di HDInsight con Hadoop, Spark, Kafka, Interactive Hive, HBase, R Server o Storm.
* [Monitorare le prestazioni del cluster](hdinsight-key-scenarios-to-monitor.md): informazioni sui principali scenari da monitorare per il cluster HDInsight che potrebbero influire sulla relativa capacità.
