---
title: Creare cluster Apache Hadoop usando l'interfaccia della riga di comando di Azure - Azure HDInsightCreate Apache Hadoop clusters using Azure CLI - Azure HDInsight
description: Informazioni su come creare cluster di Azure HDInsight usando l'interfaccia della riga di comando di Azure multipiattaforma.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199042"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

The steps in this document walk-through creating a HDInsight 3.6 cluster using the Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) per la relativa procedura.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Creare un cluster

1. Accedere alla sottoscrizione di Azure. Se si prevede di usare Azure Cloud Shell, selezionare **Provalo** nell'angolo superiore destro del blocco di codice. In caso contrario, immettere il comando seguente:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Impostare le variabili di ambiente. L'uso delle variabili in questo articolo è basato su Bash. Per altri ambienti saranno necessarie piccole modifiche. Vedere [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) per un elenco completo dei possibili parametri per la creazione di cluster.

    |Parametro | Descrizione |
    |---|---|
    |`--workernode-count`| Numero di nodi di lavoro nel cluster. In questo articolo `clusterSizeInNodes` viene utilizzata `--workernode-count`la variabile come valore passato a . |
    |`--version`| versione del cluster HDInsight. In questo articolo `clusterVersion` viene utilizzata `--version`la variabile come valore passato a . Vedere anche: [Versioni di HDInsight supportate](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo di cluster HDInsight, ad esempio: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  In questo articolo `clusterType` viene utilizzata `--type`la variabile come valore passato a . Vedere anche: [Tipi di cluster e configurazione](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Le versioni di vari componenti Hadoop, in versioni separate da spazi in formato 'componente-versione'. In questo articolo `componentVersion` viene utilizzata `--component-version`la variabile come valore passato a . Vedere anche: [Componenti Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Sostituire `RESOURCEGROUPNAME` `LOCATION`, `CLUSTERNAME` `STORAGEACCOUNTNAME`, `PASSWORD` , e con i valori desiderati. Modificare i valori per le altre variabili come desiderato. Quindi immettere i comandi CLI.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Creare il gruppo](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) di risorse immettendo il comando seguente:Create the resource group by entering the command below:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Per un elenco di posizioni `az account list-locations` valide, utilizzare il comando `name` e quindi utilizzare uno dei percorsi dal valore.

4. [Creare un account di Archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) immettendo il comando seguente:Create an Azure Storage account by entering the command below:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Estrarre la chiave primaria dall'account di Archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) e archiviarla in una variabile immettendo il comando seguente:Extract the primary key from the Azure Storage account and store it in a variable by entering the command below:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Creare un contenitore di Archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) immettendo il comando seguente:Create an Azure Storage container by entering the command below:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Creare il cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) immettendo il comando seguente:Create the HDInsight cluster by entering the following command:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.

    Il completamento del processo di creazione del cluster potrebbe richiedere alcuni minuti. in genere circa 15.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'articolo, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Immettere tutti o alcuni dei comandi seguenti per rimuovere le risorse:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Risolvere i problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato correttamente un cluster HDInsight usando l'interfaccia della riga di comando di Azure, usare quanto segue per informazioni su come usare il cluster:Now that you've successfully created an HDInsight cluster using the Azure CLI, use the following to learn how to work with your cluster:

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Cluster Apache HBase

* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Cluster Apache Storm

* [Sviluppare topologie Java per Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usare i componenti di Python in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuzione e monitoraggio di topologie con Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
