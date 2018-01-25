---
title: Copiare dati in blocco con Azure Data Factory | Microsoft Docs
description: "Informazioni su come usare Azure Data Factory e l'attività di copia per copiare dati in blocco da un archivio dati di origine a un archivio dati di destinazione."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 9971eb9e069d9fe1148c293c3459a8b11d931c2a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiare più tabelle in blocco con Azure Data Factory
Questa esercitazione illustra la **copia di alcune tabelle dal database SQL di Azure ad Azure SQL Data Warehouse**. È possibile applicare lo stesso modello anche in altri scenari di copia, ad esempio per la copia di tabelle da SQL Server/Oracle in database SQL di Azure/SQL Data Warehouse/archivio BLOB di Azure o la copia di percorsi diversi dall'archivio BLOB alle tabelle del database SQL di Azure.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

A livello generale, questa esercitazione prevede la procedura seguente:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare i servizi collegati database SQL di Azure, Azure SQL Data Warehouse e Archiviazione di Azure.
> * Creare i set di dati per database SQL di Azure e Azure SQL Data Warehouse.
> * Creare una pipeline per cercare le tabelle da copiare e un'altra pipeline per eseguire l'operazione di copia effettiva. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Questa esercitazione usa Azure PowerShell. Per informazioni sull'uso di altri strumenti/SDK per creare una data factory, vedere le [Guide introduttive](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
In questo scenario il database SQL di Azure include alcune tabelle da copiare in SQL Data Warehouse. Ecco la sequenza logica di passaggi nel flusso di lavoro che si verifica nelle pipeline:

