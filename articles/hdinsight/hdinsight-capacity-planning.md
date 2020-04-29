---
title: Pianificazione della capacità dei cluster in Azure HDInsight
description: Identificare le domande chiave per la pianificazione della capacità e delle prestazioni di un cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886994"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Pianificazione della capacità per cluster HDInsight

Prima di distribuire un cluster HDInsight, pianificare la capacità del cluster desiderata determinando le prestazioni e la scalabilità necessarie. Con questa azione di pianificazione è possibile ottimizzare sia la semplicità di utilizzo sia i costi. Alcune decisioni relative alla capacità del cluster non possono essere modificate dopo la distribuzione. Se i parametri delle prestazioni cambiano, è comunque possibile smontare e ricreare un cluster senza perdere i dati archiviati.

Di seguito sono elencate alcune domande che consentono di pianificare correttamente la capacità:

* In quale area geografica si intende distribuire il cluster?
* Quanto spazio di archiviazione è necessario?
* Quale tipo di cluster è opportuno distribuire?
* Che tipo di macchina virtuale, e di quali dimensioni, dovrebbero usare i nodi del cluster?
* Quanti nodi di lavoro dovrebbe avere il cluster?

## <a name="choose-an-azure-region"></a>Scegliere un'area di Azure

L'area di Azure determina il luogo in cui viene fisicamente eseguito il provisioning del cluster. Per ridurre al minimo la latenza di lettura e scrittura, è opportuno che il cluster si trovi vicino ai dati.

HDInsight è disponibile in molte aree di Azure. Per trovare l'area più vicina, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Scegliere le dimensioni e la posizione di archiviazione

### <a name="location-of-default-storage"></a>Posizione della risorsa di archiviazione predefinita

