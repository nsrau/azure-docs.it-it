---
title: Configurazioni dei nodi supportati di Azure HDInsightAzure HDInsight supported node configurations
description: Informazioni sulle configurazioni minime e consigliate per i nodi del cluster HDInsight.Learn the minimum and recommended configurations for HDInsight cluster nodes.
keywords: dimensioni delle macchine virtuali, dimensioni del cluster, configurazione del cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484787"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quali sono le configurazioni di nodi predefinite e consigliate per Azure HDInsight?

Questo articolo illustra le configurazioni dei nodi predefinite e consigliate per i cluster di Azure HDInsight.This article discusses default and recommended node configurations for Azure HDInsight clusters.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione predefinita e minima consigliata dei nodi e delle macchine virtuali per i cluster

Le tabelle seguenti elencano le dimensioni predefinite e consigliate delle macchine virtuali (VM) per i cluster HDInsight.The following tables list default and recommended virtual machine (VM) sizes for HDInsight clusters.  Queste informazioni sono necessarie per comprendere le dimensioni delle macchine virtuali da usare quando si creano script dell'interfaccia di comando di PowerShell o Di Azure per distribuire cluster HDInsight.This information is necessary to understand the VM sizes to use when you are creating PowerShell or Azure CLI scripts to deploy HDInsight clusters.

Se sono necessari più di 32 nodi di lavoro in un cluster, selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. 

Gli unici tipi di cluster con dischi dati sono i cluster Kafka e HBase con la funzionalità Scritture accelerate abilitata. HDInsight supporta le dimensioni dei dischi P30 e S30 in questi scenari.

Le specifiche di tutti i tipi di VM consigliati minimi usati in questo documento sono riepilogate nella tabella seguente.

| Dimensione              | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2/2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |

Per altre informazioni sulle specifiche di ogni tipo di macchina virtuale, vedere i documenti seguenti:For more details on the specifications of each VM type, see the following documents:

* [Dimensioni delle macchine virtuali generiche: serie Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Dimensioni delle macchine virtuali ottimizzate per la memoria: serie Dv2 11-15Memory optimized virtual machine sizes: Dv2 series 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Dimensioni delle macchine virtuali generiche: Av2 serie 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Tutte le regioni supportate, ad eccezione del Brasile meridionale e del Giappone occidentale

> [!Note]
> Per ottenere l'identificatore SKU da usare `Standard_` in Powershell e in altri script, aggiungere all'inizio di tutti gli SKU della macchina virtuale nelle tabelle seguenti. Ad esempio, `D12_v2` `Standard_D12_v2`diventerebbe .

| Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2. | D12_v2 | D3_v2 |
| Testa: dimensioni minime consigliate per le macchine virtuali | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2. | D12_v2 | D3_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 con 2 dischi S30 per broker |
| Lavoratore: dimensioni minime consigliate per le macchine virtualiWorker: minimum recommended VM sizes | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: dimensioni minime consigliate per le macchine virtuali |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Servizi ML: dimensione predefinita della macchina virtualeML Services: default VM size |  |  |  |  |  | D4_v2 |  |
| Servizi ML: dimensione minima consigliata della macchina virtualeML Services: minimum recommended VM size |  |  |  |  |  | D4_v2 |  |

\*Dimensioni delle macchine virtuali per i cluster Spark Enterprise Security Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brasile a sud e Giappone ovest solo

| Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Testa: dimensioni minime consigliate per le macchine virtuali | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4 | D4 | D14 | D3 | D13 | D4 |
| Lavoratore: dimensioni minime consigliate per le macchine virtualiWorker: minimum recommended VM sizes | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: dimensioni minime consigliate per le macchine virtuali |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Servizi ML: dimensioni predefinite delle macchine virtualiML Services: default VM sizes |  |  |  |  |  | D4 |
| Servizi ML: dimensioni minime consigliate per le macchine virtuali |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Supervisor* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Regione* per il tipo di cluster HBase.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti e versioni di Apache Hadoop disponibili in HDInsight](hdinsight-component-versioning.md)
