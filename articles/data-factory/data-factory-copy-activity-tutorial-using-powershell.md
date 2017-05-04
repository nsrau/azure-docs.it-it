---
title: 'Esercitazione: Creare una pipeline per lo spostamento di dati con Azure PowerShell | Documentazione Microsoft'
description: "In questa esercitazione viene creata una pipeline di Azure Data Factory con un&quot;attività di copia usando Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8b5cb66ea958cf6643fa34abb8d484b97b212373
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Esercitazione: Creare una pipeline di Data Factory per lo spostamento di dati con Azure PowerShell
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

In questa esercitazione viene creata e monitorata una istanza di Azure Data Factory mediante i cmdlet di Azure PowerShell. La pipeline nella data factory creata in questa esercitazione usa un'attività di copia per copiare i dati da un BLOB di Azure e inserirli in un database SQL di Azure.

L'attività di copia esegue lo spostamento dei dati in Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md).   

> [!NOTE]
> Questo articolo non illustra tutti i cmdlet di Data factory. Per la documentazione completa su questi cmdlet, vedere [Data Factory Cmdlet Reference](/powershell/module/azurerm.datafactories) (Informazioni di riferimento sui cmdlet di Data Factory).
>
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Non trasforma i dati di input per produrre dati di output. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare una pipeline per trasformare i dati usando un cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti
- Per una panoramica dell'esercitazione e per eseguire i passaggi relativi ai [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , vedere **Panoramica e prerequisiti** .
- Installare Azure PowerShell. Seguire le istruzioni in [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Come installare e configurare Azure PowerShell).

## <a name="in-this-tutorial"></a>Contenuto dell'esercitazione:
La tabella seguente elenca i passaggi da eseguire come parte dell'esercitazione.

