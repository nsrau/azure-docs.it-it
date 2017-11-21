---
title: Copiare dati in un archivio BLOB usando Azure Data Factory | Microsoft Docs
description: Creare una data factory di Azure per copiare dati da una cartella a un'altra cartella in una posizione diversa dello stesso archivio BLOB.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 8ee2f48db009da4660a03f91194c4e99f6ecac4a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Creare una data factory di Azure con PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](quickstart-create-data-factory-powershell.md)

Questa guida introduttiva descrive come usare PowerShell per creare una data factory di Azure. La pipeline creata in questa data factory copia dati da una cartella a un'altra in un archivio BLOB di Azure. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere l'[esercitazione sulla trasformazione dei dati con Spark](transform-data-using-spark.md). 

Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa guida introduttiva si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati sia di **origine** che di **destinazione/sink**. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. 

#### <a name="get-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa guida introduttiva si usano il nome e la chiave dell'account di archiviazione di Azure. La procedura seguente illustra i passaggi per recuperare il nome e la chiave dell'account di archiviazione. 

1. Avviare un Web browser e passare al [portale di Azure](https://portal.azure.com). Eseguire l'accesso con il nome utente e la password di Azure. 
2. Fare clic su **Altri servizi** nel menu a sinistra, usare la parola chiave **Archiviazione** come filtro e selezionare **Account di archiviazione**.

    ![Cercare l'account di archiviazione](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. Nell'elenco degli account di archiviazione filtrare, se necessario, e quindi selezionare il **proprio account di archiviazione**. 
4. Nella pagina **Account di archiviazione** selezionare **Chiavi di accesso** nel menu.

    ![Recuperare il nome e la chiave dell'account di archiviazione](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Copiare i valori dei campi **Nome account di archiviazione** e **key1** negli Appunti. Incollarli in un blocco note o in qualsiasi altro editor e salvare.  

#### <a name="create-input-folder-and-files"></a>Creare la cartella e i file di input
In questa sezione si crea un contenitore BLOB denominato adftutorial nell'archivio BLOB di Azure. Si crea quindi una cartella denominata input nel contenitore e si carica un file di esempio in tale cartella. 

1. Installare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), se non è già presente nel computer. 
2. Avviare **Microsoft Azure Storage Explorer** nel computer.   
3. Nella finestra **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) selezionare **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e fare clic su **Next** (Avanti). Se la finestra **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) non viene visualizzata, fare clic con il pulsante destro del mouse su **Storage Accounts** (Account di archiviazione) nella visualizzazione struttura ad albero e scegliere **Connect to Azure storage** (Connetti ad Archiviazione di Azure). 

    ![Connessione ad Archiviazione di Azure](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. Nella finestra **Attach using Name and Key** (Collega con nome e chiave) incollare il **nome** e la **chiave dell'account** salvati nel passaggio precedente. Quindi fare clic su **Next**. 
5. Nella finestra **Connection Summary** (Riepilogo connessione) fare clic su **Connect** (Connetti).
6. Verificare che l'account di archiviazione sia incluso nella visualizzazione struttura ad albero sotto **(Local and Attached)** (Locale e collegato) -> **Storage Accounts** (Account di archiviazione). 
7. Espandere **Blob Containers** (Contenitori BLOB) e verificare che il contenitore BLOB **adftutorial** non sia presente. Se esiste già, ignorare i passaggi successivi per la creazione del contenitore. 
8. Fare clic con il pulsante destro del mouse su **Blob Containers** (Contenitori BLOB) e scegliere **Create Blob Container** (Crea contenitore BLOB) dal menu di scelta rapida.

    ![Creare un contenitore BLOB](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. Immettere **adftutorial** come nome e premere **INVIO**. 
10. Verificare che il contenitore **adftutorial** sia selezionato nella visualizzazione struttura ad albero. 
11. Fare clic su **New Folder** (Nuova cartella) sulla barra degli strumenti. 

    ![Pulsante per la creazione di una cartella](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. Nella finestra **Create New Virtual Directory** (Crea nuova directory virtuale) immettere **input** in **Name** (Nome) e fare clic su **OK**. 

    ![Finestra di dialogo per la creazione di una directory](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. Avviare il **Blocco note** e creare un file denominato **emp.txt** con il contenuto seguente: 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    Salvarlo nella cartella **c:\ADFv2QuickStartPSH**. Se non esiste già, creare la cartella **ADFv2QuickStartPSH**. 
14. Fare clic sul pulsante **Upload** (Carica) sulla barra degli strumenti e selezionare **Upload Files** (Carica file). 

    ![Pulsante Carica](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. Nella finestra **Upload files** (Carica file) selezionare `...` in corrispondenza di **File**. 
16. Nella finestra **Select folder to upload** (Seleziona cartella da caricare) passare alla cartella contenente **emp.txt** e selezionare il file. 

    ![Finestra di dialogo per il caricamento di file](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. Nella finestra **Upload files** (Carica file) fare clic su **Upload** (Carica). 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Installare Azure PowerShell
Installare l'ultima versione di Azure PowerShell, se non è già presente nel computer. 

1. Nel Web browser passare alla pagina relativa a [SDK e download per Azure SDK](https://azure.microsoft.com/downloads/). 
2. Fare clic su **Windows - Installazione** nella sezione **Strumenti da riga di comando** -> **PowerShell**. 
3. Per installare Azure PowerShell, eseguire il file **MSI**. 

Per istruzioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Accedere ad Azure PowerShell
Avviare **PowerShell** nel computer. Tenere aperto Azure PowerShell fino al termine di questa guida introduttiva. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

1. Eseguire questo comando e immettere il nome utente e la password di Azure usati per accedere al portale di Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se si hanno più sottoscrizioni di Azure, eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Definire una variabile per il nome della data factory utilizzabile in seguito nei comandi di PowerShell. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Definire una variabile per la località della data factory: 

    ```powershell
    $location = "East US"
    ```
4. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` e riprovare. Se si vuole condividere il gruppo di risorse, procedere al passaggio successivo. 
5. Per creare la data factory, eseguire questo cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Per creare istanze di Data Factory è necessario essere un **collaboratore** o un **amministratore** della sottoscrizione di Azure.
* Data Factory versione 2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

## <a name="create-a-linked-service"></a>Creare un servizio collegato

Creare servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa guida introduttiva è necessario creare solo un servizio collegato Archiviazione di Azure come archivio sink e di origine della copia, denominato "AzureStorageLinkedService" nell'esempio.

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFv2QuickStartPSH** con il contenuto seguente. Creare la cartella ADFv2QuickStartPSH se non esiste già. 

    > [!IMPORTANT]
    > Sostituire &lt;accountname&gt; e &lt;accountkey&gt; con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. In **Azure PowerShell** passare alla cartella **ADFv2QuickStartPSH**.

3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Creare un set di dati

È necessario definire un set di dati che rappresenta i dati da copiare da un'origine a un sink. In questo esempio il set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente. Il set di dati accetta un parametro il cui valore è impostato in un'attività che utilizza il set di dati. Il parametro viene usato per costruire la proprietà **folderPath** che punta al percorso in cui si trovano e sono archiviati i dati.

1. Creare un file JSON denominato **BlobDataset.json** nella cartella **C:\ADFv2QuickStartPSH**, con il contenuto seguente:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Per creare il set di dati **BlobDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
  
In questo esempio la pipeline contiene un'attività e accetta due parametri, il percorso del BLOB di input e il percorso del BLOB di output. I valori per questi parametri vengono impostati all'esecuzione/attivazione della pipeline. L'attività di copia usa lo stesso set di dati del BLOB creato nel passaggio precedente come input e output. Quando il set di dati viene usato come set di dati di input, viene specificato il percorso di input. Quando il set di dati viene usato come set di dati di output, viene specificato il percorso di output. 

1. Creare un file JSON denominato **Adfv2QuickStartPipeline.json** nella cartella **C:\ADFv2QuickStartPSH**, con il contenuto seguente:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Per creare la pipeline **Adfv2QuickStartPipeline**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

In questo passaggio impostare valori per i parametri della pipeline **inputPath** e **outputPath** con gli effettivi valori dei percorsi dei BLOB di origine e sink. È quindi possibile creare un'esecuzione della pipeline usando questi argomenti. 

1. Creare un file JSON denominato **PipelineParameters.json** nella cartella **C:\ADFv2QuickStartPSH**, con il contenuto seguente:

    Se si usano contenitori e cartelle diversi, sostituire i valori di **inputPath** e **outputPath** con il percorso dei BLOB di origine e sink.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```

2. Eseguire il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** per creare un'esecuzione della pipeline e passare i valori di parametro. Viene anche acquisito l'ID di esecuzione della pipeline per il monitoraggio futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

1. Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    Ecco l'output di esempio dell'esecuzione della pipeline:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. Eseguire lo script seguente per recuperare i dettagli sull'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Assicurarsi di visualizzare un output simile all'output di esempio seguente come risultato dell'esecuzione dell'attività:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output nel contenitore BLOB adftutorial, quindi copia il file emp.txt dalla cartella di input a quella di output. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che i BLOB in inputBlobPath siano stati copiati in outputBlobPath. 

## <a name="clean-up-resources"></a>Pulire le risorse
È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. Si può eliminare il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md), in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la data factory creata in questa esercitazione.

Eseguire il comando seguente per eliminare l'intero gruppo di risorse: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Eseguire il comando seguente per eliminare solo la data factory: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md). 
