---
title: Eseguire la copia incrementale tramite Rilevamento modifiche e Azure Data Factory | Microsoft Docs
description: 'In questa esercitazione si creerà una pipeline di Azure Data Factory che copia dati differenziali in modo incrementale da più tabelle di un database di SQL Server locale a un database SQL di Azure. '
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
ms.openlocfilehash: d8299778ce5b713f4275a28c7f174a300197a6a2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Caricare dati in modo incrementale da un database SQL di Azure all'archiviazione BLOB di Azure tramite il rilevamento delle modifiche 
In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati differenziali basati su informazioni di **rilevamento delle modifiche** nel database SQL di Azure di origine in una risorsa di archiviazione BLOB di Azure.  

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati di origine.
> * Creare una data factory.
> * Creare servizi collegati. 
> * Creare set di dati di origine, sink e di rilevamento delle modifiche.
> * Creare, eseguire e monitorare la pipeline di copia completa
> * Aggiungere o aggiornare dati nella tabella di origine
> * Creare, eseguire e monitorare la pipeline di copia incrementale

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Panoramica
In una soluzione di integrazione dei dati il caricamento incrementale di dati dopo i caricamenti di dati iniziali è uno scenario ampiamente diffuso. In alcuni casi, i dati modificati entro un periodo di tempo nell'archivio dati di origine possono essere suddivisi facilmente, ad esempio con LastModifyTime e CreationTime. In alcuni casi, non esiste un modo esplicito per identificare i dati differenziali dall'ultima elaborazione dei dati. È possibile usare la tecnologia Rilevamento modifiche supportata da archivi dati come il database SQL di Azure e SQL Server per identificare i dati differenziali.  Questa esercitazione descrive come usare Azure Data Factory versione 2 per usare la tecnologia Rilevamento modifiche SQL per caricare in modo incrementare dati differenziali dal database SQL di Azure all'archiviazione BLOB di Azure.  Per altre informazioni pratiche sulla tecnologia Rilevamento modifiche SQL, vedere [Rilevamento modifiche in SQL Server](/sql/relational-databases/track-changes/about-change-tracking-sql-server). 

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
Ecco alcuni passaggi del tipico flusso di lavoro end-to-end per caricare dati in modo incrementale usando la tecnologia Rilevamento modifiche.

> [!NOTE]
> Sia il database SQL di Azure sia SQL Server supportano la tecnologia Rilevamento modifiche. Questa esercitazione usa il database SQL di Azure come archivio dati di origine. È anche possibile usare un database di SQL Server locale. 

1. **Caricamento iniziale di dati cronologici** (una sola esecuzione):
    1. Abilitare la tecnologia Rilevamento modifiche nel database SQL di Azure di origine.
    2. Ottenere il valore iniziale di SYS_CHANGE_VERSION nel database SQL di Azure come base per acquisire i dati modificati.
    3. Caricare i dati completi dal database SQL di Azure a una risorsa di archiviazione BLOB di Azure. 
2. **Caricamento incrementale di dati differenziali in base a una pianificazione** (esecuzione periodica dopo il caricamento iniziale dei dati):
    1. Ottenere i valori precedente e nuovo di SYS_CHANGE_VERSION.
    3. Caricare i dati differenziali unendo le chiavi primarie delle righe modificate (tra due valori di SYS_CHANGE_VERSION) da **sys.change_tracking_tables** ai dati nella **tabella di origine** e quindi spostare i dati differenziali nella destinazione.
    4. Aggiornare SYS_CHANGE_VERSION per il successivo caricamento differenziale.

## <a name="high-level-solution"></a>Soluzione di alto livello
In questa esercitazione vengono create due pipeline che eseguono le due operazioni seguenti:  

