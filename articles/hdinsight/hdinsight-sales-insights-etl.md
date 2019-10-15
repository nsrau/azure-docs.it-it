---
title: 'Esercitazione: Creare una pipeline ETL end-to-end per derivare informazioni di vendita'
description: Informazioni su come creare pipeline di estrazione, trasformazione e caricamento con Azure HDInsight per derivare informazioni dai dati di vendita tramite cluster Spark su richiesta e Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: b9bcaf4b7497e8beba377eb7e47a44a6eb061299
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178016"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Esercitazione: Creare una pipeline di dati end-to-end per derivare informazioni di vendita

In questa esercitazione verrà creata una pipeline di dati end-to-end che esegue operazioni di estrazione, trasformazione e caricamento (ETL). La pipeline userà cluster di Apache Spark e Apache Hive in esecuzione in Azure HDInsight per eseguire query e manipolare i dati. Le altre tecnologie usate includono Data Lake Storage Gen2 per l'archiviazione dei dati e Power BI per la visualizzazione.

Questa pipeline di dati combina i dati da vari archivi, rimuove i dati indesiderati, aggiunge i nuovi dati e li ricarica nello spazio di archiviazione per la visualizzazione di informazioni dettagliate aziendali. Per altre informazioni sulle pipeline ETL, vedere [Estrarre, trasformare e caricare (ETL) su larga scala](./hadoop/apache-hadoop-etl-at-scale.md).

