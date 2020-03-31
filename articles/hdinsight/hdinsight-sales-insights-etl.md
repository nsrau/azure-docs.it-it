---
title: 'Esercitazione: Creare una pipeline ETL end-to-end per derivare le informazioni dettagliate di vendita in Azure HDInsight'
description: Informazioni su come creare pipeline di estrazione, trasformazione e caricamento con Azure HDInsight per derivare informazioni dai dati di vendita tramite cluster Spark su richiesta e Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247266"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Esercitazione: Creare una pipeline di dati end-to-end per derivare le informazioni dettagliate di vendita in Azure HDInsight

In questa esercitazione verrà creata una pipeline di dati end-to-end che esegue operazioni di estrazione, trasformazione e caricamento (ETL). La pipeline userà cluster di [Apache Spark](./spark/apache-spark-overview.md) e Apache Hive in esecuzione in Azure HDInsight per eseguire query e modificare i dati. Le altre tecnologie usate includono Data Lake Storage Gen2 per l'archiviazione dei dati e Power BI per la visualizzazione.

Questa pipeline di dati combina i dati da vari archivi, rimuove i dati indesiderati, aggiunge i nuovi dati e li ricarica nello spazio di archiviazione per la visualizzazione di informazioni dettagliate aziendali. Per altre informazioni sulle pipeline ETL, vedere [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md).

