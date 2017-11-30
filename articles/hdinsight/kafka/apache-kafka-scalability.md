---
title: "Aumento della scalabilità in Apache Kafka - Azure HDInsight | Microsoft Docs"
description: "Informazioni su come configurare i dischi gestiti per i cluster di Apache Kafka in Azure HDInsight per aumentare la scalabilità."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: e0fef242810148938e62dab9f77ee77b543fbbc0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurare l'archiviazione e la scalabilità per Apache Kafka in HDInsight

Questo articolo spiega come configurare il numero di dischi gestiti usati da Apache Kafka in HDInsight.

Kafka in HDInsight usa il disco locale delle macchine virtuali nel cluster HDInsight. Dal momento che in Kafka i processi I/O sono intensivi, viene usata la funzionalità [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) per assicurare una velocità effettiva elevata e fornire maggiore spazio di archiviazione per ogni nodo. Se si usano le tradizionali unità disco rigido virtuali (VHD) per Kafka, ogni nodo è limitato a 1 TB. Con i dischi gestiti, è possibile usare più dischi per ottenere 16 TB per ogni nodo del cluster.

Il diagramma seguente offre un confronto tra Kafka in HDInsight prima dei dischi gestiti e Kafka in HDInsight con i dischi gestiti:

![Diagramma che mostra Kafka in HDInsight con un singolo disco rigido virtuale per ogni macchina virtuale e con più dischi gestiti per ogni macchina virtuale](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Configurare i dischi gestiti: portale di Azure

1. Seguire i passaggi riportati in [Creare un cluster HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md) per comprendere le operazioni principali per creare un cluster tramite il portale. Non completare il processo di creazione del portale.

2. Nella sezione __Dimensioni del cluster__ usare il campo __Disks per worker node__ (Dischi per nodo del ruolo di lavoro) per configurare il numero di dischi.

    > [!NOTE]
    > Il tipo di disco gestito può essere __Standard__ (HDD) o __Premium__ (SSD). I dischi Premium sono usati con le macchine virtuali serie DS e GS. Tutti gli altri tipi di macchine virtuali usano dischi Standard.

    ![Immagine della sezione Dimensioni del cluster con i dischi per nodo del ruolo di lavoro evidenziati](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Configurare i dischi gestiti: modello di Resource Manager

Per controllare il numero di dischi usati dai nodi del ruolo di lavoro in un cluster Kafka, usare la sezione seguente del modello:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

È possibile trovare un modello completo che illustra come configurare i dischi gestiti all'indirizzo [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso della gestione di Kafka in HDInsight, vedere i documenti seguenti:

* [Usare MirrorMaker per creare una replica di Kafka in HDInsight](apache-kafka-mirroring.md)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)

* [Blog di HDInsight sui dischi gestiti con Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)