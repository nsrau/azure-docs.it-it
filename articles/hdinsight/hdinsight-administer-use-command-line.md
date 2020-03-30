---
title: Gestire i cluster di Azure HDInsight usando l'interfaccia della riga di comando di AzureManage Azure HDInsight clusters using Azure CLI
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire i cluster di Azure HDInsight.Learn how to use the Azure CLI to manage Azure HDInsight clusters. I tipi di cluster includono Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query e ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272773"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gestire i cluster di Azure HDInsight usando l'interfaccia della riga di comando di AzureManage Azure HDInsight clusters using Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come usare [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) di Azure per gestire i cluster di Azure HDInsight.Learn how to use Azure CLI to manage Azure HDInsight clusters. L'interfaccia della riga di comando di Azure è l'esperienza di riga di comando multipiattaforma Microsoft per la gestione delle risorse di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) per la relativa procedura.

* Un cluster Apache Hadoop in HDInsight. Vedere [Introduzione a HDInsight su Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Connettersi ad Azure

Accedere alla sottoscrizione di Azure. Se si prevede di usare Azure Cloud Shell, selezionare **Provalo** nell'angolo superiore destro del blocco di codice. In caso contrario, immettere il comando seguente:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Elencare i cluster

Usare [l'elenco hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) per elencare i cluster. Modificare i comandi seguenti `RESOURCE_GROUP_NAME` sostituendo con il nome del gruppo di risorse, quindi immettere i comandi:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Mostrare cluster

Usare [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) per visualizzare le informazioni per un cluster specificato. Modificare il comando seguente `RESOURCE_GROUP_NAME`sostituendo `CLUSTER_NAME` e con le informazioni pertinenti, quindi immettere il comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Eliminare cluster

Usare [az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) per eliminare un cluster specificato. Modificare il comando seguente `RESOURCE_GROUP_NAME`sostituendo `CLUSTER_NAME` e con le informazioni pertinenti, quindi immettere il comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

È inoltre possibile eliminare un cluster eliminando il gruppo di risorse che lo contiene. Si noti che verranno eliminate tutte le risorse del gruppo, incluso l'account di archiviazione predefinito.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Ridimensionare i cluster

Usare [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) per ridimensionare il cluster HDInsight specificato in base alle dimensioni specificate. Modificare il comando seguente `RESOURCE_GROUP_NAME`sostituendo `CLUSTER_NAME` e con le informazioni pertinenti. Sostituire `WORKERNODE_COUNT` con il numero desiderato di nodi di lavoro per il cluster. Per altre informazioni sulla scalabilità dei cluster, vedere Scalare i cluster HDInsight.For more information about scaling [clusters, see Scale HDInsight clusters.](./hdinsight-scaling-best-practices.md) Immettere il comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire diverse attività amministrative del cluster HDInsight.In this article, you've learned how to perform different HDInsight cluster administrative tasks. Per altre informazioni, vedere gli articoli seguenti:

* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md)
* [Amministrare HDInsight con Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Introduzione ad Azure HDInsightGet started with Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introduzione all'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