![Architettura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Scaricare [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) per visualizzare informazioni aziendali al termine dell'esercitazione.

## <a name="create-resources"></a>Creare le risorse

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonare il repository con script e dati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire Azure Cloud Shell dalla barra dei menu superiore. Selezionare la sottoscrizione per creare una condivisione file, se richiesto da Cloud Shell.

   ![Aprire Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Scegliere **Bash** dal menu a discesa **Seleziona ambiente**.
1. Accedere all'account Azure e impostare la sottoscrizione. 
1. Configurare il gruppo di risorse per il progetto.
   1. Scegliere un nome univoco per il gruppo di risorse.
   1. Eseguire il frammento di codice seguente in Cloud Shell per impostare le variabili che verranno usate nei passaggi successivi:

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

1. Digitare `ls` al prompt della shell per verificare che siano stati creati i file e le directory seguenti:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuire le risorse di Azure necessarie per la pipeline 

1. Aggiungere le autorizzazioni di esecuzione per lo script `chmod +x scripts/*.sh`
1. Usare il comando `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` per eseguire lo script per distribuire le risorse seguenti in Azure:

   1. Account di archiviazione BLOB di Azure. Questo account conterrà i dati di vendita aziendali.
   2. Account Azure Data Lake Storage Gen2. Questo account fungerà da account di archiviazione per entrambi i cluster di HDInsight. Per altre informazioni su HDInsight e Data Lake Storage Gen2, vedere [Integrazione di Azure HDInsight con Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. Identità gestita assegnata dall'utente. Questo account concede ai cluster di HDInsight l'accesso all'account Data Lake Storage Gen2.
   4. Un cluster Apache Spark. Questo cluster verrà usato per pulire e trasformare i dati non elaborati.
   5. Cluster Apache Hive Interactive Query. Questo cluster consentirà di eseguire query sui dati di vendita e di visualizzarli con Power BI.
   6. Rete virtuale di Azure supportata da regole del gruppo di sicurezza di rete. Questa rete virtuale consente ai cluster di comunicare e protegge le comunicazioni. 

La creazione del cluster può richiedere circa 20 minuti.

Lo script `resources.sh` contiene il comando seguente. Questo comando usa un modello di Azure Resource Manager (`resourcestemplate.json`) per creare le risorse specificate con la configurazione desiderata.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Lo script `resources.sh` carica inoltre i file CSV con i dati di vendita nell'account di archiviazione BLOB appena creato tramite questo comando:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

La password predefinita usata per l'accesso SSH ai cluster è `Thisisapassword1`. Se si vuole cambiare la password, passare al file `resourcesparameters.json` e cambiare la password per i parametri `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificare la distribuzione e raccogliere informazioni sulle risorse

1. Per controllare lo stato della distribuzione, passare al gruppo di risorse nel portale di Azure. Fare clic su **Distribuzioni** in **Impostazioni**. Selezionare il nome della distribuzione, `ResourcesDeployment`. Qui è possibile osservare le risorse che sono state distribuite e quelle ancora in fase di distribuzione.
1. Al termine della distribuzione, passare al portale di Azure > **Gruppi di risorse** > <NOME_GRUPPO_RISORSE>
1. Individuare il nuovo account di archiviazione di Azure creato per l'archiviazione dei file di vendita. Il nome dell'account di archiviazione inizia con `blob` seguito da una stringa casuale. Eseguire le operazioni seguenti:
   1. Prendere nota del nome dell'account di archiviazione, che servirà in seguito.
   1. Selezionare il nome dell'account di archiviazione BLOB.
   1. Sul lato sinistro del portale selezionare **Chiavi di accesso** in **Impostazioni**.
   1. Copiare la stringa nella casella **Key1** e salvarla per l'utilizzo successivo.
1. Individuare l'account Data Lake Storage Gen2 creato come account di archiviazione per i cluster di HDInsight. Questo account si trova nello stesso gruppo di risorse dell'account di archiviazione BLOB, ma inizia con `adlsgen2`. Eseguire le operazioni seguenti:
   1. Prendere nota del nome dell'account Data Lake Storage Gen2.
   1. Selezionare il nome dell'account Data Lake Storage Gen2.
   1. Sul lato sinistro del portale selezionare **Chiavi di accesso** in **Impostazioni**.
   1. Copiare la stringa nella casella **Key1** e salvarla per l'utilizzo successivo.

> [!Note]
> Dopo aver individuato i nomi degli account di archiviazione, è anche possibile ottenere le chiavi degli account usando il comando seguente al prompt di Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Creare una data factory

Azure Data Factory è uno strumento che consente di automatizzare le pipeline di Azure. Non è l'unico modo per completare queste attività, ma è ideale per automatizzare i processi. Per altre informazioni su Azure Data Factory, vedere la [Documentazione di Azure Data Factory](https://azure.microsoft.com/en-us/services/data-factory/). 

Questa data factory conterrà una pipeline con due attività: 

- La prima attività copierà i dati dall'account di archiviazione BLOB di Azure all'account di archiviazione Data Lake Storage Gen 2 per simulare l'inserimento di dati.
- La seconda attività trasformerà i dati nel cluster di Spark. Lo script trasforma i dati rimuovendo le colonne non desiderate. Aggiunge anche una nuova colonna che calcola i ricavi generati da una singola transazione.

Per configurare la pipeline di Azure Data Factory, eseguire lo script `adf.sh`:

1. Usare `chmod +x adf.sh` per aggiungere le autorizzazioni di esecuzione per il file.
1. Usare `./adf.sh` per eseguire lo script. 

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

- Eseguire i comandi seguenti per attivare le pipeline di Data Factory in PowerShell: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Aprire la data factory e fare clic su **Crea e monitora**. Attivare la pipeline di copia e quindi la pipeline di Spark dal portale. Per informazioni su come attivare le pipeline tramite il portale, vedere [Creare cluster Apache Hadoop su richiesta in HDInsight con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Per verificare che le pipeline siano state eseguite, seguire una di queste procedure:

- Passare alla sezione **Monitoraggio** della data factory tramite il portale.
- In Azure Storage Explorer passare all'account di archiviazione Data Lake Storage Gen 2. Passare al file system `files`, quindi alla cartella `transformed` e verificarne il contenuto per scoprire se l'esecuzione della pipeline è riuscita.

Per informazioni su come trasformare i dati in altri modi con HDInsight, vedere [questo articolo sull'uso di Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Creare una tabella nel cluster di Interactive Query per visualizzare i dati in Power BI

1. Copiare il file `query.hql` nel cluster LLAP tramite SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Usare SSH per accedere al cluster LLAP usando il comando seguente e quindi immettere la password. Se il file `resourcesparameters.json` non è stato modificato, la password è `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Usare questo comando per eseguire lo script:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Questo script creerà una tabella gestita nel cluster di Interactive Query accessibile da Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Creare un dashboard di Power BI dai dati di vendita

1. Aprire Power BI Desktop.
1. Selezionare **Recupera dati**.
1. Cercare il **cluster di HDInsight Interactive Query**.
1. Incollare qui l'URI del cluster. Deve essere nel formato `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

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
