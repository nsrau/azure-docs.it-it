---
title: Creare cluster Hadoop con una riga di comando - Azure HDInsight | Documentazione Microsoft
description: Informazioni su come creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure 1.0.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8f2fcb46789d000cd66164508f1159338dcae5f9
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

La procedura presentata in questo documento illustra come creare un cluster HDInsight 3.5 usando l'interfaccia della riga di comando di Azure 1.0.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Interfaccia della riga di comando di Azure**. I passaggi descritti nel presente documento sono stati testati con la versione dell'interfaccia della riga di comando di Azure 0.10.14.

    > [!IMPORTANT]
    > La procedura descritta in questo documento non funziona con l'interfaccia della riga di comando di Azure 2.0. L'interfaccia della riga di comando di Azure 2.0 non supporta la creazione di un cluster HDInsight.

## <a name="log-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure

Seguire i passaggi descritti in [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md) e connettersi alla sottoscrizione usando il metodo **login** .

## <a name="create-a-cluster"></a>Creare un cluster

I passaggi seguenti devono essere eseguiti dalla riga di comando, ad esempio PowerShell o Bash.

1. Per eseguire l'autenticazione della sottoscrizione di Azure, usare il comando seguente:

        azure login

    Occorre specificare il nome e la password. Se si dispone di più sottoscrizioni di Azure, usare `azure account set <subscriptionname>` per impostare la sottoscrizione utilizzata dai comandi dell'interfaccia della riga di comando di Azure.

2. Passare alla modalità Gestione risorse di Azure usando il comando seguente:

        azure config mode arm

3. Creare un gruppo di risorse. Questo gruppo di risorse contiene il cluster HDInsight e l'account di archiviazione associato.

        azure group create groupname location

    * Sostituire `groupname` con un nome univoco per il gruppo.

    * Sostituire `location` con l'area geografica in cui si vuole creare il gruppo.

       Per un elenco di località valide, usare il comando `azure location list` e quindi una delle località della colonna `Name`.

4. Creare un account di archiviazione. Questo account di archiviazione verrà usato come risorsa di archiviazione predefinita per il cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Sostituire `groupname` con il nome del gruppo creato nel passaggio precedente.

    * Sostituire `location` con la stessa località usata nel passaggio precedente.

    * Sostituire `storagename` con un nome univoco per l'account di archiviazione.

        > [!NOTE]
        > Per altre informazioni sui parametri usati in questo comando, usare `azure storage account create -h` per visualizzare la Guida relativa a questo comando.

5. Recuperare la chiave usata per accedere all'account di archiviazione.

        azure storage account keys list -g groupname storagename

    * Sostituire `groupname` con il nome del gruppo di risorse.
    * Sostituire `storagename` con il nome per l'account di archiviazione.

     Nei dati restituiti salvare il valore `key` per `key1`.

6. Creare un cluster HDInsight

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Sostituire `groupname` con il nome del gruppo di risorse.

    * Sostituire `Hadoop` con il tipo di cluster da creare, ad esempio, `Hadoop`, `HBase`, `Kafka`, `Spark` o `Storm`.

     > [!IMPORTANT]
     > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.

    * Sostituire `location` con la stessa località usata nel passaggio precedente.

    * Sostituire `storagename` con il nome per l'account di archiviazione.

    * Sostituire `storagekey` con la chiave ottenuta nel passaggio precedente.

    * Per il parametro `--defaultStorageContainer` usare lo stesso nome usato per il cluster.

    * Sostituire `admin` e `httppassword` con il nome e la password da usare per l'accesso al cluster tramite HTTPS.

    * Sostituire `sshuser` e `sshuserpassword` con il nome utente e la password da usare per l'accesso al cluster tramite SSH

    > [!IMPORTANT]
    > L'esempio precedente crea un cluster con 2 nodi di ruolo di lavoro. È inoltre possibile modificare il numero di nodi del ruolo di lavoro dopo la creazione del cluster eseguendo le operazioni di ridimensionamento. Se si prevede di usare più di 32 nodi del ruolo di lavoro, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. È possibile impostare le dimensioni del nodo head usando il parametro `--headNodeSize` durante la creazione del cluster.
    >
    > Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Il processo di creazione del cluster può richiedere alcuni minuti, in genere circa 15.

## <a name="troubleshoot"></a>Risoluzione dei problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un cluster HDInsight tramite l'interfaccia della riga di comando di Azure, usare le informazioni seguenti per acquisire familiarità con il cluster:

### <a name="hadoop-clusters"></a>Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

