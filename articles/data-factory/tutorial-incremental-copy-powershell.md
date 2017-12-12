---
title: Eseguire la copia incrementale di una tabella usando Azure Data Factory | Microsoft Docs
description: In questa esercitazione si crea una pipeline di Azure Data Factory che copia i dati in modo incrementale da un database SQL di Azure a un archivio BLOB di Azure.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: shlo
ms.openlocfilehash: 0b05971b5ab8ec3fd14dd4ce14d07df478e1dcc9
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage"></a>Caricare i dati in modo incrementale da un database SQL di Azure a un archivio BLOB di Azure
In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati differenziali da una tabella di un database SQL di Azure a una risorsa di archiviazione BLOB di Azure. 


> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati per l'archiviazione del valore limite.   
> * Creare una data factory.
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare una pipeline.
> * Eseguire la pipeline.
> * Monitorare l'esecuzione della pipeline. 

## <a name="overview"></a>Panoramica
Il diagramma generale della soluzione è il seguente: 

![Caricare i dati in modo incrementale](media\tutorial-Incrementally-copy-powershell\incrementally-load.png)

Di seguito sono descritti i passaggi fondamentali per la creazione di questa soluzione: 

1. **Selezionare la colonna del limite**.
    Selezionare una colonna nell'archivio dati di origine, che può essere usata per analizzare approfonditamente i record nuovi o aggiornati per ogni esecuzione. I dati della colonna selezionata (ad esempio last_modify_time o ID) continuano in genere ad aumentare quando le righe vengono create o aggiornate. Il valore massimo di questa colonna viene usato come limite.
2. **Preparare un archivio dati per l'archiviazione del valore limite**.   
    In questa esercitazione si archivia il valore limite in un database SQL di Azure.
3. **Creare una pipeline con il flusso di lavoro seguente:** 
    
    La pipeline di questa soluzione contiene le attività seguenti:
  
    1. Creare due attività di **ricerca**. Usare la prima attività di ricerca per recuperare l'ultimo valore limite. Usare la seconda attività di ricerca per recuperare il nuovo valore limite. Questi valori limite vengono passati all'attività di copia. 
    2. Creare un'**attività di copia** che copi le righe dell'archivio dati di origine con il valore della colonna del limite maggiore del valore limite precedente e minore del nuovo valore limite. L'attività copierà quindi i dati delta dall'archivio dati di origine a un archivio BLOB come nuovo file. 
    3. Creare un'**attività stored procedure** che aggiorni il valore limite per la pipeline che verrà eseguita la volta successiva. 


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* **Database SQL di Azure**. Usare il database come archivio dati di **origine**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati **sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. Creare un contenitore denominato **adftutorial**. 
* **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell).

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Creare una tabella di origine dati nel database SQL di Azure
1. Aprire **SQL Server Management Studio**. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.
2. Eseguire questo comando SQL sul database SQL di Azure per creare una tabella denominata `data_source_table` come archivio dell'origine dati.  
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    In questa esercitazione si usa **LastModifytime** come colonna **limite**.  I dati nell'archivio dell'origine dati sono visualizzati nella tabella seguente:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-sql-database-to-store-the-high-watermark-value"></a>Creare un'altra tabella nel database SQL per archiviare il valore del limite massimo
1. Eseguire il comando SQL seguente sul database SQL di Azure per creare una tabella denominata `watermarktable` per archiviare il valore limite.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
3. Impostare il **valore** predefinito del limite massimo con il nome tabella dell'archivio dei dati di origine.  In questa esercitazione, il nome della tabella è **data_source_table**.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
4. Esaminare i dati nella tabella: `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Output: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Creare una stored procedure nel database SQL di Azure 

Eseguire questo comando per creare una stored procedure nel database SQL di Azure.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando.
2. Definire una variabile per la località della data factory: 

    ```powershell
    $location = "East US"
    ```
3. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando. 
3. Definire una variabile per il nome della data factory. 

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. Ad esempio, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
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


## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa sezione si creano servizi collegati all'account di archiviazione di Azure e al database SQL di Azure. 

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure.
1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADF** con il contenuto seguente. Creare la cartella ADF se non esiste già. Sostituire `<accountName>` e `<accountKey>` con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file.

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
2. In **Azure PowerShell** passare alla cartella **ADF**.
3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureStorageLinkedService**. Nell'esempio seguente si passano i valori per i parametri **ResourceGroupName** e **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure.
1. Creare un file JSON denominato **AzureSQLDatabaseLinkedService.json** nella cartella **C:\ADF** con il contenuto seguente. Creare la cartella ADF se non esiste già. Sostituire **&lt;server&gt; &lt;database&gt;, &lt;user id&gt; e &lt;password&gt;** con il nome del server SQL di Azure, il database, l'ID utente e la password prima di salvare il file. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
1. In **Azure PowerShell** passare alla cartella **ADF**.
2. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di source e sink. 

### <a name="create-a-source-dataset"></a>Creare set di dati di origine

1. Creare un file JSON denominato SourceDataset.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    In questa esercitazione si usa il nome di tabella **data_source_table**. Sostituirlo se si usa una tabella con un nome diverso. 
2.  Eseguire il cmdlet Set-AzureRmDataFactoryV2Dataset per creare il set di dati SourceDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Creare un set di dati sink

1. Creare un file JSON denominato SinkDataset.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Questo frammento di codice presuppone che nell'archivio BLOB di Azure sia presente un contenitore BLOB denominato **adftutorial**. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. La cartella di output `incrementalcopy` viene creato automaticamente se non esiste nel contenitore. In questa esercitazione, il nome del file viene generato in modo dinamico con l'espressione: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.
2.  Eseguire il cmdlet Set-AzureRmDataFactoryV2Dataset per creare il set di dati SinkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-watermark"></a>Creare un set di dati per il limite
In questo passaggio si crea un set di dati per l'archiviazione di un valore limite massimo. 

1. Creare un file JSON denominato WatermarkDataset.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Eseguire il cmdlet Set-AzureRmDataFactoryV2Dataset per creare il set di dati WatermarkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
In questa esercitazione si crea una pipeline con due attività di ricerca, un'attività di copia e un'attività di stored procedure concatenate in una pipeline. 


1. Creare un file JSON denominato IncrementalCopyPipeline.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
            
        }
    }
    ```
    

