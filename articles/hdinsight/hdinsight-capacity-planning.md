---
title: Pianificazione della capacità dei cluster in Azure HDInsight
description: Identificare le domande chiave per la pianificazione della capacità e delle prestazioni di un cluster Azure HDInsight.Identify key questions for capacity and performance planning of an Azure HDInsight cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
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

HDInsight è disponibile in molte aree di Azure. Per trovare l'area più vicina, vedere [Prodotti disponibili per regione](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Scegliere le dimensioni e la posizione di archiviazione

### <a name="location-of-default-storage"></a>Posizione della risorsa di archiviazione predefinita

La risorsa di archiviazione predefinita, che può essere un account di Archiviazione di Azure o Azure Data Lake Storage, deve trovarsi nella stessa posizione del cluster. Archiviazione di Azure è disponibile in tutte le posizioni, Data Lake Storage Gen1 è disponibile in alcune aree geografiche: vedere la disponibilità corrente di [Data Lake Storage](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Posizione dei dati esistenti

Se si vuole usare un account di archiviazione esistente o Archiviazione dati per data lake come risorsa di archiviazione predefinita del cluster, è necessario distribuire il cluster nella stessa posizione.

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

In un cluster distribuito è possibile collegare altri account di Archiviazione di Azure o accedere ad altri account di Archiviazione data lake. Tutti gli account di archiviazione devono trovarsi nella stessa posizione del cluster. Un Data Lake Storage può trovarsi in una posizione diversa, anche se grandi distanze possono introdurre una certa latenza.

Archiviazione di Azure presenta alcuni limiti di [capacità,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)mentre Data Lake Storage Gen1 è quasi illimitato.

Un cluster può accedere a una combinazione di account di archiviazione diversi. Di seguito sono riportati alcuni esempi comuni:

* Quando è probabile che la quantità di dati superi la capacità di archiviazione di un singolo contenitore di archiviazione BLOB.
* Quando è possibile che la frequenza di accesso al contenitore BLOB superi la soglia in cui è in funzione la limitazione.
* Quando si desidera rendere i dati già caricati in un contenitore BLOB disponibile per il cluster.
* Quando si preferisce isolare le varie parti della risorsa di archiviazione per motivi di sicurezza o per semplificare le attività di amministrazione.

Per prestazioni ottimali, usare solo un contenitore per ogni account archiviazione.

## <a name="choose-a-cluster-type"></a>Scegliere un tipo di cluster

Il tipo di cluster determina il carico di lavoro che il cluster HDInsight è configurato per l'esecuzione. I tipi includono [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)o [Apache Spark](./spark/apache-spark-overview.md). Per una descrizione dettagliata dei tipi di cluster disponibili, vedere [Introduzione ad Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Ogni tipo di ogni cluster ha una topologia di distribuzione specifica che include i requisiti relativi alle dimensioni e al numero di nodi.

## <a name="choose-the-vm-size-and-type"></a>Scegliere il tipo e le dimensioni della macchina virtuale

Ogni tipo di cluster contiene un set di tipi di nodo, a cui sono associate opzioni specifiche per il tipo e la dimensione della macchina virtuale.

Per determinare le dimensioni ottimali del cluster per il tipo di applicazione, è possibile effettuare un benchmark della capacità del cluster e aumentare le dimensioni in base a quanto specificato. È possibile, ad esempio, usare un carico di lavoro simulato o una *query canary*. Eseguire i carichi di lavoro simulati in cluster di dimensioni diverse. Aumentare gradualmente le dimensioni fino a raggiungere le prestazioni previste. Una query canary può essere inserita periodicamente tra le altre query di produzione per mostrare se il cluster dispone di risorse sufficienti.

Per altre informazioni su come scegliere la famiglia di macchine virtuali più adatta al carico di lavoro, vedere [Selezione delle dimensioni corrette per il cluster.](hdinsight-selecting-vm-size.md)

## <a name="choose-the-cluster-scale"></a>Scegliere la scalabilità del cluster

La scalabilità di un cluster è determinata dalla quantità di nodi della macchina virtuale. Per tutti i tipi di cluster, esistono tipi di nodo con una scalabilità specifica e tipi di nodo che supportano la scalabilità orizzontale. Ad esempio, un cluster può richiedere esattamente tre nodi [Apache zooKeeper](https://zookeeper.apache.org/) o due nodi Head. I nodi di lavoro che estraggono i dati in modo distribuito traggono vantaggio dai nodi di lavoro aggiuntivi.

A seconda del tipo di cluster, l'aumento del numero di nodi di lavoro aggiunge ulteriore capacità di calcolo (ad esempio più core). Più nodi aumenteranno la memoria totale necessaria per l'intero cluster per supportare l'archiviazione in memoria dei dati in fase di elaborazione. Come per la scelta delle dimensioni e del tipo di macchina virtuale, la selezione della scala del cluster corretta viene in genere raggiunta empiricamente. Usare carichi di lavoro simulati o query canarino.

È possibile scalare orizzontalmente il cluster per soddisfare le richieste di carico massimo. Quindi ridimensionarlo quando i nodi aggiuntivi non sono più necessari. La [funzionalità di scalabilità automatica](hdinsight-autoscale-clusters.md) consente di ridimensionare automaticamente il cluster in base a metriche e intervalli predeterminati. Per altre informazioni sulla scalabilità manuale dei cluster, vedere [Scalare i cluster HDInsight.For](hdinsight-scaling-best-practices.md)more information on scaling your clusters manually, see Scale HDInsight clusters .

### <a name="cluster-lifecycle"></a>Ciclo di vita del cluster

Ti viene addebitato per tutta la vita di un cluster. Se il cluster è necessario solo in orari specifici, [creare cluster su richiesta con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). In alternativa, è possibile creare script di PowerShell che eseguono il provisioning ed eliminano il cluster e quindi pianificare gli script tramite [Automazione di Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando viene eliminato un cluster, viene eliminato anche il metastore Hive predefinito. Per mantenere il metastore per la creazione del cluster successivo, usare un archivio di metadati esterno come Database di Azure o [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolare gli errori di processo del cluster

A volte possono verificarsi errori a causa dell'esecuzione parallela di più mappe e ridurre i componenti in un cluster a più nodi. Per isolare il problema, provare a eseguire il test distribuito. Eseguire più processi simultanei in un cluster a nodo di lavoro singolo. Espandere quindi questo approccio per eseguire più processi contemporaneamente su cluster contenenti più di un nodo. Per creare un cluster HDInsight a nodo *`Custom(size, settings, apps)`* singolo in Azure, usare l'opzione e usare il valore 1 per *Numero di nodi di lavoro* nella sezione Dimensioni **cluster** quando si esegue il provisioning di un nuovo cluster nel portale.

## <a name="quotas"></a>Quote

Dopo aver determinato il tipo, la dimensione e la scalabilità della macchina virtuale del cluster di destinazione, è necessario verificare i limiti di capacità di quota della sottoscrizione. Quando si raggiunge un limite di quota, non è possibile distribuire nuovi cluster. Oppure scalabilità orizzontale dei cluster esistenti mediante l'aggiunta di più nodi di lavoro. L'unico limite di quota è la quota di core di CPU esistente a livello di area per ogni sottoscrizione. Ad esempio, la sottoscrizione può avere il limite di 30 core nell'area Stati Uniti orientali.

Per verificare i core disponibili, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare alla pagina **Panoramica** per il cluster HDInsight.Navigate to the Overview page for the HDInsight cluster.
3. Nel menu a sinistra selezionare **Limiti di quota**.

   Nella pagina vengono visualizzati il numero di core in uso, il numero di core disponibili e i core totali.

Se è necessario richiedere un aumento della quota, eseguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Guida e supporto nella** parte inferiore sinistra della pagina.
1. Selezionare **Nuova richiesta di supporto**.
1. Nella pagina **Nuova richiesta di supporto** selezionare le opzioni seguenti nella scheda **Informazioni di base**:

   - **Tipo di**problema : **Limiti di servizio e abbonamento (quote)**
   - **Sottoscrizione**: l'abbonamento che si desidera modificare
   - **Tipo di quota**: **HDInsightQuota** type : HDInsight

     ![Creare una richiesta di supporto per aumentare la quota di core HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selezionare **Avanti: Soluzioni >>**.
1. Nella pagina **Dettagli** immettere una descrizione del problema, selezionare la gravità del problema, il metodo di contatto preferito e altri campi obbligatori.
1. Selezionare **Successivo: Rivedere e creare >>**.
1. Nella scheda **Rivedi e crea** selezionare **Crea**.

> [!NOTE]  
> Se occorre aumentare la quota di core HDInsight in un'area privata, [inviare una richiesta di aggiunta all'elenco elementi consentiti](https://aka.ms/canaryintwhitelist).

È possibile [contattare il supporto tecnico per richiedere un aumento dei limiti di quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Esistono alcuni limiti di quota fissi. Ad esempio, una singola sottoscrizione di Azure può avere al massimo 10.000 core. Per informazioni dettagliate sui limiti, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster in HDInsight con Apache Hadoop, Spark, Kafka e altro ancora:](hdinsight-hadoop-provision-linux-clusters.md)informazioni su come configurare e configurare i cluster in HDInsight.Set up clusters in HDInsight with Apache Hadoop, Spark, Kafka and more : Learn how to set up and configure clusters in HDInsight.
* [Monitorare le prestazioni del cluster](hdinsight-key-scenarios-to-monitor.md): informazioni sui principali scenari da monitorare per il cluster HDInsight che potrebbero influire sulla relativa capacità.