| Passaggio | Descrizione |
| --- | --- |
| [Creare un'istanza di Azure Data Factory](#create-data-factory) |In questo passaggio viene creata una data factory di Azure denominata **ADFTutorialDataFactoryPSH**. |
| [Creazione di servizi collegati](#create-linked-services) |In questo passaggio vengono creati due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService collega un servizio di Archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure a ADFTutorialDataFactoryPSH. |
| [Creare set di dati di input e di output](#create-datasets) |In questo passaggio vengono definiti due set di dati, EmpTableFromBlob e EmpSQLTable, usati come tabelle di input e output per l'**attività di copia** nella ADFTutorialPipeline che verrà creata nel passaggio seguente. |
| [Creazione ed esecuzione di una pipeline](#create-pipeline) |In questo passaggio viene creata una pipeline denominata **ADFTutorialPipeline** nella data factory ADFTutorialDataFactoryPSH. La pipeline usa un'attività di copia per copiare dati da un BLOB di Azure a una tabella di output del database di Azure. |
| [Monitorare i set di dati e la pipeline](#monitor-pipeline) |In questo passaggio vengono monitorati i set di dati e la pipeline mediante Azure PowerShell. |

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
In questo passaggio è possibile usare Azure PowerShell per creare una data factory di Azure denominata **ADFTutorialDataFactoryPSH**.

1. Avviare **PowerShell**. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

    Eseguire questo comando e immettere il nome utente e la password usati per accedere al portale di Azure:

    ```PowerShell
    Login-AzureRmAccount
    ```   
   
    Eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```PowerShell
    Get-AzureRmSubscription
    ```

    Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **&lt;NameOfAzureSubscription**&gt; con il nome della sottoscrizione di Azure:

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
2. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo questo comando:

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato **ADFTutorialResourceGroup**. Se si usa un gruppo di risorse diverso, è necessario usarlo al posto di ADFTutorialResourceGroup in questa esercitazione.
3. Eseguire il cmdlet **New-AzureRmDataFactory** per creare una data factory denominata **ADFTutorialDataFactoryPSH**:  

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome. Ad esempio, NomeUtenteADFTutorialDataFactoryPSH. Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactoryPSH. Per informazioni sugli elementi di Data Factory, vedere [Data Factory - Regole di denominazione](data-factory-naming-rules.md).

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Per creare istanze di Data Factory, è necessario essere un collaboratore o amministratore della sottoscrizione di Azure.
* Il nome della data factory può essere registrato come nome DNS in futuro e quindi divenire visibile pubblicamente.
* È possibile che venga visualizzato questo errore: "**La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**". Eseguire una di queste operazioni e provare a ripetere la pubblicazione:

  * In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory:

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Eseguire questo comando per verificare che il provider di Data Factory sia registrato:

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Accedere al [portale di Azure](https://portal.azure.com) usando la sottoscrizione di Azure. Passare a un pannello di Data Factory o creare una data factory nel portale di Azure. Questa azione registra automaticamente il provider.

## <a name="create-linked-services"></a>Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere un servizio di Archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale che include dati di input o archivia dati di output per una pipeline di Data Factory. Un servizio di calcolo è un servizio che elabora dati di input e produce dati di output.

In questo passaggio vengono creati due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure alla data factory **ADFTutorialDataFactoryPSH**. Più avanti in questa esercitazione viene una pipeline che copia i dati da un contenitore BLOB di StorageLinkedService e li inserisce in una tabella SQL di AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Creare un servizio collegato per un account di archiviazione di Azure
1. Creare un file JSON denominato **StorageLinkedService.json** nella cartella **C:\ADFGetStartedPSH** con il contenuto seguente. Creare la cartella ADFGetStartedPSH, se non esiste già.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ```
   Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.
2. In **Azure PowerShell** passare alla cartella **ADFGetStartedPSH**.
3. È possibile usare il cmdlet **New-AzureRmDataFactoryLinkedService** per creare un servizio collegato. Questo cmdlet e altri cmdlet di Data Factory usati in questa esercitazione richiedono il passaggio di valori per i parametri **ResourceGroupName** e **DataFactoryName**. In alternativa, è possibile usare **Get-AzureRmDataFactory** per ottenere un oggetto DataFactory e passarlo senza digitare ResourceGroupName e DataFactoryName ogni volta che si esegue un cmdlet. Eseguire questo comando per assegnare l'output del cmdlet **Get-AzureRmDataFactory** a una variabile **$df**:

    ```PowerShell   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. Eseguire quindi il cmdlet **New-AzureRmDataFactoryLinkedService** per creare il servizio collegato **StorageLinkedService**.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    Se il cmdlet **Get-AzureRmDataFactory** non è stato eseguito e l'output non è stato assegnato alla variabile **$df**, sarà necessario specificare i valori per i parametri ResourceGroupName e DataFactoryName come indicato di seguito.   

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

Se si chiude Azure PowerShell a metà esercitazione, è necessario eseguire il cmdlet Get-AzureRmDataFactory al successivo avvio di Azure PowerShell per completare l'esercitazione.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Creare un servizio collegato per un database SQL di Azure
1. Creare un file JSON denominato AzureSqlLinkedService.json con il contenuto seguente:

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
   Sostituire **servername**, **databasename**, **username@servername** e **password** con i nomi del server, database, account utente e password di Azure SQL.
2. Eseguire questo comando per creare un servizio collegato:

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   Confermare che l'impostazione **Consenti l'accesso a Servizi di Azure** sia attivata per il server di database SQL. Per verificare e attivare l'impostazione, seguire questa procedura:

   1. Fare clic sull'hub **ESPLORA** a sinistra, quindi su **Server SQL**.
   2. Selezionare il server e quindi fare clic su **IMPOSTAZIONI** nel pannello **SQL SERVER**.
   3. Nel pannello **IMPOSTAZIONI** fare clic su **Firewall**.
   4. Nel pannello **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso Servizi Azure**.
   5. Fare clic sull'hub **ATTIVA** a sinistra per passare al pannello **Data Factory** precedente.

## <a name="create-datasets"></a>Creare set di dati
Nel passaggio precedente sono stati creati servizi per collegare un account di Archiviazione di Azure e un database SQL di Azure alla data factory. In questo passaggio vengono creati set di dati che rappresentano i dati di input e di output per l'attività di copia nella pipeline che viene creata nel passaggio successivo.

Un tabella è un set di dati rettangolare. È attualmente l'unico tipo di set di dati supportato. La tabella di input in questa esercitazione fa riferimento a un contenitore BLOB in Archiviazione di Azure. La tabella di output fa riferimento a una tabella SQL nel database SQL di Azure.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Preparare l'archivio BLOB di Azure e il database SQL Azure per l'esercitazione
Ignorare questo passaggio se è stata eseguita l'esercitazione [Copiare dati da un archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Seguire questa procedura per preparare l'archivio BLOB e il database SQL per l'esercitazione.

1. Creare un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure a cui fa riferimento **StorageLinkedService**.
2. Creare e caricare un file di testo, denominato **emp.txt** come BLOB per il contenitore **adftutorial**.
3. Creare una tabella, denominata **emp**, nel database SQL a cui fa riferimento **AzureSqlLinkedService**.

4. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\ADFGetStartedPSH** nel disco rigido.

    ```
    John, Doe
    Jane, Doe
    ```
5. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Azure Storage Explorer](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. Usare il seguente script SQL per creare la tabella **emp** nel database SQL.  

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    Se nel computer è installato SQL Server 2014, seguire le istruzioni disponibili nel [Passaggio 2: Connettersi al database SQL dell'articolo Gestione di database SQL di Azure tramite SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) per connettersi al server del database SQL ed eseguire lo script SQL.

    Se il client non è autorizzato ad accedere al server del database SQL, è necessario configurare il firewall per il server del database SQL per consentire l'accesso dal computer (indirizzo IP). Per informazioni sulla procedura, vedere [questo articolo](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-an-input-dataset"></a>Creare un set di dati di input
Un tabella è un set di dati rettangolare che prevede uno schema. In questo passaggio viene creata una tabella, denominata **EmpBlobTable**. Questa tabella fa riferimento a un contenitore BLOB in Archiviazione di Azure rappresentato dal servizio collegato **StorageLinkedService**. Questo contenitore BLOB, adftutorial, contiene i dati di input nel file **emp.txt**.

1. Creare un file JSON denominato **EmpBlobTable.json** nella cartella **C:\ADFGetStartedPSH** con i contenuti seguenti:

    ```json
    {
        "name": "EmpTableFromBlob",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

   Tenere presente quanto segue:

   * Il set di dati **type** è impostato su **AzureBlob**.
   * L'oggetto **linkedServiceName** è impostato su **StorageLinkedService**.
   * L'oggetto **folderPath** è impostato sul contenitore **adftutorial**.
   * **fileName** è impostato su **emp.txt**. Se non si specifica il nome del BLOB, i dati di tutti i BLOB nel contenitore sono considerati come dati di input.  
   * Il formato dell'oggetto **type** è impostato su **TextFormat**.
   * Nel file di testo sono presenti due campi, **FirstName** e **LastName**, separati da una virgola, columnDelimiter.    
   * L'oggetto **availability** è impostato su **hourly**. L'oggetto frequency è impostato su hour e l'oggetto interval è impostato su 1. Il servizio Data Factory cerca quindi i dati di input ogni ora nella cartella radice del contenitore BLOB, adftutorial.

   Se non si specifica un oggetto **fileName** per una **tabella di input**, tutti i file e i BLOB della cartella di input, folderPath, vengono considerati input. Se si specifica un oggetto fileName in JSON, solo il file o BLOB specificato viene considerato un input.

   Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid\>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà **partitionedBy**. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Se viene generata una sezione per 2016-10-20T08:00:00, ad esempio, folderName è impostato su wikidatagateway/wikisampledataout/2016/10/20 e fileName è impostato su 08.csv.

    ```json
     "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
     "fileName": "{Hour}.csv",
     "partitionedBy":
     [
         { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
         { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
         { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
         { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
     ],
    ```

   Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON](data-factory-data-movement-activities.md).
2. Eseguire il comando seguente per creare il set di dati di Data factory.

    ```PowerShell  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>Creare un set di dati di output
In questo passaggio si crea un set di dati di output denominato **EmpSQLTable**. Questo set di dati punta a una tabella SQL (emp) nel database SQL di Azure rappresentato da **AzureSqlLinkedService**. La pipeline copia i dati dal BLOB di input e li inserisce nella tabella **emp** .

1. Creare un file JSON denominato **EmpSQLTable.json** nella cartella **C:\ADFGetStartedPSH** con il contenuto seguente:

    ```json
    {
        "name": "EmpSQLTable",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

   Tenere presente quanto segue:

   * Il set di dati **type** è impostato su **AzureSqlTable**.
   * L'oggetto **linkedServiceName** è impostato su **AzureSqlLinkedService**.
   * L'oggetto **tablename** è impostato su **emp**.
   * La tabella emp del database include tre colonne, **ID**, **FirstName** e **LastName**. ID è una colonna Identity, quindi in questo caso è necessario specificare solo **FirstName** e **LastName**.
   * L'oggetto **availability** è impostato su **hourly**. L'oggetto frequency è impostato su hour e l'oggetto interval è impostato su 1. Il servizio Data Factory genera una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.
2. Eseguire il comando seguente per creare il set di dati di Data Factory.

    ```PowerShell   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con l'**attività di copia**. La pipeline usa **EmpTableFromBlob** come input e **EmpSQLTable** come output.

1. Creare un file JSON denominato **ADFTutorialPipeline.json** nella cartella **C:\ADFGetStartedPSH** con i contenuti seguenti:

    ```json
    {
        "name": "ADFTutorialPipeline",
        "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
                {
                    "name": "CopyFromBlobToSQL",
                    "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                    "type": "Copy",
                    "inputs": [{ "name": "EmpTableFromBlob" }],
                    "outputs": [{ "name": "EmpSQLTable" }],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "SqlSink"
                        }
                    },
                    "Policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "style": "StartOfInterval",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
            ],
            "start": "2016-08-09T00:00:00Z",
            "end": "2016-08-10T00:00:00Z",
            "isPaused": false
        }
     }
    ```
   Tenere presente quanto segue:

   * Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
   * L'input per l'attività è impostato su **EmpTableFromBlob** e l'output per l'attività è impostato su **EmpSQLTable**.
   * Nella sezione **transformation** **BlobSource** viene specificato come tipo di origine e **SqlSink** come tipo di sink.

   Sostituire il valore della proprietà **start** con il giorno corrente e il valore della proprietà **end** con il giorno successivo. Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2016-10-14T16:32:41Z. In questa esercitazione viene usato il valore di **end**, ma è facoltativo.

   Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come valore per la proprietà **end**.

   Nell'esempio sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.

   Per altre informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON](data-factory-data-movement-activities.md).
2. Eseguire il comando seguente per creare la tabella di Data Factory.

    ```PowerShell   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

Congratulazioni. Sono stati creati un'istanza di Azure Data Factory, servizi collegati, tabelle e una pipeline. È stata anche pianificata la pipeline.

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usato Azure PowerShell per monitorare le attività in un'istanza di Azure Data Factory.

1. Eseguire **Get-AzureRmDataFactory** e assegnare l'output a una variabile $df.

    ```PowerShell  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. Eseguire **Get-AzureRmDataFactorySlice** per ottenere dettagli su tutte le sezioni di **EmpSQLTable**, ovvero la tabella di output della pipeline.  

    ```PowerShell   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Sostituire la parte relativa ad anno, mese e data del parametro **StartDateTime** con l'anno, il mese e la data correnti. Questa impostazione deve corrispondere al valore **Start** nel file JSON della pipeline.

   Dovrebbero essere visualizzate 24 sezioni, una per ogni ora a partire dalle 00.00 del giorno corrente alle 00.00 del giorno successivo.

   **Output di esempio:**

    ``` 
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. Eseguire **Get-AzureRmDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione **specifica**. Cambiare il valore del parametro **StartDateTime** in modo che corrisponda all'orario specificato per **Start** per la sezione dall'output. Il valore di **StartDateTime** deve essere in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601).

    ```PowerShell  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   L'output dovrebbe essere simile all'esempio seguente:

    ```  
    Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH
    TableName           : EmpSQLTable
    ProcessingStartTime : 8/9/2016 11:03:28 PM
    ProcessingEndTime   : 8/9/2016 11:04:36 PM
    PercentComplete     : 100
    DataSliceStart      : 8/9/2016 10:00:00 PM
    DataSliceEnd        : 8/9/2016 11:00:00 PM
    Status              : Succeeded
    Timestamp           : 8/9/2016 11:03:28 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy
    ```

Per la documentazione completa sui cmdlet di Data Factory, vedere [Data Factory Cmdlet Reference][cmdlet-reference] (Informazioni di riferimento su sui cmdlet di Data Factory).

## <a name="summary"></a>Riepilogo
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato PowerShell per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare **servizi collegati**:

   a. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.     
   b. Un servizio collegato di **Azure SQL** per collegare il database SQL che contiene i dati di output.
3. Creare **set di dati** che descrivono dati di input e di output per le pipeline.
4. Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink.

## <a name="see-also"></a>Vedere anche
| Argomento | Descrizione |
|:--- |:--- |
| [Informazioni di riferimento sui cmdlet di Data factory](/powershell/module/azurerm.datafactories) | Questa sezione fornisce informazioni su tutti i cmdlet di Data Factory |
| [Pipeline](data-factory-create-pipelines.md) |Questo articolo fornisce informazioni sulle pipeline e le attività in Azure Data Factory. |
| [datasets](data-factory-create-datasets.md) |Questo articolo fornisce informazioni sui set di dati in Azure Data Factory. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) |Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo Azure Data Factory. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md

