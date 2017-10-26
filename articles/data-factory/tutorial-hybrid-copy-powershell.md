---
title: Copiare dati locali nel cloud con Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un archivio dati locale al cloud di Azure usando un runtime di integrazione self-hosted di Azure Data Factory.
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
ms.date: 09/14/2017
ms.author: jingwang
ms.openlocfilehash: 9aac9c9bcc609a91415438279419d4cc8e237fcb
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Copiare dati tra origini locali e cloud
Azure Data Factory è un servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi. Usando Azure Data Factory è possibile creare e pianificare flussi di lavoro (denominati pipeline) basati sui dati che possono inserire dati da archivi diversi, elaborarli e trasformarli tramite servizi di calcolo come Hadoop di Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e pubblicare l'output in archivi come Azure SQL Data Warehouse per l'uso da parte di applicazioni di business intelligence (BI).

In questa esercitazione si usa Azure PowerShell per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Viene creato e usato un runtime di integrazione self-hosted di Azure Data Factory, che consente l'integrazione di archivi dati locali e cloud.  Per informazioni sull'uso di altri strumenti/SDK per creare una data factory, vedere le [guide introduttive](quickstart-create-data-factory-dot-net.md).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare e crittografare un servizio collegato SQL Server locale nel runtime di integrazione self-hosted.
> * Creare il servizio collegato Archiviazione di Azure.
> * Creare i set di dati per SQL Server e Archiviazione di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione di pipeline e attività.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **SQL Server**. Usare un database di SQL Server locale come archivio dati di **origine** in questa esercitazione.
* **Account di archiviazione di Azure**. In questa esercitazione, l'archivio BLOB di Azure viene usato come archivio dati di **destinazione/sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
* **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell).

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
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Tenere presente quanto segue:

    * È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Per creare istanze di Data Factory è necessario essere un collaboratore o amministratore della sottoscrizione di Azure.
    * Data Factory consente attualmente di creare data factory solo nelle aree Stati Uniti orientali e Stati Uniti orientali 2. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

## <a name="create-a-self-hosted-ir"></a>Creare un runtime di integrazione self-hosted

Questa sezione consente di creare un runtime di integrazione self-hosted e associarlo a un nodo locale (computer).

1. Creare un runtime di integrazione self-hosted. Usare un nome univoco nel caso esista un altro runtime di integrazione con lo stesso nome.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Di seguito è riportato l'output di esempio:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Eseguire questo comando per recuperare lo stato del runtime di integrazione creato.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Di seguito è riportato l'output di esempio:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Eseguire questo comando per recuperare le chiavi di autenticazione per la registrazione del runtime di integrazione self-hosted nel servizio Data Factory nel cloud. Copiare una delle chiavi per registrare il runtime di integrazione self-hosted.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Di seguito è riportato l'output di esempio:

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) il runtime di integrazione self-hosted in un computer Windows locale e usare la chiave di autenticazione ottenuta nel passaggio precedente per registrare manualmente il runtime.

   ![Registrare il runtime di integrazione](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Al termine della registrazione del runtime di integrazione self-hosted viene visualizzato il messaggio seguente:

   ![Registrazione completata](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   Quando il nodo viene connesso al servizio cloud, viene visualizzata la pagina seguente:

   ![Connessione del nodo](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Creare servizi collegati

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creare un servizio collegato Archiviazione di Azure (destinazione/sink)

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente. Creare la cartella ADFv2Tutorial, se non esiste già.  Sostituire &lt;accountname&gt; e &lt;accountkey&gt; con il nome e la chiave dell'account di archiviazione di Azure.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. In **Azure PowerShell** passare alla cartella **ADFv2Tutorial**.

   Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureStorageLinkedService**. I cmdlet usati in questa esercitazione accettano valori per i parametri **ResourceGroupName** e **DataFactoryName**. In alternativa, è possibile passare l'oggetto **DataFactory** restituito dal cmdlet Set-AzureRmDataFactoryV2 senza digitare ResourceGroupName e DataFactoryName ogni volta che si esegue un cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Di seguito è riportato un output di esempio:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creare e crittografare un servizio collegato SQL Server (origine)

1. Creare un file JSON denominato **SqlServerLinkedService.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente. Sostituire **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** e **&lt;password>** con i valori della propria istanza di SQL Server prima di salvare il file. Sostituire **&lt;integration** **runtime** **name>** con il nome del runtime di integrazione.

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
2. Per crittografare i dati sensibili dal payload JSON nel runtime di integrazione self-hosted locale, è possibile eseguire **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e passare il payload JSON precedente. Con questa crittografia, le credenziali vengono crittografate con Data Protection API (DPAPI) e archiviate in locale nel nodo del runtime di integrazione self-hosted. Il payload di output può essere reindirizzato a un altro file JSON (in questo caso, encryptedLinkedService.json) che contiene le credenziali crittografate.

    Prima di eseguire il comando, sostituire **&lt;integration runtime name&gt;** con il nome del runtime di integrazione.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Eseguire questo comando usando il file JSON del passaggio precedente per creare **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creare set di dati

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Preparare un'istanza locale di SQL Server per l'esercitazione

In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure). Prima di creare i set di dati, eseguire questa procedura (la procedura dettagliata segue l'elenco):

- Creare una tabella denominata **emp** nel database SQL Server aggiunto come servizio collegato all'istanza di Data factory e inserire una coppia di voci di esempio nella tabella.
- Creare un contenitore BLOB denominato **adftutorial** nell'account di archiviazione BLOB di Azure aggiunto come servizio collegato alla data factory.


1. Nel database specificato per il servizio collegato di SQL Server locale (**SqlServerLinkedService**) usare lo script SQL seguente per creare la tabella **emp** nel database.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Inserire un esempio nella tabella:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Creare un set di dati per il database SQL di origine

1. Creare un file JSON denominato **SqlServerDataset.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Per creare il set di dati **SqlServerDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Creare un set di dati per l'archivio BLOB di Azure sink

1. Creare un file JSON denominato **AzureBlobDataset.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente:

    > [!IMPORTANT]
    > Questo codice di esempio presuppone che nell'archivio BLOB di Azure sia presente un contenitore denominato **adftutorial**.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Per creare il set di dati **AzureBlobDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Creare le pipeline

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Creare la pipeline "SqlServerToBlobPipeline"

1. Creare un file JSON denominato **SqlServerToBlobPipeline.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Per creare la pipeline **SQLServerToBlobPipeline**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Avviare e monitorare un'esecuzione della pipeline

1. Avviare un'esecuzione della pipeline "SQLServerToBlobPipeline" e acquisire l'ID di esecuzione della pipeline per il monitoraggio futuro.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Eseguire questo script per verificare continuamente lo stato di esecuzione della pipeline "**SQLServerToBlobPipeline**" e stampare il risultato finale.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. È possibile ottenere l'ID di esecuzione della pipeline "**SQLServerToBlobPipeline**" e verificare il risultato dettagliato dell'esecuzione dell'attività come illustrato di seguito.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Connettersi all'archivio BLOB di Azure sink e verificare che i dati siano stati copiati correttamente dal database SQL di Azure.

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare e crittografare un servizio collegato SQL Server locale nel runtime di integrazione self-hosted.
> * Creare il servizio collegato Archiviazione di Azure.
> * Creare i set di dati per SQL Server e Archiviazione di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione di pipeline e attività.

Per un elenco degli archivi dati supportati da Azure Data Factory come origini e sink, vedere l'articolo che illustra gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati in blocco da un'origine a una destinazione:

> [!div class="nextstepaction"]
>[Copiare dati in blocco](tutorial-bulk-copy.md)
