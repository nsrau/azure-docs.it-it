---
title: Uso di archivi di metadati esterni - Azure HDInsight | Microsoft Docs
description: Usare gli archivi di metadati esterni con i cluster HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: 767a1b8a8213b0139878c82d64639b2ba10b5f4f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usare gli archivi di metadati esterni in Azure HDInsight

Il metastore Hive in HDInsight è una parte essenziale dell'architettura di Hadoop. Un metastore è il repository centrale di schemi che può essere usato da altri strumenti di accesso ai Big Data come Spark, Interactive Query (LLAP), Presto o Pig. HDInsight usa un Database SQL di Azure come il metastore Hive.

![Architettura dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Esistono due modi per configurare un metastore per i cluster HDInsight:

* [Metastore predefinito](#default-metastore)
* [Metastore personalizzato](#custom-metastore)

## <a name="default-metastore"></a>Metastore predefinito

Per impostazione predefinita, HDInsight esegue il provisioning di un metastore con ogni tipo di cluster. È invece possibile specificare un metastore personalizzato. Il metastore predefinito include quanto segue:
- Non sono previsti costi aggiuntivi. HDInsight esegue il provisioning di un metastore con ogni tipo di cluster senza alcun costo aggiuntivo per l'utente.
- Ogni metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche quel metastore e i metadati.
- Il metastore predefinito non può essere condiviso con altri cluster.
- Il metastore predefinito usa il database SQL Azure di base, che ha un limite di 5 DTU (unità di trasmissione database).
Questo metastore predefinito viene usato in genere per carichi di lavoro relativamente semplici che non richiedono più cluster e che non hanno bisogno di conservare i metadati oltre il ciclo di vita del cluster.


## <a name="custom-metastore"></a>Metastore personalizzato

HDInsight supporta inoltre i metastore personalizzati, che sono consigliati per i cluster di produzione:
- Specificare il database di SQL Azure come metastore.
- Il ciclo di vita del metastore non è associato a un ciclo di vita dei cluster, pertanto è possibile creare ed eliminare i cluster senza perdere i metadati. I metadati come ad esempio gli schemi di Hive verranno mantenuti anche dopo aver eliminato e ricreato il cluster di HDInsight.
- Un metastore personalizzato consente di collegare più cluster e tipi di cluster al metastore. Ad esempio, un singolo metastore può essere condiviso tra i cluster Interactive Query, Hive e Spark in HDInsight.
- Si paga il costo di un metastore (database SQL di Azure) in base al livello di prestazioni scelto.
- È possibile aumentare il metastore in base alle esigenze.


![Caso d'uso dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selezionare un metastore personalizzato durante la creazione del cluster

È possibile indirizzare il cluster a un database di SQL Azure creato in precedenza durante la creazione del cluster, oppure è possibile configurare il database di SQL dopo la creazione del cluster. Questa opzione viene specificata selezionando Archiviazione > Impostazioni metastore durante la creazione di un nuovo cluster Hadoop, Spark o Hive interattivo dal portale di Azure.

![Portale dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

È possibile aggiungere anche altri cluster a un metastore personalizzato dal portale di Azure o dalle configurazioni Ambari (Hive > Avanzate)

![Ambari dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Procedure consigliate metastore Hive

Ecco alcune procedure consigliate generali per il metastore Hive di HDInsight:

- Usare un metastore personalizzato quando possibile, poiché ciò consentirà di separare le risorse di calcolo (il cluster in esecuzione) e i metadati (archiviati nel metastore).
- Iniziare con un livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.
- Assicurarsi che il metastore creato per una versione del cluster HDInsight non venga condiviso in versioni diverse del cluster HDInsight. Versioni diverse di Hive usano schemi diversi. Ad esempio non è possibile condividere un metastore con i cluster Hive 1.2 e Hive 2.1.
- Eseguire periodicamente il backup del metastore personalizzato.
- Mantenere il metastore e un cluster HDInsight nella stessa area.
- Monitorare le prestazioni e la disponibilità del metastore usando gli strumenti di monitoraggio del Database SQL di Azure, ad esempio il portale di Azure o Azure Log Analytics.

## <a name="oozie-metastore"></a>Metastore Oozie

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop.  Oozie supporta i processi Hadoop per Apache MapReduce, Pig, Hive e altri.  Oozie usa un metastore per archiviare i dettagli sui flussi di lavoro correnti e completati. Per ottenere un miglioramento delle prestazioni quando si usa Oozie, è possibile usare il database SQL di Azure come metastore personalizzato. Il metastore può anche fornire l'accesso ai dati di processo Oozie dopo l'eliminazione del cluster.

Per istruzioni sulla creazione di un metastore Oozie con il database SQL di Azure, vedere come [usare Oozie per i flussi di lavoro](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](./hdinsight-hadoop-provision-linux-clusters.md)
