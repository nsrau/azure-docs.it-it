---
title: 'Esercitazione: Creare una pipeline ETL end-to-end per derivare le informazioni dettagliate di vendita in Azure HDInsight'
description: Informazioni su come creare pipeline di estrazione, trasformazione e caricamento con Azure HDInsight per derivare informazioni dai dati di vendita tramite cluster Spark su richiesta e Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 1031c34a44a253c7458ef78c6371b88014e882ed
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746481"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Esercitazione: Creare una pipeline di dati end-to-end per derivare le informazioni dettagliate di vendita in Azure HDInsight

In questa esercitazione verrà creata una pipeline di dati end-to-end che esegue operazioni di estrazione, trasformazione e caricamento (ETL). La pipeline userà cluster di [Apache Spark](./spark/apache-spark-overview.md) e Apache Hive in esecuzione in Azure HDInsight per eseguire query e modificare i dati. Le altre tecnologie usate includono Data Lake Storage Gen2 per l'archiviazione dei dati e Power BI per la visualizzazione.

Questa pipeline di dati combina i dati da vari archivi, rimuove i dati indesiderati, aggiunge i nuovi dati e li ricarica nello spazio di archiviazione per la visualizzazione di informazioni dettagliate aziendali. Per altre informazioni sulle pipeline ETL, vedere [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md).

