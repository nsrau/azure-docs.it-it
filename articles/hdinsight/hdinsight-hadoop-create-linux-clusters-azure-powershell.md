---
title: Creare cluster Hadoop con PowerShell - Azure HDInsight | Microsoft Docs
description: Informazioni su come creare cluster Hadoop, HBase, Storm o Spark su Linux per HDInsight tramite Azure PowerShell.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 2bbb34987bbf639bcd246afbfd4debea98681922
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Creare cluster basati su Linux in HDInsight tramite Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Microsoft Azure. Questo documento offre informazioni su come creare un cluster HDInsight basato su Linux usando Azure PowerShell. Include anche uno script di esempio.

> [!NOTE]
> Azure PowerShell è disponibile solo su client Windows. Se si usa un client Mac OS X, Linux o Unix, vedere [Creare cluster basati su Linux in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) per informazioni sull'uso dell'interfaccia della riga di comando di Azure per creare un cluster.

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa procedura è necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Il supporto di Azure PowerShell per la gestione delle risorse HDInsight tramite Azure Service Manager è **deprecato** ed è stato rimosso dal 1° gennaio 2017. La procedura descritta in questo documento usa i nuovi cmdlet HDInsight, compatibili con Azure Resource Manager.
    >
    > Per installare la versione più recente di Azure PowerShell, seguire la procedura descritta in [Installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Se sono presenti script che devono essere modificati per l'uso dei nuovi cmdlet compatibili con Azure Resource Manager, per altre informazioni vedere [Migrazione a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) .

## <a name="create-cluster"></a>Creare cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

È necessario completare le procedure seguenti per creare un cluster HDInsight con Azure PowerShell:

* Creare un gruppo di risorse di Azure
* Creare un account di Archiviazione di Azure
* Creazione di un contenitore BLOB di Azure
* Creazione di un cluster HDInsight

Lo script seguente illustra come creare un nuovo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

I valori specificati per l'accesso al cluster vengono usati per creare l'account utente Hadoop per il cluster. Usare questo account per connettersi ai servizi ospitati nel cluster, ad esempio interfacce utente Web o API REST.

I valori specificati per l'utente SSH vengono usati per creare l'utente SSH per il cluster. Usare questo account per avviare una sessione SSH remota nel cluster ed eseguire i processi. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Se si prevede di usare più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario specificare anche una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

La creazione di un cluster può richiedere fino a 20 minuti.

## <a name="create-cluster-configuration-object"></a>Creare il cluster: oggetto di configurazione

È anche possibile creare un oggetto di configurazione di HDInsight tramite il cmdlet `New-AzureRmHDInsightClusterConfig`. È quindi possibile modificare questo oggetto di configurazione per abilitare le opzioni di configurazione aggiuntive per il cluster. Infine, usare il parametro `-Config` del cmdlet `New-AzureRmHDInsightCluster` per usare la configurazione.

Lo script seguente crea un oggetto di configurazione per configurare un R Server sul tipo di cluster HDInsight. La configurazione consente a un nodo del bordo, RStudio e ad un account di archiviazione aggiuntivo.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> L'uso di un account di archiviazione in una località diversa rispetto al cluster HDInsight non è supportato. Quando si usa questo esempio, creare l'account di archiviazione aggiuntivo nello stesso percorso del server.

## <a name="customize-clusters"></a>Personalizzare i cluster

* Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Vedere [Personalizzare cluster HDInsight usando l'azione script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le risorse seguenti per acquisire familiarità con il cluster.

### <a name="hadoop-clusters"></a>Cluster Hadoop

* [Usare Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Cluster HBase

* [Introduzione a HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Cluster Spark

* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire i processi in modalità remota in un cluster Spark utilizzando Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](spark/apache-spark-machine-learning-mllib-ipython.md)

