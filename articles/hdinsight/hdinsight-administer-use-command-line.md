---
title: Gestire cluster HDInsight di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire i cluster HDInsight di Azure. I tipi di cluster includono Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive query e ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/06/2019
ms.openlocfilehash: 81bc632f1061f0ee73d2295cafa5f7a8472d20ee
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951803"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gestire cluster HDInsight di Azure con l'interfaccia della riga di comando di Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come usare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per gestire i cluster HDInsight di Azure. L'interfaccia della riga di comando di Azure è l'esperienza di riga di comando multipiattaforma Microsoft per la gestione delle risorse di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) per la relativa procedura.

* Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Connettersi ad Azure

Accedere alla sottoscrizione di Azure. Se si prevede di usare Azure Cloud Shell, selezionare **prova** nell'angolo superiore destro del blocco di codice. In caso contrario, immettere il comando seguente:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Elencare cluster

Usare il comando [AZ HDInsight list](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) per elencare i cluster. Modificare i comandi seguenti sostituendo `RESOURCE_GROUP_NAME` con il nome del gruppo di risorse, quindi immettere i comandi:

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

Usare il comando [AZ HDInsight Show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) per visualizzare le informazioni relative a un cluster specificato. Modificare il comando seguente sostituendo `RESOURCE_GROUP_NAME`e `CLUSTER_NAME` con le informazioni rilevanti, quindi immettere il comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Eliminare cluster

Usare il comando [AZ HDInsight Delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) per eliminare un cluster specificato. Modificare il comando seguente sostituendo `RESOURCE_GROUP_NAME`e `CLUSTER_NAME` con le informazioni rilevanti, quindi immettere il comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

È inoltre possibile eliminare un cluster eliminando il gruppo di risorse che lo contiene. Si noti che questa operazione eliminerà tutte le risorse nel gruppo, incluso l'account di archiviazione predefinito.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Scalare i cluster

Usare il comando [AZ HDInsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) per ridimensionare il cluster HDInsight specificato alla dimensione specificata. Modificare il comando seguente sostituendo `RESOURCE_GROUP_NAME`e `CLUSTER_NAME` con le informazioni rilevanti. Sostituire `TARGET_INSTANCE_COUNT` con il numero desiderato di nodi del ruolo di lavoro per il cluster. Per altre informazioni sul ridimensionamento dei cluster, vedere [ridimensionare i cluster HDInsight](./hdinsight-scaling-best-practices.md). Immettere il comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire diverse attività amministrative del cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md)
* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introduzione all'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