![Architettura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure (almeno la versione 2.2.0). Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

* jq, un processore JSON da riga di comando.  Vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Membro del [ruolo predefinito di Azure - proprietario](../role-based-access-control/built-in-roles.md).

* Se si usa PowerShell per attivare la pipeline di Data Factory, sarà necessario il [modulo az](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) per visualizzare le informazioni aziendali dettagliate al termine dell'esercitazione.

## <a name="create-resources"></a>Creare le risorse

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonare il repository con script e dati

1. Accedere alla sottoscrizione di Azure. Se si prevede di usare Azure Cloud Shell, fare clic su **Prova** nell'angolo superiore destro del blocco di codice. In caso contrario, immettere il comando seguente:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Assicurarsi di essere un membro del ruolo [proprietario](../role-based-access-control/built-in-roles.md) di Azure. Sostituire `user@contoso.com` con l'account e quindi immettere il comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se non viene restituito alcun record, non si è membri e non sarà possibile completare questa esercitazione.

1. Scaricare i dati e gli script per questa esercitazione dal [repository ETL di informazioni di vendita HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Immettere il comando seguente:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Assicurarsi che la creazione di `salesdata scripts templates` sia riuscita. Per verificare, eseguire il comando seguente:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuire le risorse di Azure necessarie per la pipeline

1. Per aggiungere le autorizzazioni di esecuzione per tutti gli script, immettere:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Impostare la variabile per il gruppo di risorse. Sostituire `RESOURCE_GROUP_NAME` con il nome di un gruppo di risorse nuovo o esistente e quindi immettere il comando:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Eseguire lo script. Sostituire `LOCATION` con un valore desiderato e quindi immettere il comando:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    In caso di dubbi su quale area specificare, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [az account list-locations](/cli/azure/account#az-account-list-locations).

    Il comando distribuirà le risorse seguenti:

    * Account di archiviazione BLOB di Azure. Questo account conterrà i dati di vendita aziendali.
    * Account Azure Data Lake Storage Gen2. Questo account fungerà da account di archiviazione per entrambi i cluster di HDInsight. Per altre informazioni su HDInsight e Data Lake Storage Gen2, vedere [Integrazione di Azure HDInsight con Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Identità gestita assegnata dall'utente. Questo account concede ai cluster di HDInsight l'accesso all'account Data Lake Storage Gen2.
    * Un cluster Apache Spark. Questo cluster verrà usato per pulire e trasformare i dati non elaborati.
    * Cluster Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md). Questo cluster consentirà di eseguire query sui dati di vendita e di visualizzarli con Power BI.
    * Rete virtuale di Azure supportata da regole del gruppo di sicurezza di rete. Questa rete virtuale consente ai cluster di comunicare e protegge le comunicazioni.

La creazione del cluster può richiedere circa 20 minuti.

La password predefinita usata per l'accesso SSH ai cluster è `Thisisapassword1`. Se si vuole cambiare la password, passare al file `./templates/resourcesparameters_remainder.json` e cambiare la password per i parametri `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificare la distribuzione e raccogliere informazioni sulle risorse

1. Per controllare lo stato della distribuzione, passare al gruppo di risorse nel portale di Azure. In **Impostazioni** selezionare **Distribuzioni** e quindi la propria distribuzione. Qui è possibile osservare le risorse che sono state distribuite e quelle ancora in fase di distribuzione.

1. Per visualizzare i nomi dei cluster, immettere il comando seguente:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Per visualizzare l'account di archiviazione e la chiave di accesso di Azure, immettere il comando seguente:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Per visualizzare l'account Data Lake Storage Gen2 e la chiave di accesso, immettere il comando seguente:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Creare una data factory

Azure Data Factory è uno strumento che consente di automatizzare Azure Pipelines. Non è l'unico modo per completare queste attività, ma è ideale per automatizzare i processi. Per altre informazioni su Azure Data Factory, vedere la [Documentazione di Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Questa data factory conterrà una pipeline con due attività:

* La prima attività copierà i dati dall'account di archiviazione BLOB di Azure all'account di archiviazione Data Lake Storage Gen 2 per simulare l'inserimento di dati.
* La seconda attività trasformerà i dati nel cluster di Spark. Lo script trasforma i dati rimuovendo le colonne non desiderate. Aggiunge anche una nuova colonna che calcola i ricavi generati da una singola transazione.

Per configurare la pipeline di Azure Data Factory, eseguire il comando seguente:  La directory attiva dovrebbe ancora essere `hdinsight-sales-insights-etl`.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Questo script esegue le operazioni seguenti:

1. Crea un'entità servizio con le autorizzazioni di `Storage Blob Data Contributor` per l'account di archiviazione di Data Lake Storage Gen2.
1. Ottiene un token di autenticazione per autorizzare richieste POST all'[API REST file system di Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Inserisce il nome effettivo dell'account di archiviazione di Data Lake Storage Gen2 nei file `sparktransform.py` e `query.hql`.
1. Ottiene le chiavi di archiviazione per l'account di archiviazione di Data Lake Storage Gen2 e l'account di archiviazione BLOB.
1. Crea un'altra distribuzione di risorse per creare una pipeline di Azure Data Factory, con le attività e i servizi collegati associati. Passa le chiavi di archiviazione come parametri al file modello in modo che i servizi collegati possano accedere correttamente agli account di archiviazione.

## <a name="run-the-data-pipeline"></a>Eseguire la pipeline di dati

### <a name="trigger-the-data-factory-activities"></a>Attivare le attività di Data Factory

La prima attività della pipeline di Data Factory creata sposta i dati dall'account di archiviazione BLOB a Data Lake Storage Gen2. La seconda attività applica le trasformazioni Spark sui dati e salva i file CSV trasformati in una nuova posizione. Il completamento dell'intera pipeline può richiedere alcuni minuti.

Per recuperare il nome della data factory, immettere il comando seguente:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Per attivare la pipeline, è possibile:

* Attivare la pipeline di Data Factory in PowerShell. Sostituire `RESOURCEGROUP` e `DataFactoryName` con i valori appropriati e quindi eseguire i comandi seguenti:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Se necessario, eseguire nuovamente `Get-AzDataFactoryV2PipelineRun` per monitorare lo stato.

    Oppure

* Aprire la data factory e fare clic su **Crea e monitora** . Attivare la pipeline `IngestAndTransform` dal portale. Per informazioni su come attivare le pipeline tramite il portale, vedere [Creare cluster Apache Hadoop su richiesta in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Per verificare che la pipeline sia stata eseguita, seguire una di queste procedure:

* Passare alla sezione **Monitoraggio** della data factory tramite il portale.
* In Azure Storage Explorer passare all'account di archiviazione Data Lake Storage Gen 2. Passare al file system `files`, quindi alla cartella `transformed` e verificarne il contenuto per scoprire se l'esecuzione della pipeline è riuscita.

Per informazioni su come trasformare i dati in altri modi con HDInsight, vedere [questo articolo sull'uso di Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Creare una tabella nel cluster di Interactive Query per visualizzare i dati in Power BI

1. Copiare il file `query.hql` nel cluster LLAP tramite SCP. Immettere il comando:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Promemoria: La password predefinita è `Thisisapassword1`.

1. Usare SSH per accedere al cluster LLAP. Immettere il comando:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Usare questo comando per eseguire lo script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Questo script creerà una tabella gestita nel cluster di Interactive Query accessibile da Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Creare un dashboard di Power BI dai dati di vendita

1. Aprire Power BI Desktop.

1. Dal menu passare a **Recupera dati** > **Altro** > **Azure** > **HDInsight Interactive Query** .

1. Selezionare **Connetti** .

1. Nella finestra di dialogo **HDInsight Interactive Query** :
    1. Nella casella di testo **Server** immettere il nome del cluster LLAP in formato `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Nella casella di testo **database** immettere `default`.
    1. Selezionare **OK** .

1. Nella finestra di dialogo **AzureHive** :
    1. Nella casella di testo **Nome utente** immettere `admin`.
    1. Nella casella di testo **Password** immettere `Thisisapassword1`.
    1. Selezionare **Connetti** .

1. In **Strumento di navigazione** selezionare `sales` e/o `sales_raw` per visualizzare un'anteprima dei dati. Una volta caricati i dati, è possibile sperimentare con il dashboard che si vuole creare. Per iniziare a usare i dashboard di Power BI, vedere questi collegamenti:

* [Introduzione ai dashboard di Power BI per i progettisti](https://docs.microsoft.com/power-bi/service-dashboards)
* [Esercitazione: Introduzione al servizio Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare tutte le risorse usando questo comando per evitare che vengano addebitati i relativi costi.

1. Per rimuovere il gruppo di risorse, immettere il comando:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Per rimuovere l'entità servizio, immettere i comandi seguenti:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md)
