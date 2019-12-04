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
ms.openlocfilehash: 278639b27d821e8d6440248a1add43bcd9de22c6
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775226"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quali sono le configurazioni del nodo predefinite e consigliate per Azure HDInsight?

Questo articolo illustra le configurazioni dei nodi predefinite e consigliate per i cluster HDInsight di Azure.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione dei nodi predefinita e minima consigliata e dimensioni delle macchine virtuali per i cluster

Le tabelle seguenti elencano le dimensioni predefinite e consigliate per le macchine virtuali (VM) per i cluster HDInsight.  Queste informazioni sono necessarie per comprendere le dimensioni delle macchine virtuali da usare durante la creazione di PowerShell o gli script dell'interfaccia della riga di comando di Azure per distribuire i cluster HDInsight.

Se sono necessari più di 32 nodi di lavoro in un cluster, selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. 

Gli unici tipi di cluster con dischi dati sono i cluster Kafka e HBase con la funzionalità di scrittura accelerata abilitata. HDInsight supporta le dimensioni dei dischi P30 e S30 in questi scenari.

Nella tabella seguente sono riepilogate le specifiche di tutti i tipi di VM consigliati in questo documento.

| Dimensioni              | vCPU | Memoria: GiB | GiB di archiviazione temporanea (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32X500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2/2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |

Per altri dettagli sulle specifiche di ogni tipo di macchina virtuale, vedere i documenti seguenti:

* [Dimensioni delle macchine virtuali per utilizzo generico: dv2 serie 1-5](../virtual-machines/linux/sizes-general.md#dv2-series)
* [Dimensioni delle macchine virtuali con ottimizzazione per la memoria: dv2 serie 11-15](../virtual-machines/linux/sizes-memory.md#dv2-series-11-15)
* [Dimensioni delle macchine virtuali per utilizzo generico: AV2 serie 1-8](../virtual-machines/linux/sizes-general.md#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Tutte le aree supportate eccetto Brasile meridionale e Giappone occidentale

> [!Note]
> Per ottenere l'identificatore dello SKU da usare in PowerShell e in altri script, aggiungere `Standard_` all'inizio di tutti gli SKU di VM nelle tabelle seguenti. Ad esempio, `D12_v2` diventerà `Standard_D12_v2`.

| Tipo di cluster | Hadoop | hbase | Query interattiva | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head: dimensioni minime consigliate per le macchine virtuali | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 con 2 dischi S30 per broker |
| Ruolo di lavoro: dimensioni minime consigliate per le VM | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: dimensioni minime consigliate per le macchine virtuali |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML Services: dimensioni VM predefinite |  |  |  |  |  | D4_v2 |  |
| ML Services: dimensioni minime consigliate per le macchine virtuali |  |  |  |  |  | D4_v2 |  |

\* = dimensioni delle VM per i cluster Spark Enterprise Security Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Solo Brasile meridionale e Giappone occidentale

| Tipo di cluster | Hadoop | hbase | Query interattiva | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: dimensioni minime consigliate per le macchine virtuali | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4 | D4 | D14 | D3 | D13 | D4 |
| Ruolo di lavoro: dimensioni minime consigliate per le VM | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: dimensioni minime consigliate per le macchine virtuali |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML Services: dimensioni VM predefinite |  |  |  |  |  | D4 |
| ML Services: dimensioni minime consigliate per le macchine virtuali |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Supervisor* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Regione* per il tipo di cluster HBase.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti e versioni di Apache Hadoop disponibili in HDInsight](hdinsight-component-versioning.md)
