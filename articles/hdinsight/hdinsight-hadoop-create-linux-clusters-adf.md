<properties
   pageTitle="Creare cluster Hadoop on demand basati su Linux in HDInsight con Azure Data Factory | Microsoft Azure"
   	description="Informazioni su come creare cluster HDInsight on demand con Azure Data Factory."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/16/2016"
   ms.author="jgao"/>

# Creare cluster Hadoop on demand basati su Linux in HDInsight con Azure Data Factory

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) è un servizio di integrazione delle informazioni basato sul cloud che consente di automatizzare lo spostamento e la trasformazione dei dati. In questo articolo verrà spiegato come usare Azure Data Factory per creare un [servizio collegato Azure HDInsight on demand](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) e usare il cluster per eseguire un processo Hive. Ecco il flusso di alto livello:

1. Creare un cluster HDInsight on demand.
2. Eseguire un processo Hive per leggere i dati del log Web non elaborati da un account di archiviazione BLOB di origine, trasformare i dati e scrivere l'output in un account di archiviazione BLOB di destinazione. 
3. Eliminare il cluster in base all'impostazione Durata (TTL).

L'attività Hive definita nella pipeline di Data Factory richiama uno script HiveQL predefinito. Lo script crea una tabella esterna che fa riferimento ai dati del log Web non elaborati presenti nell'archiviazione BLOB di Azure e quindi esegue il partizionamento dei dati non elaborati per anno e per mese.

Ecco le righe di esempio per ogni mese nel file di input.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Lo script crea tre cartelle di output sulla base dell'input precedente. Ogni cartella contiene un file con le voci di ogni mese.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Per vedere un elenco delle attività di trasformazione dei dati di Data Factory oltre alle attività Hive, vedere [Trasformazione e analisi tramite Data Factory di Azure](../data-factory/data-factory-data-transformation-activities.md).

L'uso di HDInsight con Data Factory comporta vari vantaggi:

- I cluster HDInsight vengono fatturati con tariffa oraria, sia che vengano usati o meno. Con Data Factory, i cluster vengono creati su richiesta ed eliminati automaticamente quando i processi sono completati. Pertanto, si paga solo per la durata del processo in esecuzione e il breve tempo di inattività (TTL).
- È possibile creare un flusso di lavoro tramite la pipeline di Data Factory.
- Si possono pianificare processi ricorsivi.  

##Prerequisiti:

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- [Sottoscrizione di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Interfaccia della riga di comando di Azure](../xplat-cli-install.md) o [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater). 

##Preparare un account di archiviazione

In questo scenario, è possibile usare fino a tre account di archiviazione:

- account di archiviazione predefinito per il cluster HDInsight
- account di archiviazione per i dati di input
- account di archiviazione per i dati di output

Per semplificare l'esercitazione, si userà un solo account di archiviazione per tutti e tre gli scopi. L'interfaccia della riga di comando di Azure e lo script di esempio di Azure PowerShell di questa sezione eseguono le operazioni seguenti:

1. Accedere ad Azure.
2. Creare un gruppo di risorse di Azure.
3. Creare un account di archiviazione di Azure.
4. Creare un contenitore BLOB nell'account di archiviazione.
5. Copiare i due file seguenti nel contenitore BLOB:

    - File di dati di input: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - Script HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    Entrambi i file vengono archiviati in un contenitore BLOB pubblico.

>[AZURE.IMPORTANT] Annotare il nome del gruppo di risorse, il nome dell'account di archiviazione e la chiave dell'account di archiviazione usato nello script. Verranno usati nella sezione successiva.

**Per preparare l'archiviazione e copiare i file tramite l'interfaccia della riga di comando di Azure**

    azure login

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

Il nome del contenitore è *adfgetstarted*. Mantenerlo invariato perché altrimenti sarebbe necessario aggiornare il modello di Azure Resource Manager.

Se occorre assistenza per questo script dell'interfaccia della riga di comando, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](../storage/storage-azure-cli.md).

**Per preparare l'archiviazione e copiare i file tramite Azure PowerShell**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName |  %{ $_.Key1 }

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

Se occorre assistenza per questo script di PowerShell, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md).

