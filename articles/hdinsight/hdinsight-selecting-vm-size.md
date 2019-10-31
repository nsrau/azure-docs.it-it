---
title: Come selezionare le dimensioni di macchina virtuale corrette per il cluster HDInsight di Azure
description: Informazioni su come selezionare le dimensioni appropriate per la macchina virtuale per il cluster HDInsight.
keywords: dimensioni delle macchine virtuali, dimensioni del cluster, configurazione del cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: d93c3599bb4a52a03d4ac436ca664ce6be1c55b9
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200827"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Selezione delle dimensioni di macchina virtuale corrette per il cluster HDInsight di Azure

Questo articolo illustra come selezionare le dimensioni di macchina virtuale corrette per i vari nodi del cluster HDInsight. 

Per iniziare, è necessario comprendere in che modo le proprietà di una macchina virtuale, ad esempio l'elaborazione della CPU, le dimensioni della RAM e la latenza di rete, influiranno sull'elaborazione dei carichi di lavoro. A questo punto, è opportuno considerare l'applicazione e il modo in cui corrisponde alle diverse famiglie di VM ottimizzate per. Assicurarsi che la famiglia di macchine virtuali che si desidera utilizzare sia compatibile con il tipo di cluster che si intende distribuire. Per un elenco di tutte le dimensioni di macchina virtuale supportate e consigliate per ogni tipo di cluster, vedere [configurazioni dei nodi supportati da Azure HDInsight](hdinsight-supported-node-configuration.md). Infine, è possibile usare un processo di benchmarking per testare alcuni carichi di lavoro di esempio e verificare quale SKU all'interno di tale famiglia è adatto alle proprie proprie.

Per altre informazioni sulla pianificazione di altri aspetti del cluster, ad esempio la selezione di un tipo di archiviazione o di dimensioni del cluster, vedere [pianificazione della capacità per i cluster HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Proprietà della macchina virtuale e carichi di lavoro Big Data

Il tipo e le dimensioni della macchina virtuale variano in base alla potenza di elaborazione della CPU, alle dimensioni della RAM e alla latenza di rete.

- CPU: le dimensioni della macchina virtuale determinano il numero di core. Maggiore è il numero di core, più alto è il grado di calcolo parallelo che ogni nodo può raggiungere. Alcuni tipi di macchine virtuali hanno core più veloci.

- RAM: le dimensioni della macchina virtuale determinano anche la quantità di RAM disponibile nella macchina virtuale. Per i carichi di lavoro che archiviano i dati in memoria per essere elaborati, anziché leggerli dal disco, accertarsi che i nodi di lavoro abbiano memoria sufficiente per contenere i dati.

- Rete: per la maggior parte dei tipi di cluster, i dati elaborati dal cluster non si trovino in un disco locale, ma piuttosto in un servizio di archiviazione esterno, ad esempio Data Lake Storage o archiviazione di Azure. È necessario quindi tenere conto della larghezza di banda di rete e della velocità effettiva tra la macchina virtuale del nodo e il servizio di archiviazione. In genere, la larghezza di banda di rete disponibile per una macchina virtuale aumenta in caso di macchine di grandi dimensioni. Per informazioni dettagliate, vedere [Panoramica delle dimensioni di VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Informazioni sull'ottimizzazione delle macchine virtuali

Le famiglie di macchine virtuali in Azure sono ottimizzate per adattarsi a diversi casi d'uso. Nella tabella seguente è possibile trovare alcuni dei casi d'uso più comuni e le famiglie di VM corrispondenti.

| Type                     | Dimensioni           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [A livello di voce](../virtual-machines/linux/sizes-general.md)          | A, AV2  | Sono disponibili configurazioni di memoria e prestazioni della CPU più adatte per carichi di lavoro a livello di voce quali sviluppo e test. Sono economiche e offrono una soluzione a basso costo per iniziare a usare Azure. |
| [Utilizzo generico](../virtual-machines/linux/sizes-general.md)          | D, DSv2, dv2  | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Ottimizzate per il calcolo](../virtual-machines/linux/sizes-compute.md)        | F           | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni.        |
| [Ottimizzate per la memoria](../virtual-machines/linux/sizes-memory.md)         | Esv3, EV3  | Rapporto elevato tra memoria e CPU. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |

- Per informazioni sui prezzi delle istanze di VM disponibili nelle aree HDInsight supportate, vedere [prezzi di HDInsight](https://azure.microsoft.com/en-us/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Risparmio sui costi dei tipi di VM per carichi di lavoro leggeri

In presenza di requisiti di elaborazione leggeri, la [serie F](https://azure.microsoft.com/blog/f-series-vm-size/) può essere una scelta ottimale per iniziare a usare HDInsight. Con un prezzo di listino orario più basso, la serie F presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di elaborazione di Azure (ACU, Azure Compute Unit) per ogni vCPU.

La tabella seguente descrive i tipi di cluster e i tipi di nodo che possono essere creati con le macchine virtuali della serie Fsv2.

| Tipo di cluster | Versione | Nodo del ruolo di lavoro | Nodo head | Nodo Zookeeper |
|---|---|---|---|---|
| Spark | Tutto | F4 e versioni successive | no | no |
| Hadoop | Tutto | F4 e versioni successive | no | no |
| Kafka | Tutto | F4 e versioni successive | no | no |
| hbase | Tutto | F4 e versioni successive | no | no |
| LLAP | disabled | no | no | no |
| Storm | disabled | no | no | no |
| Servizio ML | SOLO HDI 3,6 | F4 e versioni successive | no | no |

Per visualizzare le specifiche di ogni SKU della serie F, vedere [dimensioni delle macchine virtuali della serie f](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Benchmarking

Il benchmarking è il processo di esecuzione di carichi di lavoro simulati in macchine virtuali diverse per misurare le prestazioni che eseguiranno per i carichi di lavoro di produzione. 

Per altre informazioni sul benchmarking per gli SKU di VM e le dimensioni dei cluster, vedere [pianificazione della capacità del cluster in Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Passaggi successivi

- [Configurazioni del nodo supportate da Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md)
