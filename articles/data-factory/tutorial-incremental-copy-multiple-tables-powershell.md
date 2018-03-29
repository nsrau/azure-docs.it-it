---
title: Eseguire la copia incrementale di più tabelle con Azure Data Factory | Microsoft Docs
description: In questa esercitazione si creerà una pipeline di Azure Data Factory che copia dati differenziali in modo incrementale da più tabelle di un database di SQL Server locale a un database SQL di Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 8f59ffb2011ad43173881d4ced231e4820fcf5f8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Caricare dati in modo incrementale da più tabelle in SQL Server a un database SQL di Azure
In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati delta da più tabelle di un database di SQL Server locale a un database SQL di Azure.    

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare gli archivi dati di origine e di destinazione.
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Installare il runtime di integrazione. 
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare, eseguire e monitorare una pipeline.
> * Esaminare i risultati.
> * Aggiungere o aggiornare dati nelle tabelle di origine.
> * Rieseguire e monitorare la pipeline.
> * Esaminare i risultati finali.

> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione di Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Panoramica
Di seguito sono descritti i passaggi fondamentali per la creazione di questa soluzione: 

1. **Selezionare la colonna del limite**.
    Selezionare una colonna per ogni tabella nell'archivio dati di origine, che può essere usata per identificare i record nuovi o aggiornati per ogni esecuzione. I dati della colonna selezionata (ad esempio last_modify_time o ID) continuano in genere ad aumentare quando le righe vengono create o aggiornate. Il valore massimo di questa colonna viene usato come limite.

2. **Preparare un archivio dati per l'archiviazione del valore limite**.   
    In questa esercitazione si archivia il valore limite in un database SQL.

3. **Creare una pipeline con le attività seguenti**: 
    
    a. Creare un'attività ForEach che esegue l'iterazione di un elenco di nomi di tabella di origine passato come parametro alla pipeline. Per ogni tabella di origine, l'attività richiama le attività seguenti per eseguire il caricamento differenziale per la tabella.

    b. Creare due attività di ricerca. Usare la prima attività di ricerca per recuperare l'ultimo valore limite. Usare la seconda attività di ricerca per recuperare il nuovo valore limite. Questi valori limite vengono passati all'attività di copia.

    c. Creare un'attività di copia che copi le righe dell'archivio dati di origine con il valore della colonna del limite maggiore del valore limite precedente e minore del nuovo valore limite. L'attività copierà quindi i dati delta dall'archivio dati di origine a un archivio BLOB di Azure come nuovo file.

    d. Creare un'attività stored procedure che aggiorni il valore limite per la pipeline che verrà eseguita la volta successiva. 

    Il diagramma generale della soluzione è il seguente: 

    ![Caricare i dati in modo incrementale](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* **SQL Server**. Usare un database di SQL Server locale come archivio dati di origine in questa esercitazione. 
* **Database SQL di Azure**. Usare un database SQL come archivio dati sink. Se non è disponibile un database SQL, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md) per crearne uno. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Creare le tabelle di origine nel database di SQL Server

1. Aprire SQL Server Management Studio e connettersi al database SQL Server locale.

2. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

3. Eseguire il comando SQL seguente sul database per creare le tabelle denominate `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Creare le tabelle di destinazione nel database SQL di Azure
1. Aprire SQL Server Management Studio e connettersi al database di SQL Server.

2. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

3. Eseguire questo comando SQL sul database SQL per creare le tabelle denominate `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Creare un'altra tabella nel database SQL di Azure per archiviare il valore limite massimo
1. Eseguire questo comando SQL sul database SQL per creare una tabella denominata `watermarktable` in cui archiviare il valore limite: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Inserire i valori del limite iniziale per entrambe le tabelle di origine nella tabella dei limiti.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Creare una stored procedure nel database SQL di Azure 