1. **Caricamento iniziale:** viene creata una pipeline con un'attività di copia che copia tutti i dati dall'archivio dati di origine (database SQL di Azure) all'archivio dati di destinazione (archiviazione BLOB di Azure).

    ![Caricamento completo dei dati](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Caricamento incrementale:** viene creata una pipeline con le attività seguenti, eseguita periodicamente. 
    1. Creare **due attività di ricerca** per ottenere i valori precedente e nuovo di SYS_CHANGE_VERSION dal database SQL di Azure e passarli all'attività di copia.
    2. Creare **un'attività di copia** per copiare i dati inseriti/aggiornati/eliminati tra i due valori di SYS_CHANGE_VERSION dal database SQL di Azure all'archiviazione BLOB di Azure.
    3. Creare **un'attività stored procedure** per aggiornare il valore di SYS_CHANGE_VERSION per la successiva esecuzione di pipeline.

    ![Diagramma di flusso del caricamento incrementale](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* Azure PowerShell. Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).
* **Database SQL di Azure**. Usare il database come archivio dati di **origine**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati **sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. Creare un contenitore denominato **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Creare una tabella di origine dati nel database SQL di Azure
1. Avviare **SQL Server Management Studio** e connettersi al server SQL di Azure. 
2. In **Esplora server** fare clic con il pulsante destro del mouse sul **database** e scegliere **Nuova query**.
3. Eseguire questo comando SQL sul database SQL di Azure per creare una tabella denominata `data_source_table` come archivio dell'origine dati.  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Abilitare il meccanismo **Rilevamento modifiche** nel database e nella tabella di origine (data_source_table) eseguendo la query SQL seguente: 

    > [!NOTE]
    > - Sostituire &lt;your database name&gt; con il nome del database SQL di Azure che include data_source_table. 
    > - Nell'esempio corrente i dati modificati vengono mantenuti per due giorni. Se i dati modificati vengono caricati ogni tre giorni o più, alcuni non verranno inclusi.  In questo caso, è necessario modificare il valore di CHANGE_RETENTION impostando un numero maggiore. In alternativa, assicurarsi che il periodo per il caricamento dei dati modificati sia non più di due giorni. Per altre informazioni, vedere [Abilitare il rilevamento delle modifiche per un database](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Creare una nuova tabella e archiviare ChangeTracking_version con un valore predefinito eseguendo la query seguente: 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > Se i dati non vengono modificati dopo aver abilitato il rilevamento delle modifiche per il database SQL, il valore della versione di rilevamento delle modifiche è 0.
6. Eseguire la query seguente per creare una stored procedure nel database SQL di Azure. La pipeline richiama questa stored procedure per aggiornare la versione di rilevamento delle modifiche nella tabella creata nel passaggio precedente. 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

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
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Per creare la data factory, eseguire questo cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.
* Data Factory versione 2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.


## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa sezione si creano servizi collegati all'account di archiviazione di Azure e al database SQL di Azure. 

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure.
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory.

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFTutorials\IncCopyChangeTrackingTutorial** con il contenuto seguente. Creare la cartella se non esiste già. Sostituire `<accountName>` e `<accountKey>` con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file.

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
2. In **Azure PowerShell** passare alla cartella **C:\ADFTutorials\IncCopyChgTrackingTutorial**.
3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureStorageLinkedService**. Nell'esempio seguente si passano i valori per i parametri **ResourceGroupName** e **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure.
In questo passaggio viene collegato il database SQL di Azure alla data factory.

1. Creare un file JSON denominato **AzureSQLDatabaseLinkedService.json** nella cartella **C:\ADFTutorials\IncCopyChangeTrackingTutorial** con il contenuto seguente. Sostituire **&lt;server&gt; &lt;database name&gt;, &lt;user id&gt; e &lt;password&gt;** con il nome del server SQL di Azure, il nome del database, l'ID utente e la password prima di salvare il file. 

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
2. In **Azure PowerShell** eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati i set di dati per rappresentare l'origine dati, la destinazione dati e la posizione in cui archiviare SYS_CHANGE_VERSION.

### <a name="create-a-source-dataset"></a>Creare set di dati di origine
In questo passaggio viene creato un set di dati per rappresentare i dati di origine. 

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

2.  Eseguire il cmdlet Set-AzureRmDataFactoryV2Dataset per creare il set di dati SourceDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Creare un set di dati sink
In questo passaggio viene creato un set di dati per rappresentare i dati copiati dall'archivio dati di origine. 

1. Creare un file JSON denominato SinkDataset.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
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

    Come parte dei prerequisiti, è necessario creare il contenitore adftutorial nell'archiviazione BLOB di Azure. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. In questa esercitazione il nome del file di output viene generato in modo dinamico con l'espressione @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Eseguire il cmdlet Set-AzureRmDataFactoryV2Dataset per creare il set di dati SinkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Creare un set di dati di rilevamento delle modifiche
In questo passaggio viene creato un set di dati per l'archiviazione della versione di rilevamento delle modifiche.  

1. Creare un file JSON denominato ChangeTrackingDataset.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Come parte dei prerequisiti, è necessario creare la tabella table_store_ChangeTracking_version.
2.  Eseguire il cmdlet Set-AzureRmDataFactoryV2Dataset per creare il set di dati WatermarkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Ecco l'output di esempio del cmdlet:
    
    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Creare una pipeline per la copia completa
In questo passaggio viene creata una pipeline con un'attività di copia che copia tutti i dati dall'archivio dati di origine (database SQL di Azure) all'archivio dati di destinazione (Archiviazione BLOB di Azure).

1. Creare un file JSON denominato FullCopyPipeline.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
    
                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Eseguire il cmdlet Set-AzureRmDataFactoryV2Pipeline per creare la pipeline FullCopyPipeline.
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ``` 

   Di seguito è riportato l'output di esempio: 

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```
 
### <a name="run-the-full-copy-pipeline"></a>Eseguire la pipeline di copia completa
Eseguire la pipeline **FullCopyPipeline** usando il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
``` 

### <a name="monitor-the-full-copy-pipeline"></a>Monitorare la pipeline di copia completa

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Tutti i servizi**, eseguire una ricerca con la parola chiave `data factories` e selezionare **Data factory**. 

    ![Menu Data factory](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-data-factories-menu-1.png)
3. Cercare **la propria data factory** nell'elenco delle data factory e selezionarla per avviare la pagina Data factory. 

    ![Cercare la data factory](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-search-data-factory-2.png)
4. Nella pagina Data factory fare clic sul riquadro **Monitoraggio e gestione**. 

    ![Riquadro Monitoraggio e gestione](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-monitor-manage-tile-3.png)    
5. L'applicazione **Integrazione dati** viene avviata in una scheda separata. È possibile visualizzare tutte le **esecuzioni di pipeline** e i rispettivi stati. Notare che nell'esempio seguente lo stato dell'esecuzione della pipeline è **Riuscito**. È possibile controllare i parametri passati alla pipeline facendo clic sul collegamento nella colonna **Parametri**. Se si è verificato un errore, verrà visualizzato un collegamento nella colonna **Errore**. Fare clic sul collegamento nella colonna **Azioni**. 

    ![Esecuzioni di pipeline](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-pipeline-runs-4.png)    
6. Quando si fa clic sul collegamento nella colonna **Azioni**, viene visualizzata la pagina seguente, che mostra tutte le **esecuzioni di attività** per la pipeline. 

    ![Esecuzioni attività](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-activity-runs-5.png)
7. Per tornare alla visualizzazione **Esecuzioni di pipeline**, fare clic su **Pipeline**, come mostrato nell'immagine. 


### <a name="review-the-results"></a>Esaminare i risultati
Viene visualizzato un file denominato `incremental-<GUID>.txt` nella cartella `incchgtracking` del contenitore `adftutorial`. 

![File di output dalla copia completa](media\tutorial-incremental-copy-change-tracking-feature-powershell\full-copy-output-file.png)

Il file conterrà i dati del database SQL di Azure:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Aggiungere altri dati alla tabella di origine

Eseguire la query seguente sul database SQL di Azure per aggiungere una riga e aggiornare una riga. 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>Creare una pipeline per la copia differenziale
In questo passaggio viene creata una pipeline con le attività seguenti, eseguita periodicamente. Le **attività di ricerca** ottengono i valori precedente e nuovo di SYS_CHANGE_VERSION dal database SQL di Azure e li passano all'attività di copia. L'**attività di copia** copia i dati inseriti/aggiornati/eliminati tra i due valori di SYS_CHANGE_VERSION dal database SQL di Azure all'archiviazione BLOB di Azure. L'**attività stored procedure** aggiorna il valore di SYS_CHANGE_VERSION per la successiva esecuzione di pipeline.

1. Creare un file JSON denominato IncrementalCopyPipeline.json nella stessa cartella con il contenuto seguente: 

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },
        
                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
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
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
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
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
    
                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
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
2. Eseguire il cmdlet Set-AzureRmDataFactoryV2Pipeline per creare la pipeline FullCopyPipeline.
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Di seguito è riportato l'output di esempio: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Eseguire la pipeline di copia incrementale
Eseguire la pipeline **IncrementalCopyPipeline** usando il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
``` 


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorare la pipeline di copia incrementale
1. Nell'applicazione **Integrazione dati** aggiornare la visualizzazione **Esecuzioni di pipeline**. Verificare che la pipeline IncrementalCopyPipeline sia visualizzata nell'elenco. Fare clic sul collegamento nella colonna **Azioni**.  

    ![Esecuzioni di pipeline](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-pipeline-runs-6.png)    
2. Quando si fa clic sul collegamento nella colonna **Azioni**, viene visualizzata la pagina seguente, che mostra tutte le **esecuzioni di attività** per la pipeline. 

    ![Esecuzioni attività](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-activity-runs-7.png)
3. Per tornare alla visualizzazione **Esecuzioni di pipeline**, fare clic su **Pipeline**, come mostrato nell'immagine. 

### <a name="review-the-results"></a>Esaminare i risultati
Il secondo file viene visualizzato nella cartella `incchgtracking` del contenitore `adftutorial`. 

![File di output dalla copia incrementale](media\tutorial-incremental-copy-change-tracking-feature-powershell\incremental-copy-output-file.png)

Il file conterrà solo i dati differenziali del database SQL di Azure. Il record con `U` è la riga aggiornata nel database e `I` è la riga aggiunta. 

```
1,update,10,2,U
6,new,50,1,I
```
Le prime tre colonne sono i dati modificati da data_source_table. Le ultime due colonne sono i metadati dalla tabella di sistema del rilevamento delle modifiche. La quarta colonna è SYS_CHANGE_VERSION per ogni riga modificata. La quinta colonna è l'operazione:  U = aggiornamento, I = inserimento.  Per tutti i dettagli sulle informazioni di rilevamento delle modifiche, vedere [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati usando un cluster Spark nel cloud](tutorial-transform-data-spark-powershell.md)