La risorsa di archiviazione predefinita, che può essere un account di Archiviazione di Azure o Azure Data Lake Storage, deve trovarsi nella stessa posizione del cluster. Archiviazione di Azure è disponibile in tutte le posizioni, Data Lake Storage Gen1 è disponibile in alcune aree, vedere la [disponibilità del data Lake storage](https://azure.microsoft.com/global-infrastructure/services/?products=storage)corrente.

### <a name="location-of-existing-data"></a>Posizione dei dati esistenti

Se si vuole usare un account di archiviazione esistente o Data Lake Storage come risorsa di archiviazione predefinita del cluster, è necessario distribuire il cluster nella stessa posizione.

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

In un cluster distribuito è possibile aggiungere altri account di archiviazione di Azure o accedere ad altri Data Lake Storage. Tutti gli account di archiviazione devono risiedere nella stessa posizione del cluster. Una Data Lake Storage può trovarsi in una posizione diversa, anche se le distanze ottimali possono presentare una certa latenza.

Archiviazione di Azure presenta alcuni [limiti di capacità](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), mentre Data Lake storage Gen1 è quasi illimitato.

Un cluster può accedere a una combinazione di account di archiviazione diversi. Di seguito sono riportati alcuni esempi comuni:

* Quando è probabile che la quantità di dati superi la capacità di archiviazione di un singolo contenitore di archiviazione BLOB.
* Quando è possibile che la frequenza di accesso al contenitore BLOB superi la soglia in cui è in funzione la limitazione.
* Quando si vogliono creare dati, è già stato caricato in un contenitore BLOB disponibile nel cluster.
* Quando si preferisce isolare le varie parti della risorsa di archiviazione per motivi di sicurezza o per semplificare le attività di amministrazione.

Per prestazioni ottimali, usare solo un contenitore per ogni account archiviazione.

## <a name="choose-a-cluster-type"></a>Scegliere un tipo di cluster

Il tipo di cluster determina il carico di lavoro che il cluster HDInsight è configurato per l'esecuzione. I tipi includono [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)o [Apache Spark](./spark/apache-spark-overview.md). Per una descrizione dettagliata dei tipi di cluster disponibili, vedere [Introduzione ad Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Ogni tipo di ogni cluster ha una topologia di distribuzione specifica che include i requisiti relativi alle dimensioni e al numero di nodi.

## <a name="choose-the-vm-size-and-type"></a>Scegliere il tipo e le dimensioni della macchina virtuale

Ogni tipo di cluster contiene un set di tipi di nodo, a cui sono associate opzioni specifiche per il tipo e la dimensione della macchina virtuale.

Per determinare le dimensioni ottimali del cluster per il tipo di applicazione, è possibile effettuare un benchmark della capacità del cluster e aumentare le dimensioni in base a quanto specificato. È possibile, ad esempio, usare un carico di lavoro simulato o una *query canary*. Eseguire i carichi di lavoro simulati in cluster di dimensioni diverse. Aumentare gradualmente le dimensioni fino a raggiungere le prestazioni desiderate. Una query Canary può essere inserita periodicamente tra le altre query di produzione per indicare se il cluster dispone di risorse sufficienti.

Per altre informazioni su come scegliere la famiglia di VM appropriata per il carico di lavoro, vedere [selezione delle dimensioni della macchina virtuale corrette per il cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Scegliere la scalabilità del cluster

La scalabilità di un cluster è determinata dalla quantità di nodi della macchina virtuale. Per tutti i tipi di cluster sono disponibili tipi di nodo con una scala specifica e tipi di nodo che supportano la scalabilità orizzontale. Un cluster, ad esempio, può richiedere esattamente tre nodi [Apache ZooKeeper](https://zookeeper.apache.org/) o due nodi head. I nodi del ruolo di lavoro che eseguono l'elaborazione dei dati in un vantaggio distribuito della moda dai nodi del ruolo di lavoro aggiuntivi.

A seconda del tipo di cluster, aumentando il numero di nodi di lavoro si aggiunge ulteriore capacità di calcolo, ad esempio più core. Un numero maggiore di nodi aumenterà la quantità di memoria totale richiesta dall'intero cluster per supportare l'archiviazione in memoria dei dati in fase di elaborazione. Come per la scelta delle dimensioni e del tipo di macchina virtuale, la selezione della scalabilità del cluster corretta viene in genere raggiunta in modo empirico. Usare carichi di lavoro simulati o query Canary.

È possibile scalare in orizzontale il cluster per soddisfare le richieste di carico di picco. Ridimensionarlo quindi quando questi nodi aggiuntivi non sono più necessari. La [funzionalità di scalabilità](hdinsight-autoscale-clusters.md) automatica consente di ridimensionare automaticamente il cluster in base alle metriche e agli intervalli predeterminati. Per altre informazioni sul ridimensionamento manuale dei cluster, vedere [ridimensionare i cluster HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo di vita del cluster

Viene addebitata la durata di un cluster. Se è necessario il cluster solo per orari specifici, [creare cluster su richiesta con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). In alternativa, è possibile creare script di PowerShell che eseguono il provisioning ed eliminano il cluster e quindi pianificare gli script tramite [Automazione di Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando viene eliminato un cluster, viene eliminato anche il metastore Hive predefinito. Per mantenere il metastore per la creazione del cluster successivo, usare un archivio di metadati esterno come Database di Azure o [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolare gli errori di processo del cluster

Talvolta possono verificarsi errori a causa dell'esecuzione parallela di più mappe e della riduzione dei componenti in un cluster a più nodi. Per isolare il problema, provare a eseguire il test distribuito. Eseguire più processi simultanei in un singolo cluster di nodi del ruolo di lavoro. Espandere quindi questo approccio per eseguire più processi simultaneamente nei cluster che contengono più di un nodo. Per creare un cluster HDInsight a nodo singolo in Azure, usare l' *`Custom(size, settings, apps)`* opzione e usare il valore 1 per il *numero di nodi del ruolo di lavoro* nella sezione **dimensioni del cluster** durante il provisioning di un nuovo cluster nel portale.

## <a name="quotas"></a>Quote

Dopo aver determinato il tipo, la dimensione e la scalabilità della macchina virtuale del cluster di destinazione, è necessario verificare i limiti di capacità di quota della sottoscrizione. Quando si raggiunge un limite di quota, non è possibile distribuire nuovi cluster. O scalare i cluster esistenti aggiungendo più nodi del ruolo di lavoro. L'unico limite di quota è la quota di core di CPU esistente a livello di area per ogni sottoscrizione. Ad esempio, la sottoscrizione può avere il limite di 30 core nell'area Stati Uniti orientali.

Per controllare i core disponibili, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare alla pagina **Panoramica** per il cluster HDInsight.
3. Nel menu a sinistra selezionare **limiti di quota**.

   La pagina Visualizza il numero di core in uso, il numero di core disponibili e i core totali.

Se è necessario richiedere un aumento della quota, eseguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Guida e supporto** nel lato inferiore sinistro della pagina.
1. Selezionare **Nuova richiesta di supporto**.
1. Nella pagina **Nuova richiesta di supporto** selezionare le opzioni seguenti nella scheda **Informazioni di base**:

   - **Tipo di problema**: **limiti del servizio e della sottoscrizione (quote)**
   - **Sottoscrizione**: la sottoscrizione che si vuole modificare
   - **Tipo di quota**: **HDInsight**

     ![Creare una richiesta di supporto per aumentare la quota di core HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selezionare **Avanti: soluzioni >>**.
1. Nella pagina **Dettagli** immettere una descrizione del problema, selezionare la gravità del problema, il metodo di contatto preferito e altri campi obbligatori.
1. Selezionare **Avanti: rivedere + crea >>**.
1. Nella scheda **Rivedi e crea** selezionare **Crea**.

> [!NOTE]  
> Se occorre aumentare la quota di core HDInsight in un'area privata, [inviare una richiesta di aggiunta all'elenco elementi consentiti](https://aka.ms/canaryintwhitelist).

È possibile [contattare il supporto tecnico per richiedere un aumento dei limiti di quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Sono previsti limiti di quota fissi. Ad esempio, una singola sottoscrizione di Azure può avere al massimo 10.000 core. Per informazioni dettagliate sui limiti, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare cluster in HDInsight con Apache Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come impostare e configurare i cluster in HDInsight.
* [Monitorare le prestazioni del cluster](hdinsight-key-scenarios-to-monitor.md): informazioni sui principali scenari da monitorare per il cluster HDInsight che potrebbero influire sulla relativa capacità.
