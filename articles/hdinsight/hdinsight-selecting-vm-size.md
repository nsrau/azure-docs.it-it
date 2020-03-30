---
title: Come selezionare la dimensione della macchina virtuale corretta per il cluster Azure HDInsightHow to select the right VM size for your Azure HDInsight cluster
description: Informazioni su come selezionare le dimensioni corrette della macchina virtuale per il cluster HDInsight.Learn how to select the right VM size for your HDInsight cluster.
keywords: dimensioni delle macchine virtuali, dimensioni del cluster, configurazione del cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682214"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Selezione delle dimensioni corrette della macchina virtuale per il cluster Azure HDInsightSelecting the right VM size for your Azure HDInsight cluster

Questo articolo illustra come selezionare le dimensioni corrette della macchina virtuale per i vari nodi nel cluster HDInsight.This article discusses how to select the right VM size for the various nodes in your HDInsight cluster. 

Iniziare comprendendo in che modo le proprietà di una macchina virtuale, ad esempio l'elaborazione della CPU, le dimensioni della RAM e la latenza di rete, influiranno sull'elaborazione dei carichi di lavoro. Successivamente, pensare all'applicazione e come corrisponde a quali diverse famiglie di macchine virtuali sono ottimizzate per. Assicurarsi che la famiglia di macchine virtuali che si desidera utilizzare sia compatibile con il tipo di cluster che si intende distribuire. Per un elenco di tutte le dimensioni delle macchine virtuali supportate e consigliate per ogni tipo di cluster, vedere Configurazioni dei [nodi supportate](hdinsight-supported-node-configuration.md)di Azure HDInsight.For a list of all supported and recommended VM sizes for each cluster type, see Azure HDInsight supported node configurations . Infine, è possibile usare un processo di benchmarking per testare alcuni carichi di lavoro di esempio e verificare quale SKU all'interno di tale famiglia è più adatto alle persone.

Per ulteriori informazioni sulla pianificazione di altri aspetti del cluster, ad esempio la selezione di un tipo di archiviazione o di dimensioni del cluster, vedere [Pianificazione della capacità per i cluster HDInsight.](hdinsight-capacity-planning.md)

## <a name="vm-properties-and-big-data-workloads"></a>Proprietà delle macchine virtuali e carichi di lavoro di Big DataVM properties and big data workloads

Il tipo e le dimensioni della macchina virtuale variano in base alla potenza di elaborazione della CPU, alle dimensioni della RAM e alla latenza di rete.

- CPU: le dimensioni della macchina virtuale determinano il numero di core. Maggiore è il numero di core, più alto è il grado di calcolo parallelo che ogni nodo può raggiungere. Alcuni tipi di macchine virtuali hanno core più veloci.

- RAM: le dimensioni della macchina virtuale determinano anche la quantità di RAM disponibile nella macchina virtuale. Per i carichi di lavoro che archiviano i dati in memoria per essere elaborati, anziché leggerli dal disco, accertarsi che i nodi di lavoro abbiano memoria sufficiente per contenere i dati.

- Rete: per la maggior parte dei tipi di cluster, i dati elaborati dal cluster non si esicheono sul disco locale, ma piuttosto in un servizio di archiviazione esterno, ad esempio Archiviazione data lake o Archiviazione di Azure.Network: For most cluster types, the data processed by the cluster't is on local disk, but rather in an external storage service such as Data Lake Storage or Azure Storage. È necessario quindi tenere conto della larghezza di banda di rete e della velocità effettiva tra la macchina virtuale del nodo e il servizio di archiviazione. In genere, la larghezza di banda di rete disponibile per una macchina virtuale aumenta in caso di macchine di grandi dimensioni. Per informazioni dettagliate, vedere [Panoramica delle dimensioni di VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Informazioni sull'ottimizzazione delle macchine virtualiUnderstanding VM optimization

Le famiglie di macchine virtuali in Azure sono ottimizzate per adattarsi a diversi casi d'uso. Nella tabella seguente sono disponibili alcuni dei casi d'uso più popolari e le famiglie di macchine virtuali corrispondenti ad essi.

| Type                     | Dimensioni           |    Descrizione       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Livello base](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Disporre di configurazioni di memoria e prestazioni della CPU più adatte per carichi di lavoro entry level come sviluppo e test. Sono economici e forniscono un'opzione a basso costo per iniziare a usare Azure.They are economicl and provide a low-cost option to get started with Azure. |
| [Finalità generale](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Ottimizzate per il calcolo](../virtual-machines/linux/sizes-compute.md)        | F           | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni.        |
| [Ottimizzate per la memoria](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Rapporto elevato tra memoria e CPU. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |

- Per informazioni sui prezzi delle istanze di macchine virtuali disponibili nelle aree supportate di HDInsight, vedere [Prezzi di HDInsight.For information](https://azure.microsoft.com/pricing/details/hdinsight/)about pricing of available VM instances across HDInsight supported regions, see HDInsight Pricing .

## <a name="cost-saving-vm-types-for-light-workloads"></a>Tipi di macchine virtuali con riduzione dei costi per carichi di lavoro leggeri

Se si dispone di requisiti di elaborazione della luce, la serie F può essere una buona scelta per iniziare a utilizzare HDInsight.If you have light processing requirements, the [F-series](https://azure.microsoft.com/blog/f-series-vm-size/) can be a good choice to get started with HDInsight. Con un prezzo di listino orario più basso, la serie F presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di elaborazione di Azure (ACU, Azure Compute Unit) per ogni vCPU.

Nella tabella seguente vengono descritti i tipi di cluster e i tipi di nodo, che possono essere creati con le macchine virtuali della serie Fsv2.

| Tipo di cluster | Versione | Nodo lavoratore | Nodo testa | Nodo dello zooguardian |
|---|---|---|---|---|
| Spark | Tutti | F4 e oltre | no | no |
| Hadoop | Tutti | F4 e oltre | no | no |
| Kafka | Tutti | F4 e oltre | no | no |
| hbase | Tutti | F4 e oltre | no | no |
| LLAP (LLAP) | disabled | no | no | no |
| Storm | disabled | no | no | no |
| Servizio ML | HDI 3.6 SOLO | F4 e oltre | no | no |

Per visualizzare le specifiche di ogni SKU della serie F, vedere [Dimensioni delle macchine virtuali della serie F.](https://azure.microsoft.com/blog/f-series-vm-size/)

## <a name="benchmarking"></a>Benchmarking

Il benchmarking è il processo di esecuzione di carichi di lavoro simulati in macchine virtuali diverse per misurare le prestazioni per i carichi di lavoro di produzione. 

Per altre informazioni sul benchmarking per sKU di macchine virtuali e dimensioni del cluster, vedere Pianificazione della capacità del cluster in Azure HDInsight.For more information on benchmarking for VM SKUs and cluster sizes, see [Cluster capacity planning in Azure HDInsight. ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)

## <a name="next-steps"></a>Passaggi successivi

- [Configurazioni dei nodi supportati di Azure HDInsightAzure HDInsight supported node configurations](hdinsight-supported-node-configuration.md)
- [Dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md)
