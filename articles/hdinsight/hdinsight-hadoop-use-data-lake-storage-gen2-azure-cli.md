---
title: Creare Azure HDInsight-Azure Data Lake Storage Gen2-interfaccia della riga di comando di Azure
description: Informazioni su come usare Azure Data Lake Storage Gen2 con i cluster Azure HDInsight usando l'interfaccia della riga di comando di Azure.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: e8cfe55f4843439743535b978483d8518bd020fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858783"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Creare un cluster con Data Lake Storage Gen2 usando l'interfaccia della riga di comando di Azure

Per creare un cluster HDInsight che USA Data Lake Storage Gen2 per l'archiviazione, seguire questa procedura.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con Azure Data Lake Storage Gen2, vedere la [sezione Panoramica](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:
    - Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare l'opzione Azure Cloud Shell incorporata tramite il pulsante "Prova", disponibile nell'angolo superiore destro di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. Accedere ad Azure usando `az login` , usando un account associato alla sottoscrizione di Azure in cui si vuole distribuire l'identità gestita assegnata dall'utente. INTERFACCIA della riga di comando di Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

È possibile [scaricare un file modello di esempio](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [scaricare un file di parametri di esempio](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Prima di usare il modello e il frammento di codice dell'interfaccia della riga di comando di Azure, sostituire i segnaposto seguenti con i valori corretti:

| Segnaposto | Descrizione |
|---|---|
| `<SUBSCRIPTION_ID>` | ID della sottoscrizione di Azure |
| `<RESOURCEGROUPNAME>` | Il gruppo di risorse in cui si vuole creare il nuovo cluster e l'account di archiviazione. |
| `<MANAGEDIDENTITYNAME>` | Nome dell'identità gestita a cui verranno concesse le autorizzazioni per l'account di archiviazione con Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nuovo account di archiviazione con Azure Data Lake Storage Gen2 che verrà creato. |
| `<FILESYSTEMNAME>`  | Nome del file System che il cluster deve usare nell'account di archiviazione. |
| `<CLUSTERNAME>` | Il nome del cluster HDInsight. |
| `<PASSWORD>` | Password scelta per l'accesso al cluster tramite SSH e il dashboard di Ambari. |

Il frammento di codice seguente esegue i passaggi iniziali seguenti:

1. Accede all'account Azure.
1. Imposta la sottoscrizione attiva in cui verranno eseguite le operazioni di creazione.
1. Consente di creare un nuovo gruppo di risorse per le nuove attività di distribuzione.
1. Crea un'identità gestita assegnata dall'utente.
1. Aggiunge un'estensione all'interfaccia della riga di comando di Azure per usare le funzionalità per Data Lake Storage Gen2.
1. Crea un nuovo account di archiviazione con Data Lake Storage Gen2 usando il `--hierarchical-namespace true` flag.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Accedere quindi al portale. Aggiungere la nuova identità gestita assegnata dall'utente al ruolo di **collaboratore dati BLOB di archiviazione** nell'account di archiviazione. Questo passaggio è descritto nel passaggio 3 in [uso del portale di Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Verificare che l'account di archiviazione disponga dell'identità assegnata dall'utente con le autorizzazioni del ruolo **collaboratore dati BLOB di archiviazione** . in caso contrario, la creazione del cluster non riuscirà

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

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

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passaggi successivi

È stato creato un cluster HDInsight. È ora possibile procedere e scoprire come utilizzare il cluster.

### <a name="apache-spark-clusters"></a>Cluster Apache Spark

* [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md)
* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Cluster Apache HBase

* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)