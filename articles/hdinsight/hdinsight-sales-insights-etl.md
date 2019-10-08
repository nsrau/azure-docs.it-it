---
title: 'Esercitazione: Creare una pipeline end-to-end di estrazione, trasformazione e caricamento (ETL, extraction, transformation, loading) per derivare informazioni di vendita dai dati'
description: Informazioni su come creare pipeline di estrazione, trasformazione e caricamento con Azure HDInsight per derivare informazioni dai dati di vendita tramite cluster Spark su richiesta e Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695706"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Esercitazione: Creare una pipeline di dati end-to-end per derivare informazioni di vendita

In questa esercitazione verrà creata una pipeline di dati end-to-end che esegue operazioni di estrazione, trasformazione e caricamento. La pipeline userà cluster di Apache Spark e Apache Hive in esecuzione in Azure HDInsight per eseguire query e manipolare i dati. Le altre tecnologie usate includono Data Lake Storage Gen2 per l'archiviazione e Power BI per la visualizzazione dei dati.

Questa pipeline di dati combina i dati di tutti i diversi archivi, rimuove i dati indesiderati, aggiunge i nuovi dati e li ricarica nello spazio di archiviazione per la visualizzazione di informazioni aziendali. Per altre informazioni sulle pipeline ETL, vedere [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md).

