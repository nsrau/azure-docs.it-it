---
title: Configurazioni del nodo supportate da Azure HDInsight
description: Informazioni sulle configurazioni minime e consigliate per i nodi del cluster HDInsight.
keywords: dimensioni delle macchine virtuali, dimensioni del cluster, configurazione del cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076212"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quali sono le configurazioni del nodo predefinite e consigliate per Azure HDInsight?

Questo articolo illustra le configurazioni dei nodi predefinite e consigliate per i cluster HDInsight di Azure.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione dei nodi predefinita e consigliata e dimensioni delle macchine virtuali per i cluster

Le tabelle seguenti elencano le dimensioni predefinite e consigliate per le macchine virtuali (VM) per i cluster HDInsight.  Queste informazioni sono necessarie per comprendere le dimensioni delle macchine virtuali da usare durante la creazione di PowerShell o gli script dell'interfaccia della riga di comando di Azure per distribuire i cluster HDInsight. 

Se sono necessari più di 32 nodi di lavoro in un cluster, selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. 

Gli unici tipi di cluster con dischi dati sono i cluster Kafka e HBase con la funzionalità di scrittura accelerata abilitata. HDInsight supporta le dimensioni dei dischi P30 e S30 in questi scenari.

Per altri dettagli sulla specifica di ogni tipo di macchina virtuale, vedere i documenti seguenti:

* [Dimensioni delle macchine virtuali per utilizzo generico: Serie dv2 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Dimensioni delle macchine virtuali con ottimizzazione per la memoria: Serie dv2 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Dimensioni delle macchine virtuali per utilizzo generico: Serie AV2 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Tutte le aree supportate eccetto Brasile meridionale e Giappone occidentale

> [!Note]
> Per ottenere l'identificatore dello SKU da usare in PowerShell e altri script, `Standard_` aggiungere all'inizio di tutti gli SKU di VM nelle tabelle seguenti. Ad esempio, `D12_v2` diventerà `Standard_D12_v2`.

| Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Head: dimensioni VM consigliate | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 con 2 dischi S30 per broker |
| Ruolo di lavoro: dimensioni VM consigliate | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: dimensioni VM consigliate |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML Services: dimensioni VM predefinite |  |  |  |  |  | D4_v2 |  |
| ML Services: dimensioni VM consigliate |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Solo Brasile meridionale e Giappone occidentale

| Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: dimensioni VM consigliate | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4 | D4 | D14 | D3 | D13 | D4 |
| Ruolo di lavoro: dimensioni VM consigliate | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: dimensioni VM consigliate |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML Services: dimensioni VM predefinite |  |  |  |  |  | D4 |
| ML Services: dimensioni di VM consigliate |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Supervisor* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Regione* per il tipo di cluster HBase.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti e versioni di Apache Hadoop disponibili in HDInsight](hdinsight-component-versioning.md)
