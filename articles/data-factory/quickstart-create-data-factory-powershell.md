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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 4bbac0e82181e46b84afee5ff7601da018226ec0
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
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

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

[!INCLUDE [data-factory-quickstart-prerequisites-2](../../includes/data-factory-quickstart-prerequisites-2.md)]

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando.
2. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando. 
3. Definire una variabile per il nome della data factory. 

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. Ad esempio, ADFTutorialFactorySP1127. 

    ```powershell
    $DataFactoryName = "ADFQuickStartFactory";
    ```

5. Per creare la data factory, eseguire il cmdlet **Set-AzureRmDataFactoryV2** usando le proprietà Location e ResourceGroupName della variabile $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
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
    Se si usa Blocco note, selezionare **Tutti i file** per il campo **Tipo file** nella finestra di dialogo **Salva con nome**. In caso contrario, è possibile che venga aggiunta l'estensione `.txt` al file. ad esempio `AzureStorageLinkedService.json.txt`. Se si crea il file in Esplora file prima di aprirlo in Blocco note, è possibile che l'estensione `.txt` non venga visualizzata perché l'opzione **Nascondi estensioni per i tipi di file conosciuti** è selezionata per impostazione predefinita. Rimuovere l'estensione `.txt` prima di procedere al passaggio successivo.
2. In **PowerShell** passare alla cartella **ADFv2QuickStartPSH**.

```powershell
Set-Location 'C:\ADFv2QuickStartPSH'
```

3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
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
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
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
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
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
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

    ```powershell
    while ($True) {
        $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output  "Pipeline is running...status: InProgress"
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
    Write-Output "Activity run details:"
    $Result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
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

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md). 