2. Eseguire il cmdlet Set-AzureRmDataFactoryV2Pipeline per creare la pipeline: IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Di seguito è riportato l'output di esempio: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>Eseguire la pipeline

1. Eseguire la pipeline **IncrementalCopyPipeline** usando il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Sostituire i segnaposto con il nome del gruppo di risorse e della data factory.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ``` 
2. Verificare lo stato della pipeline eseguendo il cmdlet Get-AzureRmDataFactoryV2ActivityRun fino a visualizzare tutte le attività correttamente in esecuzione. Sostituire i segnaposto con la data appropriata per il parametro RunStartedAfter e RunStartedBefore.  In questa esercitazione si usa -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Di seguito è riportato l'output di esempio:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Esaminare i risultati

1. Nell'archivio BLOB di Azure (archivio sink), si noterà che i dati sono stati copiati nel file definito in SinkDataset.  Nell'esercitazione corrente il nome del file è `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`.  Aprendo il file si può notare che i record corrispondono ai dati presenti nel database SQL di Azure.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Verificare il valore più recente da `watermarktable`. Si noterà che il valore limite è stato aggiornato.

    ```sql
    Select * from watermarktable
    ```
    
    Di seguito è riportato l'output di esempio:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table   2017-09-05  8:06:00.000

### <a name="insert-data-into-data-source-store-to-verify-delta-data-loading"></a>Inserire i dati nell'archivio dell'origine dati per verificare il caricamento dei dati delta

1. Inserire nuovi dati nel database SQL di Azure, ovvero nell'archivio dell'origine dati:

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    I dati aggiornati nel database SQL di Azure sono i seguenti:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Eseguire di nuovo la pipeline **IncrementalCopyPipeline** usando il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Sostituire i segnaposto con il nome del gruppo di risorse e della data factory.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Verificare lo stato della pipeline eseguendo il cmdlet **Get-AzureRmDataFactoryV2ActivityRun** fino a visualizzare tutte le attività correttamente in esecuzione. Sostituire i segnaposto con la data appropriata per il parametro RunStartedAfter e RunStartedBefore.  In questa esercitazione si usa -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Di seguito è riportato l'output di esempio:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4.  Nell'archivio BLOB di Azure si noterà che è stato creato un altro file nell'archivio BLOB di Azure. In questa esercitazione, il nome del nuovo file è `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`.  Aprendo il file si noteranno due righe di record:
5.  Verificare il valore più recente da `watermarktable`. Si noterà che il valore limite è stato aggiornato di nuovo

    ```sql
    Select * from watermarktable
    ```
    Output di esempio: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Definire una colonna con valori **limite** e archiviarla nel database SQL di Azure.  
> * Creare una data factory.
> * Creare servizi collegati per Archiviazione BLOB e Database SQL. 
> * Creare set di dati di origine e sink.
> * Creare una pipeline.
> * Eseguire la pipeline.
> * Monitorare l'esecuzione della pipeline. 

In questa esercitazione la pipeline ha copiato dati da una **singola tabella** di un database SQL di Azure a una risorsa di archiviazione BLOB di Azure. Passare all'esercitazione successiva per informazioni sulla copia di dati da **più tabelle** di un database di SQL Server locale a un database SQL di Azure. 

> [!div class="nextstepaction"]
>[Caricare dati in modo incrementale da più tabelle in SQL Server al database SQL di Azure](tutorial-incremental-copy-multiple-tables-powershell.md)