![Architettura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Scaricare [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) per visualizzare informazioni aziendali al termine dell'esercitazione.

## <a name="create-resources"></a>Creare le risorse

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonare il repository con script e dati

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Aprire Cloud Shell dalla barra dei menu superiore. Selezionare la propria sottoscrizione per creare una condivisione file, se richiesto da Azure Cloud Shell.

    ![Aprire Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Scegliere "Bash" dal menu a discesa "Seleziona ambiente".
1. Accedere all'account Azure e impostare la sottoscrizione. 
1. Configurare il gruppo di risorse per il progetto.
    1. Scegliere un nome univoco di gruppo di risorse.
    1. Eseguire il frammento di codice seguente in Azure Cloud Shell per impostare le variabili che verranno usate nei passaggi successivi

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Scaricare i dati e gli script per questa esercitazione dal [repository ETL di informazioni di vendita HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) digitando i comandi seguenti in Cloud Shell:

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. Digitare `ls` al prompt della shell per verificare che sono stati creati i file e le directory seguenti:

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuire le risorse di Azure necessarie per la pipeline 

1. Aggiungere autorizzazioni di esecuzione per lo script `chmod +x scripts/*.sh`
1. Usare il comando `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` per eseguire lo script per distribuire le risorse seguenti in Azure:

    1. Un account di archiviazione BLOB di Azure, che conterrà i dati di vendita aziendali
    2. Un account Azure Data Lake Storage Gen2, che fungerà da account di archiviazione per entrambi i cluster di HDInsight. Per altre informazioni su HDInsight e Data Lake Storage Gen2, vedere [Integrazione di Azure HDInsight con Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. Un'identità gestita assegnata dall'utente, ossia un account che concede ai cluster di HDInsight l'accesso all'account Data Lake Storage Gen2.
    4. Un cluster di Apache Spark, che verrà usato per pulire e trasformare i dati non elaborati
    5. Un cluster di Apache Hive Interactive Query, che consentirà di eseguire query sui dati di vendita e di visualizzarli con Power BI
    6. Una rete virtuale di Azure supportata da regole del gruppo di sicurezza di rete, che consentirà ai cluster di comunicare, oltre a proteggere tali comunicazioni. 

La creazione del cluster può richiedere circa 20 minuti.

Lo script `resources.sh` contiene il comando seguente, che usa un modello di Resource Manager (`resourcestemplate.json`) per creare le risorse specificate con la configurazione desiderata.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Lo script `resources.sh` carica inoltre i file CSV con i dati di vendita nell'account di archiviazione BLOB appena creato tramite il comando:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

La password predefinita usata per l'accesso SSH ai cluster è `Thisisapassword1`. Se si vuole cambiare la password, aprire il file `resourcesparameters.json` e cambiare la password per i parametri `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificare la distribuzione e raccogliere informazioni sulle risorse

1. Per controllare lo stato della distribuzione, passare al gruppo di risorse nel portale di Azure. Fare clic su **Distribuzioni** in **Impostazioni**. Fare clic sul nome della distribuzione `ResourcesDeployment`. Qui è possibile vedere le risorse che sono state distribuite e quelle ancora in corso.
1. Una volta completata la distribuzione, passare al portale di Azure > **Gruppi di risorse** > <NOME_GRUPPO_RISORSE>
1. Individuare il nuovo account di archiviazione di Azure creato per l'archiviazione dei file di vendita. Il nome dell'account di archiviazione inizia con `blob` seguito da una stringa casuale. 
    1. Prendere nota del nome dell'account di archiviazione, che servirà in seguito.
    1. Fare clic sul nome dell'account di archiviazione BLOB.
    1. Sul lato sinistro del portale fare clic su **Chiavi di accesso** in **Impostazioni**.
    1. Copiare la stringa nella casella **Key1** e salvarla per uso successivo.
1. Individuare l'account Data Lake Storage Gen2 creato come account di archiviazione per i cluster di HDInsight. Questo account si trova nello stesso gruppo di risorse dell'account di archiviazione BLOB, ma inizia con `adlsgen2`.
    1. Prendere nota del nome dell'account Data Lake Storage Gen2.
    1. Fare clic sul nome dell'account Data Lake Storage Gen2.
    1. Sul lato sinistro del portale fare clic su **Chiavi di accesso** in **Impostazioni**
    1. Copiare la stringa nella casella **Key1** e salvarla per uso successivo.

> [!Note]
> Dopo aver individuato i nomi degli account di archiviazione, è anche possibile ottenere le chiavi degli account usando il comando seguente al prompt di Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Creare un'istanza di Azure Data Factory

Azure Data Factory è uno strumento che consente di automatizzare Azure Pipelines. Non è l'unico modo per completare queste attività, ma è ideale per automatizzare questi processi. Per altre informazioni su Azure Data Factory, vedere la [Documentazione di Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Questa istanza di Azure Data Factory avrà una pipeline con due attività: 

1. La prima attività copierà i dati dall'account di archiviazione BLOB di Azure all'account di archiviazione di Data Lake Storage Gen 2 per simulare l'inserimento di dati.
2. La seconda attività trasformerà i dati nel cluster di Spark. Lo script trasforma i dati rimuovendo le colonne indesiderate, nonché aggiungendo una nuova colonna che calcola i ricavi generati da una singola transazione.

Per configurare la pipeline di Azure Data Factory, eseguire lo script `adf.sh`:

1. Aggiungere le autorizzazioni di esecuzione per il file usando `chmod +x adf.sh`
1. Eseguire lo script con `./adf.sh` 

Questo script esegue le operazioni seguenti:

1. Crea un'entità servizio con le autorizzazioni di `Storage Blob Data Contributor` per l'account di archiviazione di Data Lake Storage Gen2.
1. Ottiene un token di autenticazione per autorizzare richieste POST all'[API REST FileSystem di Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Inserisce il nome effettivo dell'account di archiviazione di Data Lake Storage Gen2 nei file `sparktransform.py` e `query.hql`.
1. Ottiene le chiavi di archiviazione per l'account di archiviazione di Data Lake Storage Gen2 e l'account di archiviazione BLOB.
1. Crea un'altra distribuzione di risorse per creare una pipeline di Azure Data Factory, con le attività e i servizi collegati associati. Passa le chiavi di archiviazione come parametri al file modello in modo che i servizi collegati possano accedere correttamente agli account di archiviazione.

La pipeline di Azure Data Factory viene distribuita con il comando seguente:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Eseguire la pipeline di dati

### <a name="trigger-the-adf-activities"></a>Attivare le attività di Azure Data Factory

La prima attività della pipeline di Azure Data Factory creata sposta i dati dall'account di archiviazione BLOB a Data Lake Storage Gen2. La seconda attività applica le trasformazioni Spark sui dati e salva i file CSV trasformati in una nuova posizione. Il completamento dell'intera pipeline può richiedere alcuni minuti.

Per attivare le pipeline, è possibile:

1. Eseguire i comandi seguenti per attivare le pipeline di Azure Data Factory in PowerShell. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. È anche possibile aprire l'istanza di Data Factory, selezionare Crea e monitora e attivare la pipeline di copia, quindi la pipeline Spark dal portale. Per informazioni sull'attivazione di pipeline tramite il portale, vedere [Creare cluster Apache Hadoop on demand in HDInsight con Azure Data Factory](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline).

Per verificare che le pipeline siano state eseguite, è possibile eseguire una delle procedure seguenti:

1. Passare alla sezione **Monitor** di Azure Data Factory tramite il portale.
2. Aprire lo strumento di esplorazione dell'account di archiviazione di Data Lake Storage Gen 2, passare al file system `files`, quindi alla cartella `transformed` e controllarne il contenuto per verificare se la pipeline è stata completata.

Per informazioni su come trasformare i dati in altri modi con HDInsight, vedere l'articolo sull'uso di [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Creare una tabella nel cluster di Interactive Query per visualizzare i dati in Power BI

1. Copiare il file query.hql nel cluster LLAP tramite SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Usare SSH per accedere al cluster LLAP usando il comando seguente e quindi immettere la password. Se il file `resourcesparameters.json` non è stato modificato, la password è `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Usare il comando seguente per eseguire lo script:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Questo script creerà una tabella gestita nel cluster di Interactive Query accessibile da Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Creare un dashboard di Power BI dai dati di vendita

1. Aprire Power BI Desktop
1. Selezionare **Recupera dati**.
1. Cercare il **cluster di HDInsight Interactive Query**.
1. Incollare qui l'URI del cluster. Dovrebbe essere in formato `https://<LLAP CLUSTER NAME>.azurehdinsight.net`. Digitare `default` per il database.
1. Immettere il nome utente e la password che si usano per accedere al cluster.

Una volta caricati i dati, è possibile sperimentare con il dashboard che si vuole creare. Per iniziare a usare i dashboard di Power BI, seguire questi collegamenti:

* [Introduzione ai dashboard di Power BI per i progettisti](https://docs.microsoft.com/power-bi/service-dashboards)
* [Esercitazione: Introduzione al servizio Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare tutte le risorse seguendo questa procedura in modo da non incorrere nei relativi costi.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md)