Eseguire questo comando per creare una stored procedure nel database SQL. Questa stored procedure aggiorna il valore del limite dopo ogni esecuzione di pipeline. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Creare tipi di dati e stored procedure aggiuntive nel database SQL di Azure
Eseguire questa query per creare due stored procedure e due tipi di dati nel database SQL. Questi elementi vengono usati per unire i dati delle tabelle di origine nelle tabelle di destinazione.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Un esempio è `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando.

2. Definire una variabile per la località della data factory. 

    ```powershell
    $location = "East US"
    ```
3. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando.

4. Definire una variabile per il nome della data factory. 

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. Un esempio è ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Per creare la data factory, eseguire questo cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* Il nome della data factory deve essere univoco a livello globale. Se viene visualizzato l'errore seguente, modificare il nome e riprovare:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli collaboratore o proprietario oppure un amministratore della sottoscrizione di Azure.
* Data Factory versione 2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL e così via) e le risorse di calcolo (Azure HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa sezione vengono creati i servizi collegati al database di SQL Server locale e al database SQL. 

### <a name="create-the-sql-server-linked-service"></a>Creare il servizio collegato di SQL Server
In questo passaggio si collega il database di SQL Server locale alla data factory.

1. Creare un file JSON denominato SqlServerLinkedService.json nella cartella C:\ADFTutorials\IncCopyMultiTableTutorial con il contenuto seguente. Selezionare la sezione corretta in base all'autenticazione usata per connettersi a SQL Server. Creare le cartelle locali, se non esistono già. 

    > [!IMPORTANT]
    > Selezionare la sezione corretta in base all'autenticazione usata per connettersi a SQL Server.

    Se si usa l'autenticazione SQL, copiare la definizione JSON seguente:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    Se si usa l'autenticazione Windows, copiare la definizione JSON seguente:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Selezionare la sezione corretta in base all'autenticazione usata per connettersi a SQL Server.
    > - Sostituire &lt;integration runtime name> con il nome del runtime di integrazione.
    > - Prima di salvare il file, sostituire &lt;servername>, &lt;databasename>, &lt;username> e &lt;password> con i valori del database di SQL Server.
    > - Se è necessario usare una barra (`\`) nell'account utente o nel nome del server, usare il carattere di escape (`\`). Un esempio è `mydomain\\myuser`.

2. In PowerShell passare alla cartella C:\ADFTutorials\IncCopyMultiTableTutorial.

3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato AzureStorageLinkedService. Nell'esempio seguente si passano i valori per i parametri *ResourceGroupName* e *DataFactoryName*: 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Creare il servizio collegato per il database SQL
1. Creare un file JSON denominato AzureSQLDatabaseLinkedService.json nella cartella C:\ADFTutorials\IncCopyMultiTableTutorial con il contenuto seguente. Creare la cartella ADF, se non esiste. Prima di salvare il file, sostituire &lt;server&gt;, &lt;database name&gt;, &lt;user id&gt; e &lt;password&gt; con il nome del database di SQL Server, il nome del database, l'ID utente e la password. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. In PowerShell eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati i set di dati per rappresentare l'origine dati, la destinazione dati e la posizione di archiviazione del valore limite.

### <a name="create-a-source-dataset"></a>Creare set di dati di origine

1. Creare un file JSON denominato SourceDataset.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    Il nome della tabella è un nome fittizio. L'attività di copia nella pipeline usa una query SQL per caricare i dati invece di caricare l'intera tabella.

2. Eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset** per creare il set di dati SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Creare un set di dati sink

1. Creare un file JSON denominato SinkDataset.json nella stessa cartella con il contenuto seguente. L'elemento tableName viene impostato dalla pipeline in modo dinamico in fase di esecuzione. L'attività ForEach nella pipeline esegue l'iterazione di un elenco di nomi di tabella e passa il nome di tabella a questo set di dati in ogni interazione. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset** per creare il set di dati SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Creare un set di dati per un limite
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
2. Eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset** per creare il set di dati WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
Questa pipeline accetta un elenco di nomi di tabella come parametro. L'attività ForEach esegue l'iterazione dell'elenco dei nomi di tabella ed esegue le operazioni seguenti: 

1. Usa l'attività di ricerca per recuperare il valore del limite precedente (valore iniziale o usato nell'ultima iterazione).

2. Usa l'attività di ricerca per recuperare il nuovo valore del limite (valore massimo della colonna dei limiti nella tabella di origine).

3. Usa l'attività di copia per copiare dati tra i due valori del limite dal database di origine al database di destinazione.

4. Usa l'attività stored procedure per aggiornare il valore del limite precedente da usare nel primo passaggio dell'iterazione successiva. 

### <a name="create-the-pipeline"></a>Creare la pipeline
1. Creare un file JSON denominato IncrementalCopyPipeline.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
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
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
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
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
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
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
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
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TABLE_NAME}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
2. Eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline** per creare la pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Di seguito è riportato l'output di esempio: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Eseguire la pipeline

1. Creare un file di parametro denominato Parameters.json nella stessa cartella con il contenuto seguente:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
2. Eseguire la pipeline IncrementalCopyPipeline usando il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Sostituire i segnaposto con il nome del gruppo di risorse e della data factory.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Tutti i servizi**, eseguire una ricerca con la parola chiave *Data factory* e selezionare **Data factory**. 

    ![Menu Data factory](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)

3. Cercare la propria data factory nell'elenco delle data factory e selezionarla per aprire la pagina **Data factory**. 

    ![Cercare la data factory](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)

4. Nella pagina **Data factory** selezionare **Monitoraggio e gestione**. 

    ![Riquadro Monitoraggio e gestione](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)

5. Si aprirà l'applicazione **Integrazione dati** in una scheda separata. È possibile visualizzare tutte le esecuzioni di pipeline e i rispettivi stati. Notare che nell'esempio seguente lo stato dell'esecuzione della pipeline è **Riuscito**. Per verificare i parametri passati alla pipeline, selezionare il collegamento nella colonna **Parametri**. Se si è verificato un errore, verrà visualizzato un collegamento nella colonna **Errore**. Selezionare il collegamento nella colonna **Azioni**. 

    ![Esecuzioni di pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. Quando si seleziona il collegamento nella colonna **Azioni**, viene visualizzata la pagina seguente con tutte le esecuzioni di attività per la pipeline: 

    ![Esecuzioni di attività](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)

7. Per tornare alla visualizzazione **Esecuzioni di pipeline**, selezionare **Pipeline** come mostrato nell'immagine. 

## <a name="review-the-results"></a>Esaminare i risultati
In SQL Server Management Studio eseguire queste query sul database SQL di destinazione per verificare che i dati siano stati copiati dalle tabelle di origine alle tabelle di destinazione: 

**Query** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Query**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Query**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Notare che i valori del limite per entrambe le tabelle sono stati aggiornati. 

## <a name="add-more-data-to-the-source-tables"></a>Aggiungere altri dati alle tabelle di origine

Eseguire questa query sul database di SQL Server di origine per aggiornare una riga esistente in customer_table. Inserire una nuova riga in project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Eseguire di nuovo la pipeline

1. Eseguire di nuovo la pipeline con il comando di PowerShell seguente:

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Monitorare le esecuzioni di pipeline seguendo le istruzioni contenute nella sezione [Monitorare la pipeline](#monitor-the-pipeline). Poiché lo stato della pipeline è **In corso**, in **Azioni** viene visualizzato un altro collegamento a un'azione per annullare l'esecuzione della pipeline. 

    ![Esecuzioni di pipeline in corso](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)

3. Selezionare **Aggiorna** per aggiornare l'elenco finché l'esecuzione della pipeline non riesce. 

    ![Aggiornare le esecuzioni di pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)

4. Fare eventualmente clic sul collegamento **Visualizza esecuzioni attività** in **Azioni** per visualizzare tutte le esecuzioni di attività associate a questa esecuzione di pipeline. 

## <a name="review-the-final-results"></a>Esaminare i risultati finali
In SQL Server Management Studio eseguire le query seguenti sul database di destinazione per verificare che i dati nuovi/aggiornati siano stati copiati dalle tabelle di origine alle tabelle di destinazione. 

**Query** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Notare i nuovi valori di **Name** e **LastModifytime** per **PersonID** per il numero 3. 

**Query**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Si noti che è stata aggiunta la voce **NewProject** a project_table. 

**Query**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Notare che i valori del limite per entrambe le tabelle sono stati aggiornati.
     
## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Preparare gli archivi dati di origine e di destinazione.
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Installare il runtime di integrazione.
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare, eseguire e monitorare una pipeline.
> * Esaminare i risultati.
> * Aggiungere o aggiornare dati nelle tabelle di origine.
> * Rieseguire e monitorare la pipeline.
> * Esaminare i risultati finali.

Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Caricare dati in modo incrementale dal database SQL di Azure all'archivio BLOB di Azure tramite la tecnologia Rilevamento modifiche](tutorial-incremental-copy-change-tracking-feature-powershell.md)


