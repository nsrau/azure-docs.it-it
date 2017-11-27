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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Creare una data factory di Azure con PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](quickstart-create-data-factory-powershell.md)

Questa guida introduttiva descrive come usare PowerShell per creare una data factory di Azure. La pipeline creata in questa data factory **copia** dati da una cartella a un'altra in un archivio BLOB di Azure. Per un'esercitazione su come **trasformare** i dati usando Azure Data Factory, vedere [Esercitazione: Trasformare dati usando Spark](transform-data-using-spark.md). 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure. Nel portale di Azure fare clic sul **nome utente** nell'angolo in alto a destra e selezionare **Autorizzazioni** per visualizzare le autorizzazioni disponibili nella sottoscrizione. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere l'articolo [Aggiungere i ruoli](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa guida introduttiva si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati sia di **origine** che di **destinazione**. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. 

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
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure. Si crea quindi una cartella denominata **input** nel contenitore e si carica un file di esempio in tale cartella. 

1. Nella pagina **Account di archiviazione** passare a **Panoramica** e quindi fare clic su **BLOB**. 

    ![Selezionare l'opzione BLOB](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. Nella pagina **Servizio BLOB** fare clic su **+ Contenitore** sulla barra degli strumenti. 

    ![Pulsante Aggiungi contenitore](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. Nella finestra di dialogo **Nuovo contenitore** immettere **adftutorial** come nome e fare clic su **OK**. 

    ![Immettere il nome del contenitore](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. Fare clic su **adftutorial** nell'elenco di contenitori. 

    ![Selezionare il contenitore](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. Nella pagina **Contenitore** fare clic su **Carica** sulla barra degli strumenti.  

    ![Pulsante Carica](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. Nella pagina **Carica BLOB** fare clic su **Avanzate**.

    ![Fare clic sul collegamento Avanzate](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. Avviare il **Blocco note** e creare un file denominato **emp.txt** con il contenuto seguente. Salvarlo nella cartella **c:\ADFv2QuickStartPSH**. Creare la cartella **ADFv2QuickStartPSH** se non esiste già.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Nella pagina **Carica BLOB** del portale di Azure cercare e selezionare il file **emp.txt** nel campo **File**. 
9. Immettere il valore **input** nel campo **Carica nella cartella**. 

    ![Caricare le impostazioni BLOB](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. Verificare che la cartella sia **input** e il file sia **emp.txt** e fare clic su **Carica**.
11. Verranno visualizzati il file **emp.txt** e lo stato del caricamento nell'elenco. 
12. Chiudere la pagina **Carica BLOB** facendo clic su **X** nell'angolo. 

    ![Chiudere la pagina Carica BLOB](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. Tenere aperta la pagina **Contenitore** perché verrà usata per verificare l'output alla fine di questa guida introduttiva. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Installare Azure PowerShell
Installare l'ultima versione di Azure PowerShell, se non è già presente nel computer. 

1. Nel Web browser passare alla pagina relativa a [SDK e download per Azure SDK](https://azure.microsoft.com/downloads/). 
2. Fare clic su **Windows - Installazione** nella sezione **Strumenti da riga di comando** -> **PowerShell**. 
3. Per installare Azure PowerShell, eseguire il file **MSI**. 

Per istruzioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Accedere ad Azure PowerShell

1. Avviare **PowerShell** nel computer. Tenere aperto Azure PowerShell fino al termine di questa guida introduttiva. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

    ![Avviare PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. Eseguire questo comando e immettere lo stesso nome utente e la stessa password di Azure usati per accedere al portale di Azure:
       
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
1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`.
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Definire una variabile per il nome della data factory. 

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
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando. 
5. Per creare la data factory, eseguire questo cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.
* Data Factory versione 2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

## <a name="create-a-linked-service"></a>Creare un servizio collegato

Creare servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa guida introduttiva si crea un servizio collegato Archiviazione di Azure che viene usato come archivio sia di origine che sink. Il servizio collegato ha le informazioni di connessione usate dal servizio Data Factory in fase di esecuzione per la connessione.

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
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
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
In questo passaggio è necessario definire un set di dati che rappresenta i dati da copiare da un'origine a un sink. Il set di dati è di tipo **AzureBlob**. Fa riferimento al **servizio collegato Archiviazione di Azure** creato nel passaggio precedente. Accetta un parametro per costruire la proprietà **folderPath**. Per un set di dati di input, l'attività di copia nella pipeline passa il percorso di input come valore per questo parametro. Analogamente, per un set di dati di output, l'attività di copia passa il percorso di output come valore per questo parametro. 

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
  
In questa guida introduttiva si crea una pipeline con un'attività che accetta due parametri, il percorso del BLOB di input e il percorso del BLOB di output. I valori per questi parametri vengono impostati all'esecuzione/attivazione della pipeline. L'attività di copia usa lo stesso set di dati del BLOB creato nel passaggio precedente come input e output. Quando il set di dati viene usato come set di dati di input, viene specificato il percorso di input. Quando il set di dati viene usato come set di dati di output, viene specificato il percorso di output. 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Eseguire il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** per creare un'esecuzione della pipeline e passare i valori di parametro. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

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

        Start-Sleep -Seconds 10
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

    Se viene visualizzato l'errore:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    seguire anche questa procedura: 
    1. In AzureStorageLinkedService.json verificare che il nome e la chiave dell'account di archiviazione di Azure siano corretti. 
    2. Verificare che il formato della stringa di connessione sia corretto. Le proprietà AccountName e AccountKey, ad esempio, sono separate dal carattere di punto e virgola (`;`). 
    3. Se il nome account e la chiave dell'account sono racchiusi tra parentesi angolari, rimuoverle. 
    4. Ecco una stringa di connessione di esempio: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Creare di nuovo il servizio collegato seguendo i passaggi della sezione [Creare un servizio collegato](#create-a-linked-service). 
    6. Eseguire di nuovo la pipeline seguendo i passaggi della sezione [Creare un'esecuzione della pipeline](#create-a-pipeline-run). 
    7. Eseguire di nuovo il comando di monitoraggio corrente per monitorare la nuova esecuzione della pipeline. 
1. Eseguire lo script seguente per recuperare i dettagli sull'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

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
La pipeline crea automaticamente la cartella di output nel contenitore BLOB adftutorial, quindi copia il file emp.txt dalla cartella di input a quella di output. 

1. Nella pagina del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output. 
    
    ![Aggiorna](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. Fare clic su **output** nell'elenco di cartelle. 
2. Verificare che **emp.txt** venga copiato nella cartella di output. 

    ![Aggiorna](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>Pulire le risorse
È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. Si può eliminare il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md), in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la data factory creata in questa esercitazione.

Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse in esso contenute, incluse le data factory. Eseguire il comando seguente per eliminare l'intero gruppo di risorse: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Per eliminare solo la data factory e non l'intero gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md). 
