---
title: Configurazioni di nodi supportate da Azure HDInsight
description: Informazioni sulle configurazioni minime e consigliate per i nodi del cluster HDInsight.
keywords: dimensioni macchine virtuali, dimensioni cluster, configurazione cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: 0bf5559590b66400fc4fc4dc27ea88c3522effb1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646628"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quali sono le configurazioni del nodo predefinite e consigliate per Azure HDInsight?

Questo articolo illustra le configurazioni del nodo predefinite e consigliate per i cluster Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazioni del nodo predefinite e minime consigliate e dimensioni della macchina virtuale per i cluster

La tabella seguente elenca le dimensioni della macchina virtuale predefinite e consigliate per i cluster HDInsight.  Queste informazioni sono necessarie per comprendere le dimensioni della macchina virtuale da usare durante la creazione di script di PowerShell o dell'interfaccia della riga di comando di Azure per distribuire i cluster HDInsight.

Se si prevedono più di 32 nodi di lavoro in un cluster, selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.

Gli unici tipi di cluster con dischi dati sono i cluster Kafka e HBase con la funzionalità di scritture accelerate abilitata. HDInsight supporta le dimensioni dei dischi P30 e S30 in questi scenari. Per tutti gli altri tipi di cluster, HDInsight fornisce spazio su disco gestito con il cluster. A partire dal 07/11/2019, le dimensioni del disco gestito di ogni nodo nel cluster appena creato sono 128 GB. Tali dimensioni non possono essere modificate.

La tabella seguente riepiloga le specifiche di tutti i tipi di macchina virtuale consigliati in questo documento.

| Dimensione              | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32            / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2/2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |

Per altre informazioni sulle specifiche di ogni tipo di macchina virtuale, vedere i documenti seguenti:

* [Dimensioni delle macchine virtuali per utilizzo generico: Serie Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Dimensioni delle macchine virtuali ottimizzate per la memoria: Serie Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Dimensioni delle macchine virtuali per utilizzo generico: Serie Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Tutte le aree supportate tranne Brasile meridionale e Giappone occidentale

> [!Note]
> Per ottenere l'identificatore dello SKU da usare in PowerShell e in altri script, aggiungere `Standard_` all'inizio di tutti gli SKU della macchina virtuale nelle tabelle seguenti. Ad esempio, `D12_v2` diventerebbe `Standard_D12_v2`.

| Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Head: dimensioni minime consigliate della macchina virtuale | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 con 2 dischi S30 per broker |
| Worker: dimensioni minime consigliate della macchina virtuale | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: dimensioni minime consigliate della macchina virtuale |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Servizi Machine Learning: dimensioni predefinite della macchina virtuale |  |  |  |  |  | D4_v2 |  |
| Servizi Machine Learning: dimensioni minime consigliate della macchina virtuale |  |  |  |  |  | D4_v2 |  |

\* = dimensioni della macchina virtuale per i cluster Spark con Enterprise Security Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Solo Brasile meridionale e Giappone occidentale

| Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head: dimensioni VM predefinite | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: dimensioni minime consigliate della macchina virtuale | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Ruolo di lavoro: dimensioni VM predefinite | D4 | D4 | D14 | D3 | D13 | D4 |
| Worker: dimensioni minime consigliate della macchina virtuale | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: dimensioni VM predefinite |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: dimensioni minime consigliate della macchina virtuale |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Servizi Machine Learning: dimensioni predefinite della macchina virtuale |  |  |  |  |  | D4 |
| Servizi Machine Learning: dimensioni minime consigliate della macchina virtuale |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Supervisor* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Regione* per il tipo di cluster HBase.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti e versioni di Apache Hadoop disponibili in HDInsight](hdinsight-component-versioning.md)