![Architettura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure. Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Membro del [ruolo predefinito di Azure - proprietario](../role-based-access-control/built-in-roles.md).

* [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) per visualizzare le informazioni aziendali dettagliate al termine dell'esercitazione.

## <a name="create-resources"></a>Creare le risorse

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonare il repository con script e dati

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire Azure Cloud Shell dalla barra dei menu superiore. Selezionare la sottoscrizione per creare una condivisione file, se richiesto da Cloud Shell.

   ![Aprire Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. Scegliere **Bash** dal menu a discesa **Seleziona ambiente**.

1. Assicurarsi di essere un membro del ruolo [proprietario](../role-based-access-control/built-in-roles.md) di Azure. Sostituire `user@contoso.com` con l'account e quindi immettere il comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se non viene restituito alcun record, non si è membri e non sarà possibile completare questa esercitazione.

1. Per elencare le sottoscrizioni, immettere il comando:

    ```azurecli
    az account list --output table
    ```

    Prendere nota dell'ID della sottoscrizione che verrà usata per il progetto.

1. Impostare la sottoscrizione da usare per questo progetto. Sostituire `SUBSCRIPTIONID` con il valore effettivo, quindi immettere il comando.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Creare un nuovo gruppo di risorse per il progetto. Sostituire `RESOURCEGROUP` con il nome desiderato, quindi immettere il comando.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Scaricare i dati e gli script per questa esercitazione dal [repository ETL di informazioni di vendita HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl).  Immettere il comando seguente:

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

1. Eseguire lo script. Sostituire `RESOURCE_GROUP_NAME` e `LOCATION` con i valori pertinenti, quindi immettere il comando:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    Il comando distribuirà le risorse seguenti:

    * Account di archiviazione BLOB di Azure. Questo account conterrà i dati di vendita aziendali.
    * Account Azure Data Lake Storage Gen2. Questo account fungerà da account di archiviazione per entrambi i cluster di HDInsight. Per altre informazioni su HDInsight e Data Lake Storage Gen2, vedere [Integrazione di Azure HDInsight con Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Identità gestita assegnata dall'utente. Questo account concede ai cluster di HDInsight l'accesso all'account Data Lake Storage Gen2.
    * Un cluster Apache Spark. Questo cluster verrà usato per pulire e trasformare i dati non elaborati.
    * Cluster Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md). Questo cluster consentirà di eseguire query sui dati di vendita e di visualizzarli con Power BI.
    * Rete virtuale di Azure supportata da regole del gruppo di sicurezza di rete. Questa rete virtuale consente ai cluster di comunicare e protegge le comunicazioni.

La creazione del cluster può richiedere circa 20 minuti.

Lo script `resources.sh` contiene i comandi seguenti. Non è necessario eseguire questi comandi se è già stato eseguito lo script nel passaggio precedente.

* `az group deployment create` - Questo comando usa un modello di Azure Resource Manager (`resourcestemplate.json`) per creare le risorse specificate con la configurazione desiderata.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch` - Carica i file CSV con i dati di vendita nell'account di archiviazione BLOB appena creato tramite questo comando:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

La password predefinita usata per l'accesso SSH ai cluster è `Thisisapassword1`. Se si vuole cambiare la password, passare al file `resourcesparameters.json` e cambiare la password per i parametri `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificare la distribuzione e raccogliere informazioni sulle risorse

1. Per controllare lo stato della distribuzione, passare al gruppo di risorse nel portale di Azure. Fare clic su **Distribuzioni** in **Impostazioni**. Selezionare il nome della distribuzione, `ResourcesDeployment`. Qui è possibile osservare le risorse che sono state distribuite e quelle ancora in fase di distribuzione.

1. Per visualizzare i nomi dei cluster, immettere il comando seguente:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Per visualizzare l'account di archiviazione e la chiave di accesso di Azure, immettere il comando seguente:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Per visualizzare l'account Data Lake Storage Gen2 e la chiave di accesso, immettere il comando seguente:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

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

Per configurare la pipeline di Azure Data Factory, eseguire il comando seguente:

```bash
./scripts/adf.sh
```

Questo script esegue le operazioni seguenti:

1. Crea un'entità servizio con le autorizzazioni di `Storage Blob Data Contributor` per l'account di archiviazione di Data Lake Storage Gen2.
1. Ottiene un token di autenticazione per autorizzare richieste POST all'[API REST file system di Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Inserisce il nome effettivo dell'account di archiviazione di Data Lake Storage Gen2 nei file `sparktransform.py` e `query.hql`.
1. Ottiene le chiavi di archiviazione per l'account di archiviazione di Data Lake Storage Gen2 e l'account di archiviazione BLOB.
1. Crea un'altra distribuzione di risorse per creare una pipeline di Azure Data Factory, con le attività e i servizi collegati associati. Passa le chiavi di archiviazione come parametri al file modello in modo che i servizi collegati possano accedere correttamente agli account di archiviazione.

La pipeline di Data Factory viene distribuita tramite il comando seguente:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Eseguire la pipeline di dati

### <a name="trigger-the-data-factory-activities"></a>Attivare le attività di Data Factory

La prima attività della pipeline di Data Factory creata sposta i dati dall'account di archiviazione BLOB a Data Lake Storage Gen2. La seconda attività applica le trasformazioni Spark sui dati e salva i file CSV trasformati in una nuova posizione. Il completamento dell'intera pipeline può richiedere alcuni minuti.

Per attivare le pipeline, è possibile:

* Attivare le pipeline di Data Factory in PowerShell. Sostituire `DataFactoryName` con il nome effettivo della Data Factory, quindi eseguire i comandi seguenti:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    Oppure

* Aprire la data factory e fare clic su **Crea e monitora**. Attivare la pipeline di copia e quindi la pipeline di Spark dal portale. Per informazioni su come attivare le pipeline tramite il portale, vedere [Creare cluster Apache Hadoop su richiesta in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Per verificare che le pipeline siano state eseguite, seguire una di queste procedure:

* Passare alla sezione **Monitoraggio** della data factory tramite il portale.
* In Azure Storage Explorer passare all'account di archiviazione Data Lake Storage Gen 2. Passare al file system `files`, quindi alla cartella `transformed` e verificarne il contenuto per scoprire se l'esecuzione della pipeline è riuscita.

Per informazioni su come trasformare i dati in altri modi con HDInsight, vedere [questo articolo sull'uso di Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Creare una tabella nel cluster di Interactive Query per visualizzare i dati in Power BI

1. Copiare il file `query.hql` nel cluster LLAP tramite SCP. Sostituire `LLAPCLUSTERNAME` con il nome effettivo, quindi immettere il comando:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Usare SSH per accedere al cluster LLAP. Sostituire `LLAPCLUSTERNAME` con il nome effettivo, quindi immettere il comando. Se il file `resourcesparameters.json` non è stato modificato, la password è `Thisisapassword1`.

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Usare questo comando per eseguire lo script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Questo script creerà una tabella gestita nel cluster di Interactive Query accessibile da Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Creare un dashboard di Power BI dai dati di vendita

1. Aprire Power BI Desktop.
1. Selezionare **Recupera dati**.
1. Cercare il **cluster di HDInsight Interactive Query**.
1. Incollare qui l'URI del cluster. Deve essere nel formato `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Immettere `default` per il database.
1. Immettere il nome utente e la password che si usano per accedere al cluster.

Una volta caricati i dati, è possibile sperimentare con il dashboard che si vuole creare. Per iniziare a usare i dashboard di Power BI, vedere questi collegamenti:

* [Introduzione ai dashboard di Power BI per i progettisti](https://docs.microsoft.com/power-bi/service-dashboards)
* [Esercitazione: Introduzione al servizio Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare tutte le risorse usando questo comando per evitare che vengano addebitati i relativi costi.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md)
