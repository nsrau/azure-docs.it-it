---
title: "Guida introduttiva: Creare un cluster Apache Spark in Azure HDInsight con l'interfaccia della riga di comando di Azure"
description: Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare un cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 72bdab9d7fb5c3019d97ffc4c92257c49ec2b8e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066252"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Guida introduttiva: Creare cluster Apache Spark in Azure HDInsight usando l'interfaccia della riga di comando di Azure

In questo argomento di avvio rapido si apprenderà come creare un cluster Apache Spark in Azure HDInsight tramite l'interfaccia della riga di comando di Azure. Apache Spark consente cluster computing e analisi dei dati veloci grazie all'elaborazione in memoria. L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) è l'esperienza di riga di comando multipiattaforma Microsoft per la gestione delle risorse di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) per la relativa procedura.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Creare un cluster Apache Spark

1. Accedere alla sottoscrizione di Azure. Se si prevede di usare Azure Cloud Shell, è sufficiente fare clic su **Prova** nell'angolo superiore destro del blocco di codice. In caso contrario, immettere il comando seguente:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Impostare le variabili di ambiente. L'uso delle variabili in questo argomento di avvio rapido si basa su Bash. Per altri ambienti saranno necessarie piccole modifiche. Sostituire RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME e PASSWORD nel frammento di codice seguente con i valori desiderati. Immettere quindi i comandi dell'interfaccia della riga di comando per impostare le variabili di ambiente.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Creare il gruppo di risorse immettendo il comando seguente:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Creare un account di archiviazione di Azure immettendo il comando seguente:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Estrarre la chiave primaria dall'account di archiviazione di Azure e memorizzarla in una variabile immettendo il comando seguente:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Creare un contenitore di archiviazione di Azure immettendo il comando seguente:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Creare il cluster Apache Spark immettendo il comando seguente:

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

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

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

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un cluster Apache Spark in Azure HDInsight tramite l'interfaccia della riga di comando di Azure.  Passare all'esercitazione successiva per imparare come usare un cluster HDInsight Spark per eseguire query interattive su dati di esempio.

> [!div class="nextstepaction"]
> [Eseguire query interattive su Apache Spark](./apache-spark-load-data-run-query.md)