**Per esaminare l'account di archiviazione e il suo contenuto**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Gruppi di risorse** nel pannello di sinistra.
3. Fare doppio clic sul nome del gruppo di risorse creato con lo script di PowerShell o l'interfaccia della riga di comando. Se sono presenti troppi gruppi di risorse elencati, usare il filtro. 
4. Nel riquadro **Risorse** dovrebbe essere elencata una sola risorsa, a meno che il gruppo di risorse non sia condiviso con altri progetti. Deve essere l'account di archiviazione con il nome specificato in precedenza. Fare clic sul nome dell'account di archiviazione.
5. Fare clic sui riquadri **BLOB**.
6. Fare clic sul contenitore **adfgetstarted**. Verranno visualizzate due cartelle: **input data** e **script**.
7. Aprire le cartelle e controllare i file al loro interno.
 
## Creare un'istanza di Data Factory

Con l'account di archiviazione, i dati di input e lo script HiveQL preparato, si è pronti per creare un'istanza di Azure Data Factory. Esistono diversi metodi per creare un'istanza di Data Factory. In questa esercitazione si userà il portale di Azure per chiamare un modello di Azure Resource Manager personalizzato. In alternativa, è possibile chiamare il modello di Azure Resource Manager dall'[interfaccia della riga di comando di Azure](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) o da [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell). Per altri metodi di creazione di un'istanza di Data Factory, vedere [l'esercitazione per creare la prima istanza di Data Factory](../data-factory/data-factory-build-your-first-pipeline.md).

Il livello superiore del modello di Azure Resource Manager contiene:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Una risorsa di Data Factory denominata *hdinsight-hive-on-demand* (il nome non viene visualizzato nella schermata). Il servizio Data Factory è attualmente supportato solo negli Stati Uniti occidentali e nell'Europa settentrionale.

La risorsa *hdinsight-hive-on-demand* contiene 4 risorse:

- Un servizio collegato all'account di archiviazione che verrà usato come account di archiviazione predefinito HDInsight, come posizione di archiviazione dei dati di input e come posizione archiviazione dei dati di output.
- Un servizio collegato al cluster HDInsight da creare:

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    Anche se non è specificato, il cluster viene creato nella stessa area dell'account di archiviazione.
    
    Si noti l'impostazione di *timeToLive*. Data Factory elimina automaticamente il cluster dopo un periodo di inattività di 30 minuti.
- Un set di dati per i dati di input. Il nome del file e della cartella vengono definiti qui:

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- Un set di dati per i dati di output e la pipeline per l'elaborazione dei dati. Il percorso di output viene definito qui:
        
        "folderPath": "adfgetstarted/partitioneddata",

    L'impostazione [disponibilità dei set di dati](../data-factory/data-factory-create-datasets.md#Availability) è la seguente:
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    In Azure Data Factory, la disponibilità dei set di dati di output attiva la pipeline. Ciò significa che la sezione viene prodotta mensilmente l'ultimo giorno del mese. Per altre informazioni, vedere [Pianificazione ed esecuzione con Data factory](../data-factory/data-factory-scheduling-and-execution.md).

    La definizione della pipeline è la seguente:
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    Contiene un'attività. Le date di *inizio* e *fine* dell'attività sono entrambe già passate, il che significa che sarà presente una sola sezione. Se la data finale è futura, Data Factory creerà un'altra sezione a tempo debito. Per altre informazioni, vedere [Pianificazione ed esecuzione con Data factory](../data-factory/data-factory-scheduling-and-execution.md).

    La definizione dell'attività è la seguente:
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    I percorsi di input, output e dello script sono definiti.
    
**Per creare un'istanza di Data Factory**

1. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure. Il modello si trova in https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/it-IT/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** e **STORAGEACCOUNTKEY** per l'account creato all'ultima sezione e fare clic su **OK**. Il nome dell'istanza Data Factory deve essere globalmente univoco.
3. In **Gruppo di risorse** selezionare lo stesso gruppo di risorse usato nella sezione precedente.
4. Fare clic su **Note legali** e quindi su **Crea**.
5. Fare clic su **Crea**. Nel Dashboard sarà presente il riquadro **Distribuzione modello**. Attendere finché il testo del riquadro non viene sostituito dal nome del gruppo di risorse. Possono essere necessari circa 20 minuti per creare un cluster HDInsight.
6. Fare clic sul riquadro per aprire il gruppo di risorse. Ora sarà visibile un'altra risorsa Data Factory oltre alla risorsa dell'account di archiviazione.
7. Fare clic su **hdinsight-hive-on-demand**.
8. Fare clic sul riquadro **Diagramma** . Il diagramma mostra un'attività con un set di dati di input e un set di dati di output:

    ![Diagramma della pipeline attività Hive di HDInsight on demand in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    I nomi sono definiti nel modello di Azure Resource Manager.
9. Fare doppio clic su **AzureBlobOutput**.
10. Tra le **sezioni aggiornate di recente** verrà visualizzata una sezione. Se lo stato è **In corso**, attendere finché non diventa **Pronto**.

**Per controllare l'output di Data Factory**

1. Per controllare il contenuto del contenitore adfgetstarted, usare la stessa procedura dell'ultima sessione. Oltre a **adfgetsarted** sono disponibili due nuovi contenitori:

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx: questo è il contenitore predefinito per il cluster HDInsight. Il nome predefinito del contenitore segue lo schema: "adf>yourdatafactoryname>-linkedservicename-datetimestamp". 
    - adfjobs: questo è il contenitore per i log dei processi ADF.
    
    L'output di Data Factory viene archiviato in afgetstarted come definito nel modello di Azure Resource Manager. 
2. Fare clic su **adfgetstarted**.
3. Fare doppio clic su **partitioneddata**. Verrà visualizzata una cartella **year = 2014** perché tutti i log Web sono datati 2014. 

    ![Output della pipeline attività Hive di HDInsight on demand in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Eseguendo il drill-down dell'elenco si vedranno 3 cartelle relative a gennaio, febbraio e marzo. È presente un log per ogni mese.

    ![Output della pipeline attività Hive di HDInsight on demand in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##Eseguire la pulizia dell'esercitazione

Con il servizio collegato HDInsight on demand viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo, timeToLive, che viene eliminato al termine dell'elaborazione. Per ogni cluster, Azure Data Factory crea una risorsa di archiviazione BLOB di Azure da usare come file system predefinito per il cluster. Anche se il cluster HDInsight viene eliminato, il contenitore di archiviazione BLOB predefinito e l'account di archiviazione associato non vengono eliminati. Si tratta di un comportamento previsto da progettazione. Man mano che vengono elaborate sempre più sezioni, verranno visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. Il nome di questi contenitori segue uno schema: "adfyourdatafactoryname-linkedservicename-datetimestamp".

[Azure Resource Manager](../resource-group-overview.md) viene usato per distribuire, gestire e monitorare la soluzione come gruppo. L'eliminazione di un gruppo di risorse eliminerà tutti i componenti all'interno del gruppo.

**Per eliminare il gruppo di risorse**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Gruppi di risorse** nel pannello di sinistra.
3. Fare doppio clic sul nome del gruppo di risorse creato con lo script di PowerShell o l'interfaccia della riga di comando. Se sono presenti troppi gruppi di risorse elencati, usare il filtro. Viene aperto il nuovo pannello ina per il gruppo di risorse.
4. Nel riquadro **Risorse** dovrebbe essere indicato l'account di archiviazione predefinito e l'istanza Data Factory, a meno che il gruppo di risorse non sia condiviso con altri progetti.
5. Fare clic su **Elimina** nella parte superiore del pannello. In questo modo si procederà all'eliminazione dell'account di archiviazione e dei dati archiviati nell'account di archiviazione.
6. Immettere il nome del gruppo di risorse e fare clic sul pulsante **Elimina**.

Se non si desidera eliminare l'account di archiviazione quando si elimina il gruppo di risorse, è possibile progettare l'architettura in modo da separare i dati aziendali dall'account di archiviazione predefinito. In questo caso, ci sarà un gruppo di risorse per l'account di archiviazione con i dati aziendali e un secondo gruppo di risorse per l'account di archiviazione predefinito e l'istanza Data Factory. L'eliminazione del secondo gruppo di risorse non influirà sull'account di archiviazione dei dati aziendali. A tale scopo, procedere come segue:

- Aggiungere quanto segue al gruppo di risorse di livello superiore insieme alla risorsa Microsoft.DataFactory/datafactories nel modello di Azure Resource Manager. Verrà creato un nuovo account di archiviazione:

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- Aggiungere un nuovo punto di servizio collegato al nuovo account di archiviazione:

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- Configurare il servizio collegato HDInsight on demand con un dependsOn e un additionalLinkedServiceNames aggiuntivi:

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##Passaggi successivi
Questo articolo ha descritto come usare Azure Data Factory per creare il cluster HDInsight on demand per l'elaborazione dei processi Hive. Altre informazioni:

- [Esercitazione di Hadoop: Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Documentazione relativa a HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Documentazione di Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0316_2016-->