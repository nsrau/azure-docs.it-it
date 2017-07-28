---
title: 'Esercitazione: Creare una pipeline per lo spostamento di dati con Azure PowerShell | Documentazione Microsoft'
description: "In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando Azure PowerShell."
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
ms.date: 07/10/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: ba2f64eb962aa34ca74c09441845f627342590f8
ms.contentlocale: it-it
ms.lasthandoff: 06/14/2017

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

Questo articolo illustra l'uso di PowerShell per creare una data factory con una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL di Azure. Se non si ha familiarità con Azure Data Factory, prima di eseguire questa esercitazione vedere l'articolo [Introduzione ad Azure Data Factory](data-factory-introduction.md).   

In questa esercitazione si crea una pipeline contenente una sola attività: un'attività di copia che copia i dati da un archivio dati supportato a un archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md).

Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [Attività multiple in una pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Questo articolo non illustra tutti i cmdlet di Data factory. Per la documentazione completa su questi cmdlet, vedere [Data Factory Cmdlet Reference](/powershell/module/azurerm.datafactories) (Informazioni di riferimento sui cmdlet di Data Factory).
> 
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare una pipeline per trasformare i dati usando un cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti
- Completare i prerequisiti indicati nell'articolo sui [prerequisiti dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Installare **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Come installare e configurare Azure PowerShell).

## <a name="steps"></a>Passi
Di seguito sono elencati i passaggi da eseguire in questa esercitazione:

1. Creare una **data factory** di Azure. In questo passaggio viene creata una data factory denominata ADFTutorialDataFactoryPSH. 
2. Creare **servizi collegati** nella data factory. In questo passaggio si creano due servizi collegati di tipo Archiviazione di Azure e database SQL di Azure. 
    
    AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stato creato un contenitore e sono stati caricati i dati in questo account di archiviazione.   

    AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata una tabella SQL in questo database.   
3. Creare **set di dati** di input e di output nella data factory.  
    
    Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input specifica il contenitore e la cartella che contiene i dati di input.  

    Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB.
4. Creare una **pipeline** nella data factory. In questo passaggio viene creata una pipeline con un'attività di copia.   
    
    L'attività di copia esegue la copia dei dati da un BLOB nell'archivio BLOB di Azure a una tabella nel database SQL di Azure. È possibile usare un'attività di copia in una pipeline per copiare i dati da qualsiasi origine supportata a qualsiasi destinazione supportata. Per un elenco degli archivi dati supportati, vedere l'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Monitorare la pipeline. In questo passaggio vengono **monitorate** le sezioni dei set di dati di input e di output usando PowerShell.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
> [!IMPORTANT]
> Completare i [prerequisiti per l'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) se non è già stato fatto.   

Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, un'attività di copia per copiare dati da un archivio dati di origine a uno di destinazione e un'attività Hive HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. In questo passaggio iniziale viene creata la data factory.

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
    $df=New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    È possibile che questo nome sia già in uso. Rendere quindi univoco il nome della data factory aggiungendo un prefisso o suffisso, ad esempio ADFTutorialDataFactoryPSH05152017, ed eseguire nuovamente il comando.  

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome. Ad esempio, NomeUtenteADFTutorialDataFactoryPSH. Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactoryPSH. Per informazioni sugli elementi di Data Factory, vedere [Data Factory - Regole di denominazione](data-factory-naming-rules.md).

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Per creare istanze di Data Factory è necessario essere un collaboratore o amministratore della sottoscrizione di Azure.
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
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione non si usano servizi di calcolo come Azure HDInsight o Azure Data Lake Analytics, ma due archivi dati di tipo Archiviazione di Azure (origine) e database SQL di Azure (destinazione). 

Si creano quindi due servizi collegati denominati AzureStorageLinkedService e AzureSqlLinkedService di tipo AzureStorage e AzureSqlDatabase.  

AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. L'account di archiviazione è quello in cui, come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), è stato creato un contenitore e sono stati caricati i dati.   

AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata la tabella emp in questo database. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Creare un servizio collegato per un account di archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory.

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFGetStartedPSH** con il contenuto seguente. Creare la cartella ADFGetStartedPSH se non esiste già.

    > [!IMPORTANT]
    > Sostituire &lt;accountname&gt; e &lt;accountkey&gt; con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
2. In **Azure PowerShell** passare alla cartella **ADFGetStartedPSH**.
4. Eseguire il cmdlet **New-AzureRmDataFactoryLinkedService** per creare il servizio collegato **AzureStorageLinkedService**. Questo cmdlet e altri cmdlet di Data factory usati in questa esercitazione richiedono il passaggio di valori per i parametri **ResourceGroupName** e **DataFactoryName**. In alternativa, è possibile passare l'oggetto DataFactory restituito dal cmdlet New-AzureRmDataFactory senza digitare ResourceGroupName e DataFactoryName ogni volta che si esegue un cmdlet. 

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Di seguito è riportato l'output di esempio:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Un altro modo per creare questo servizio collegato è specificare il nome del gruppo di risorse e il nome della data factory invece di specificare l'oggetto DataFactory.  

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Creare un servizio collegato per un database SQL di Azure
In questo passaggio il database SQL di Azure viene collegato alla data factory.

1. Creare un file JSON denominato AzureSqlLinkedService.json nella cartella C:\ADFGetStartedPSH con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; e &lt;password&gt; con i nomi del server, database, account utente e password di Azure SQL.
    
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
2. Eseguire questo comando per creare un servizio collegato:

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```
    
    Di seguito è riportato l'output di esempio:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Verificare che l'impostazione **Consenti l'accesso a Servizi di Azure** sia attivata per il server di database SQL. Per verificare e attivare l'impostazione, seguire questa procedura:

    1. Accedere al [portale di Azure](https://portal.azure.com)
    2. Fare clic su **Altri servizi >** a sinistra e selezionare **Server SQL** nella categoria **DATABASE**.
    3. Selezionare il server nell'elenco di server SQL.
    4. Nel pannello del server SQL fare clic sul collegamento **Mostra impostazioni firewall**.
    5. Nel pannello **Impostazioni del firewall** fare clic su **ATTIVA** per **Consenti l'accesso a Servizi Azure**.
    6. Fare clic su **Save** sulla barra degli strumenti. 

## <a name="create-datasets"></a>Creare set di dati
Nel passaggio precedente sono stati creati servizi collegati per collegare l'account di archiviazione di Azure e un database SQL di Azure alla data factory. In questo passaggio vengono definiti due set di dati denominati InputDataset e OutputDataset, che rappresentano i dati di input e di output memorizzati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService e AzureSqlLinkedService.

Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input (InputDataset) specifica il contenitore e la cartella che contiene i dati di input.  

Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output (OututDataset) specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB. 

### <a name="create-an-input-dataset"></a>Creare un set di dati di input
In questo passaggio viene creato un set di dati denominato InputDataset che punta a un file BLOB (emp.txt) nella cartella radice di un contenitore BLOB (adftutorial) nella risorsa di archiviazione di Azure rappresentata dal servizio collegato AzureStorageLinkedService. Se non si specifica un valore per fileName (o lo si ignora), i dati di tutti i BLOB della cartella di input vengono copiati nella destinazione. In questa esercitazione si specifica un valore per fileName.  

1. Creare un file JSON denominato **InputDataset.json** nella cartella **C:\ADFGetStartedPSH** con il contenuto seguente:

    ```json
    {
        "name": "InputDataset",
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
            "linkedServiceName": "AzureStorageLinkedService",
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

    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

    | Proprietà | Descrizione |
    |:--- |:--- |
    | type | La proprietà type è impostata su **AzureBlob** perché i dati risiedono in un archivio BLOB di Azure. |
    | linkedServiceName | Fa riferimento all'oggetto **AzureStorageLinkedService** creato in precedenza. |
    | folderPath | Specifica il **contenitore** BLOB e la **cartella** che contiene i BLOB di input. In questa esercitazione adftutorial è il contenitore BLOB e la cartella è la cartella radice. | 
    | fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, vengono selezionati tutti i file da folderPath. In questa esercitazione come fileName si specifica **emp.txt** e viene quindi selezionato per l'elaborazione solo tale file. |
    | format -> type |Il file di input è in formato testo, quindi viene usato **TextFormat**. |
    | columnDelimiter | Le colonne nel file di input sono delimitate da **virgola (`,`)**. |
    | frequenza/intervallo | La frequenza è impostata su **Hour** e l'intervallo è impostato su **1**, quindi le sezioni di input sono disponibili con cadenza **oraria**. In altre parole, il servizio Data Factory cerca i dati di input ogni ora nella cartella radice del contenitore BLOB specificato (**adftutorial**). Cerca i dati compresi tra l'ora di inizio e di fine della pipeline e non prima o dopo queste ore.  |
    | external | Questa proprietà è impostata su **true** se i dati non vengono generati da questa pipeline. I dati di input in questa esercitazione sono nel file emp.txt, che non viene generato da questa pipeline, quindi questa proprietà viene impostata su true. |

    Per altre informazioni su queste proprietà JSON, vedere l'articolo relativo al [connettore BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties).
2. Eseguire il comando seguente per creare il set di dati di Data factory.

    ```PowerShell  
    New-AzureRmDataFactoryDataset $df -File .\InputDataset.json
    ```
    Di seguito è riportato l'output di esempio:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Creare un set di dati di output
In questa parte del passaggio si crea un set di dati di output denominato **OutputDataset**. Questo set di dati punta a una tabella SQL nel database SQL di Azure rappresentato da **AzureSqlLinkedService**. 

1. Creare un file JSON denominato **OutputDataset.json** nella cartella **C:\ADFGetStartedPSH** con il contenuto seguente:

    ```json
    {
        "name": "OutputDataset",
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

    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

    | Proprietà | Descrizione |
    |:--- |:--- |
    | type | La proprietà type viene impostata su **AzureSqlTable** perché i dati vengono copiati in una tabella in un database SQL di Azure. |
    | linkedServiceName | Fa riferimento all'oggetto **AzureSqlLinkedService** creato in precedenza. |
    | tableName | Specifica la **tabella** in cui i dati vengono copiati. | 
    | frequenza/intervallo | La frequenza viene impostata su **Hour** e l'intervallo è **1**, quindi le sezioni di output vengono generate **ogni ora** tra l'ora di inizio e di fine della pipeline e non prima o dopo queste ore.  |

    La tabella emp del database include tre colonne: **ID**, **FirstName** e **LastName**. ID è una colonna Identity, quindi in questo caso è necessario specificare solo **FirstName** e **LastName**.

    Per altre informazioni su queste proprietà JSON, vedere l'articolo relativo al [connettore SQL di Azure](data-factory-azure-sql-connector.md#dataset-properties).
2. Eseguire il comando seguente per creare il set di dati di Data Factory.

    ```PowerShell   
    New-AzureRmDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Di seguito è riportato l'output di esempio:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'**attività di copia** che usa **InputDataset** come input e **OutputDataset** come output.

Attualmente, è il set di dati di output a determinare la pianificazione. In questa esercitazione il set di dati di output viene configurato per generare una sezione una volta ogni ora. La pipeline ha un'ora di inizio e un'ora di fine intervallate da un giorno, ovvero 24 ore. Vengono quindi generate dalla pipeline 24 sezioni di set di dati di output. 


1. Creare un file JSON denominato **ADFTutorialPipeline.json** nella cartella **C:\ADFGetStartedPSH** con i contenuti seguenti:

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    Tenere presente quanto segue:
   
    - Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Nelle soluzioni Data Factory è anche possibile usare le [attività di trasformazione dei dati](data-factory-data-transformation-activities.md).
    - L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. 
    - Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Per un elenco completo degli archivi dati supportati dall'attività di copia come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Per informazioni su come usare uno specifico archivio dati supportato come origine/sink, fare clic sul collegamento nella tabella.  
     
    Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. È possibile specificare solo la parte relativa alla data e ignorare la parte relativa all'ora, ad esempio "2016-02-03", che equivale a "2016-02-03T00:00:00Z".
     
    Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2016-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. 
     
    Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.
     
    Nell'esempio precedente sono visualizzate 24 sezioni di dati, perché viene generata una sezione di dati ogni ora.

    Per le descrizioni delle proprietà JSON nella definizione di una pipeline, vedere l'articolo su come [creare pipeline](data-factory-create-pipelines.md). Per le descrizioni delle proprietà JSON nella definizione di un'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Per le descrizioni delle proprietà JSON supportate da BlobSource, vedere l'articolo relativo al [connettore BLOB di Azure](data-factory-azure-blob-connector.md). Per le descrizioni delle proprietà JSON supportate da SqlSink, vedere l'articolo relativo al [connettore del database SQL di Azure](data-factory-azure-sql-connector.md).
2. Eseguire il comando seguente per creare la tabella di Data Factory.

    ```PowerShell   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Di seguito è riportato l'output di esempio: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Congratulazioni.** È stata creata una data factory di Azure con una pipeline per copiare dati da un archivio BLOB di Azure a un database SQL di Azure. 

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usato Azure PowerShell per monitorare le attività in un'istanza di Azure Data Factory.

1. Sostituire &lt;DataFactoryName&gt; con il nome della data factory ed eseguire **Get-AzureRmDataFactory**, quindi assegnare l'output a una variabile $df.

    ```PowerShell  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    ad esempio:
    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```
    
    Stampare quindi il contenuto di $df per visualizzare l'output seguente: 
    
    ```
    PS C:\ADFGetStartedPSH> $df
    
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
2. Eseguire **Get-AzureRmDataFactorySlice** per ottenere dettagli su tutte le sezioni di **OutputDataset**, ovvero il set di dati di output della pipeline.  

    ```PowerShell   
    Get-AzureRmDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Questa impostazione deve corrispondere al valore **Start** nel file JSON della pipeline. Dovrebbero essere visualizzate 24 sezioni, una per ogni ora a partire dalle 00.00 del giorno corrente alle 00.00 del giorno successivo.

   Di seguito sono riportate tre sezioni di esempio dell'output: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0   

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Eseguire **Get-AzureRmDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione **specifica**. Copiare il valore di data e ora dall'output del comando precedente per specificare il valore per il parametro StartDateTime. 

    ```PowerShell  
    Get-AzureRmDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Di seguito è riportato l'output di esempio: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Vedere le [informazioni di riferimento per i cmdlet di Data factory](/powershell/module/azurerm.datafactories) per la documentazione completa sui cmdlet di Data factory.

## <a name="summary"></a>Riepilogo
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato PowerShell per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare **servizi collegati**:

   a. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.     
   b. Un servizio collegato di **Azure SQL** per collegare il database SQL che contiene i dati di output.
3. Creare **set di dati** che descrivono dati di input e di output per le pipeline.
4. Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati usati l'archivio BLOB di Azure come archivio dati di origine e un database SQL di Azure come archivio dati di destinazione in un'operazione di copia. La tabella seguente contiene un elenco degli archivi dati supportati come origini e come destinazioni dall'attività di copia: 

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Per informazioni su come copiare dati da/in un archivio dati, fare clic sul collegamento relativo all'archivio dati nella tabella. 