![Flusso di lavoro](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* La prima pipeline cerca l'elenco di tabelle da copiare negli archivi dati sink.  In alternativa è possibile mantenere una tabella di metadati che elenca tutte le tabelle da copiare nell'archivio dati sink. La pipeline attiva quindi un'altra pipeline, che esegue l'iterazione di ogni tabella nel database ed esegue l'operazione di copia dei dati.
* La seconda pipeline esegue la copia effettiva. Accetta l'elenco di tabelle come parametro. Per ogni tabella nell'elenco è necessario copiare la tabella specifica del database SQL di Azure nella tabella corrispondente in SQL Data Warehouse usando una [copia di staging tramite l'archivio BLOB e PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) per prestazioni ottimali. In questo esempio la prima pipeline passa l'elenco di tabelle come valore per il parametro. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

* **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell).
* **Account di archiviazione di Azure**. L'account di archiviazione di Azure viene usato come archivio BLOB di staging nell'operazione di copia in blocco. 
* **Database SQL di Azure**. Questo database contiene i dati di origine. 
* **Azure SQL Data Warehouse**. Questo data warehouse include i dati copiati dal database SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Preparare il database SQL e SQL Data Warehouse

**Preparare il database SQL di Azure di origine**:

Creare un database SQL di Azure con i dati dell'esempio Adventure Works LT, seguendo le istruzioni disponibili nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md). Questa esercitazione copia tutte le tabelle da questo database di esempio in un'istanza di SQL Data Warehouse.

**Preparare l'istanza sink di Azure SQL Data Warehouse**:

1. Se non si ha un'istanza di Azure SQL Data Warehouse, vedere la procedura per la creazione di un'istanza nell'articolo [Creare un SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Creare gli schemi di tabella corrispondenti in SQL Data Warehouse. È possibile usare l'[Utilità per la migrazione](https://www.microsoft.com/download/details.aspx?id=49100) per **eseguire la migrazione dello schema** dal database SQL di Azure ad Azure SQL Data Warehouse. Azure Data Factory viene usato in un passaggio successivo per la migrazione/copia dei dati.

## <a name="azure-services-to-access-sql-server"></a>Accesso dei servizi di Azure a SQL Server

Per il database SQL e per SQL Data Warehouse è necessario consentire ai servizi di Azure di accedere a SQL server. Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia **ON** per il server Azure SQL. Questa impostazione consente al servizio Data Factory di leggere dati dal database SQL di Azure e di scrivere dati in Azure SQL Data Warehouse. Per verificare e attivare l'impostazione, seguire questa procedura:

1. Fare clic sull'hub **Altri servizi** a sinistra e selezionare **Server SQL**.
2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**.
3. Nella pagina **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso a Servizi Azure**.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Avviare **PowerShell**. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

    Eseguire questo comando e immettere il nome utente e la password usati per accedere al portale di Azure:
        
    ```powershell
    Login-AzureRmAccount
    ```
    Eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```powershell
    Get-AzureRmSubscription
    ```
    Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Eseguire il cmdlet **Set-AzureRmDataFactoryV2** per creare una data factory. Sostituire i segnaposto con i propri valori prima di eseguire il comando. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Tenere presente quanto segue:

    * È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Per creare istanze di Data Factory è necessario essere un collaboratore o amministratore della sottoscrizione di Azure.
    * Data Factory V2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

## <a name="create-linked-services"></a>Creare servizi collegati

In questa esercitazione vengono creati tre servizi collegati rispettivamente per origine, sink e BLOB di staging, incluse le connessioni agli archivi dati:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Creare il servizio collegato database SQL di Azure di origine

1. Creare un file JSON denominato **AzureSqlDatabaseLinkedService.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente: (Creare la cartella ADFv2TutorialBulkCopy, se non esiste già).

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; con i valori del database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. In **Azure PowerShell** passare alla cartella **ADFv2TutorialBulkCopy**.

3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Creare il servizio collegato sink Azure SQL Data Warehouse

1. Creare un file JSON denominato **AzureSqlDWLinkedService.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; con i valori del database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. Per creare il servizio collegato **AzureSqlDWLinkedService**, eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Creare il servizio collegato di staging Archiviazione di Azure

In questa esercitazione l'archivio BLOB di Azure viene usato come area di staging intermedia per abilitare PolyBase per ottimizzare le prestazioni della copia.

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;accountName&gt; e &lt;accountKey&gt; con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Per creare il servizio collegato **AzureStorageLinkedService**, eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService**.

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

## <a name="create-datasets"></a>Creare set di dati

In questa esercitazione vengono creati i set di dati di origine e sink, che specificano la posizione in cui vengono archiviati i dati:

### <a name="create-a-dataset-for-source-sql-database"></a>Creare un set di dati per il database SQL di origine

1. Creare un file JSON denominato **AzureSqlDatabaseDataset.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente: Il valore "tableName" è un valore fittizio, perché successivamente viene usata la query SQL nell'attività di copia per recuperare i dati.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Per creare il set di dati **AzureSqlDatabaseDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Creare un set di dati per l'istanza sink di SQL Data Warehouse

1. Creare un file JSON denominato **AzureSqlDWDataset.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente: Il valore "tableName" viene configurato come parametro e successivamente l'attività di copia che fa riferimento a questo set di dati passa il valore effettivo al set di dati.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Per creare il set di dati **AzureSqlDWDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Creare le pipeline

In questa esercitazione vengono create due pipeline:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Creare la pipeline "IterateAndCopySQLTables"

Questa pipeline accetta l'elenco di tabelle come parametro. Per ogni tabella della lista, copia i dati dalla tabella del database SQL di Azure ad Azure SQL Data Warehouse usando una copia di staging e PolyBase.

1. Creare un file JSON denominato **IterateAndCopySQLTables.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Per creare la pipeline **IterateAndCopySQLTables**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Creare la pipeline "GetTableListAndTriggerCopyData"

Questa pipeline esegue due passaggi:

* Ricerca della tabella di sistema del database SQL di Azure per ottenere l'elenco di tabelle da copiare.
* Attivazione della pipeline "IterateAndCopySQLTables" per l'esecuzione della copia effettiva dei dati.

1. Creare un file JSON denominato **GetTableListAndTriggerCopyData.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
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

2. Per creare la pipeline **GetTableListAndTriggerCopyData**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Avviare e monitorare un'esecuzione della pipeline

1. Avviare un'esecuzione di pipeline per la pipeline "GetTableListAndTriggerCopyData" principale e quindi acquisire l'ID di esecuzione della pipeline per il monitoraggio futuro. Viene attivata di conseguenza l'esecuzione per la pipeline "IterateAndCopySQLTables", come specificato nell'attività ExecutePipeline.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline **GetTableListAndTriggerCopyData** e stampare il risultato finale dell'esecuzione della pipeline e dell'attività.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. È possibile ottenere l'ID dell'esecuzione della pipeline "**IterateAndCopySQLTables**" e verificare il risultato dettagliato dell'esecuzione attività come illustrato di seguito.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Connettersi all'istanza sink di Azure SQL Data Warehouse e confermare che i dati siano stati copiati correttamente dal database SQL di Azure.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare i servizi collegati database SQL di Azure, Azure SQL Data Warehouse e Archiviazione di Azure.
> * Creare i set di dati per database SQL di Azure e Azure SQL Data Warehouse.
> * Creare una pipeline per cercare le tabelle da copiare e un'altra pipeline per eseguire l'operazione di copia effettiva. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per ottenere informazioni sulla copia incrementale di dati da un'origine a una destinazione:
> [!div class="nextstepaction"]
>[Copiare i dati in modo incrementale](tutorial-incremental-copy-powershell.md)