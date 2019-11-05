---
title: Creare cluster di Apache Hadoop usando l'interfaccia della riga di comando di Azure-Azure HDInsight
description: Informazioni su come creare cluster Azure HDInsight usando l'interfaccia della riga di comando di Azure multipiattaforma.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 09696f5a3df7cc4170c57b862a11bbd5a82e2bc9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494804"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

La procedura illustrata in questo documento illustra come creare un cluster HDInsight 3,6 usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) per la relativa procedura.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Creare un cluster

1. Accedere alla sottoscrizione di Azure. Se si prevede di usare Azure Cloud Shell, è sufficiente fare clic su **Prova** nell'angolo superiore destro del blocco di codice. In caso contrario, immettere il comando seguente:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Impostare le variabili di ambiente. L'uso delle variabili in questo articolo si basa su bash. Per altri ambienti saranno necessarie piccole modifiche. Vedere [AZ-HDInsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) per un elenco completo dei parametri possibili per la creazione del cluster.

    |Parametro | Descrizione |
    |---|---|
    |`--size`| Numero di nodi del ruolo di lavoro nel cluster. Questo articolo usa la variabile `clusterSizeInNodes` come valore passato a `--size`. |
    |`--version`| versione del cluster HDInsight. Questo articolo usa la variabile `clusterVersion` come valore passato a `--version`. Vedere anche: [versioni supportate di HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo di cluster HDInsight, ad esempio: Hadoop, interactivehive, HBase, Kafka, Storm, Spark, RServer, mlservices.  Questo articolo usa la variabile `clusterType` come valore passato a `--type`. Vedere anche: [tipi e configurazione di cluster](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Versioni di vari componenti di Hadoop, in versioni separate da spazi nel formato ' Component = version '. Questo articolo usa la variabile `componentVersion` come valore passato a `--component-version`. Vedere anche: [componenti di Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Sostituire `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`e `PASSWORD` con i valori desiderati. Modificare i valori per le altre variabili nel modo desiderato. Quindi, immettere i comandi dell'interfaccia della riga di comando.

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

3. [Creare il gruppo di risorse](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) immettendo il comando seguente:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Per un elenco di percorsi validi, utilizzare il comando `az account list-locations`, quindi utilizzare uno dei percorsi del valore `name`.

4. Per [creare un account di archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) , immettere il comando seguente:

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

5. [Estrarre la chiave primaria dall'account di archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) e archiviarla in una variabile immettendo il comando seguente:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Per [creare un contenitore di archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) , immettere il comando seguente:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Creare il cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) immettendo il comando seguente:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.

    Il processo di creazione del cluster può richiedere alcuni minuti, in genere circa 15.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'articolo, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

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

Dopo aver creato un cluster HDInsight tramite l'interfaccia della riga di comando di Azure, usare le informazioni seguenti per acquisire familiarità con il cluster:

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Cluster Apache HBase

* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Cluster Apache Storm

* [Sviluppare topologie Java per Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usare i componenti di Python in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuzione e monitoraggio di topologie con Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
