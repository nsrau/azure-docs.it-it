---
title: Copiare dati da SQL Server a un archivio BLOB usando Azure Data Factory | Microsoft Docs
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: afd7735712d03110a67509a7e94d336219a65b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Esercitazione: Copiare i dati da un'istanza di SQL Server locale a un archivio BLOB di Azure
In questa esercitazione si usa Azure PowerShell per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Viene creato e usato un runtime di integrazione self-hosted di Azure Data Factory, che consente l'integrazione di archivi dati locali e cloud.  Per informazioni sull'uso di altri strumenti/SDK per creare una data factory, vedere le [guide introduttive](quickstart-create-data-factory-dot-net.md).

Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md). 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure. 
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 o 2016. 
Usare un database di SQL Server locale come archivio dati di **origine** in questa esercitazione. Creare una tabella denominata **emp** nel database SQL Server e inserire una coppia di voci di esempio nella tabella.

1. Avviare **SQL Server Management Studio**. Se si usa SQL Server 2016, potrebbe essere necessario installare separatamente SQL Server Management Studio dall'[area download](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Connettersi all'istanza di SQL Server usando le credenziali. 
3. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**. Nella finestra di dialogo **Nuovo database** immettere un **nome** per il database e fare clic su **OK**. 
4. Eseguire questo script di query sul database per creare la tabella **emp**. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul **database** creato e scegliere **Nuova query**. 

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
2. Eseguire questi comandi sul database per inserire alcuni dati di esempio nella tabella:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati di **destinazione/sink**. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.

#### <a name="get-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa guida introduttiva si usano il nome e la chiave dell'account di archiviazione di Azure. La procedura seguente illustra i passaggi per recuperare il nome e la chiave dell'account di archiviazione. 

1. Avviare un Web browser e passare al [portale di Azure](https://portal.azure.com). Eseguire l'accesso con il nome utente e la password di Azure. 
2. Fare clic su **Altri servizi** nel menu a sinistra, usare la parola chiave **Archiviazione** come filtro e selezionare **Account di archiviazione**.

    ![Cercare l'account di archiviazione](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Nell'elenco degli account di archiviazione filtrare, se necessario, e quindi selezionare il **proprio account di archiviazione**. 
4. Nella pagina **Account di archiviazione** selezionare **Chiavi di accesso** nel menu.

    ![Recuperare il nome e la chiave dell'account di archiviazione](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copiare i valori dei campi **Nome account di archiviazione** e **key1** negli Appunti. Incollarli in un blocco note o in qualsiasi altro editor e salvare. Nell'esercitazione si usano il nome e la chiave dell'account di archiviazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato adftutorial nell'archivio BLOB di Azure. 

1. Installare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), se non è già presente nel computer. 
2. Avviare **Microsoft Azure Storage Explorer** nel computer.   
3. Nella finestra **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) selezionare **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e fare clic su **Next** (Avanti). Se la finestra **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) non viene visualizzata, fare clic con il pulsante destro del mouse su **Storage Accounts** (Account di archiviazione) nella visualizzazione struttura ad albero e scegliere **Connect to Azure storage** (Connetti ad Archiviazione di Azure). 

    ![Connessione ad Archiviazione di Azure](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. Nella finestra **Attach using Name and Key** (Collega con nome e chiave) incollare il **nome dell'account** e la **chiave dell'account** salvati nel passaggio precedente. Quindi fare clic su **Next**. 
5. Nella finestra **Connection Summary** (Riepilogo connessione) fare clic su **Connect** (Connetti).
6. Verificare che l'account di archiviazione sia incluso nella visualizzazione struttura ad albero sotto **(Local and Attached)** (Locale e collegato) -> **Storage Accounts** (Account di archiviazione). 
7. Espandere **Blob Containers** (Contenitori BLOB) e verificare che il contenitore BLOB **adftutorial** non sia presente. Se esiste già, ignorare i passaggi successivi per la creazione del contenitore. 
8. Fare clic con il pulsante destro del mouse su **Blob Containers** (Contenitori BLOB) e scegliere **Create Blob Container** (Crea contenitore BLOB) dal menu di scelta rapida.

    ![Creare un contenitore BLOB](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Immettere **adftutorial** come nome e premere **INVIO**. 
10. Verificare che il contenitore **adftutorial** sia selezionato nella visualizzazione struttura ad albero. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno. 

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

## <a name="create-a-self-hosted-ir"></a>Creare un runtime di integrazione self-hosted

Questa sezione consente di creare un runtime di integrazione self-hosted e associarlo a un nodo locale (computer).

1. Creare una variabile per il nome del runtime di integrazione. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Creare un runtime di integrazione self-hosted. Usare un nome univoco nel caso esista un altro runtime di integrazione con lo stesso nome.

   ```powershell
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
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Installare il runtime di integrazione
1. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) il runtime di integrazione self-hosted in un computer Windows locale, quindi eseguire l'installazione. 
2. Nella pagina iniziale dell'**installazione guidata di Microsoft Integration Runtime** fare clic su **Avanti**.  
3. Nella pagina **Contratto di licenza con l'utente finale** accettare le condizioni e fare clic su **Avanti**. 
4. Nella pagina **Cartella di destinazione** fare clic su **Avanti**. 
5. Nella pagina **Pronto per l'installazione** fare clic su **Installa**. 
6. Se viene visualizzato un messaggio di avviso relativo al computer configurato per passare alla modalità di sospensione o di ibernazione quando non è in uso, fare clic su **OK**. 
7. Nella pagina **Installazione di Microsoft Integration Runtime completata** fare clic su **Fine**.
8. Nella pagina **Registra Runtime di integrazione (self-hosted)** incollare la chiave salvata nella sezione precedente e fare clic su **Registra**. 

   ![Registrare il runtime di integrazione](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Al termine della registrazione del runtime di integrazione self-hosted viene visualizzato il messaggio seguente:

   ![Registrazione completata](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. Nella pagina **Nuovo nodo di Runtime di integrazione (self-hosted)** fare clic su **Avanti**. 

    ![Pagina Nuovo nodo di Runtime di integrazione](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. Nella pagina **Canale di comunicazione Intranet** fare clic su **Ignora**. È possibile selezionare un certificato TLS/SSL per proteggere la comunicazione tra nodi in un ambiente di runtime di integrazione di più nodi. 

    ![Pagina Canale di comunicazione Intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. Nella pagina **Registra Runtime di integrazione (self-hosted)** fare clic su **Avvia Configuration Manager**. 
6. Quando il nodo viene connesso al servizio cloud, viene visualizzata la pagina seguente:

   ![Connessione del nodo](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Creare servizi collegati

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creare un servizio collegato Archiviazione di Azure (destinazione/sink)

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente. Creare la cartella ADFv2Tutorial se non esiste già.  

    > [!IMPORTANT]
    > Sostituire &lt;accountname&gt; e &lt;accountkey&gt; con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file.

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

1. Creare un file JSON denominato **SqlServerLinkedService.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente. Sostituire **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** e **&lt;password>** con i valori della propria istanza di SQL Server prima di salvare il file. 

    > [!IMPORTANT]
    > Sostituire **&lt;integration** **runtime** **name>** con il nome del runtime di integrazione.

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
2. Per crittografare i dati sensibili dal payload JSON nel runtime di integrazione self-hosted locale, eseguire **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e passare il payload JSON precedente. Con questa crittografia, le credenziali vengono crittografate con Data Protection API (DPAPI). Le credenziali crittografate vengono archiviate in locale (computer locale) nel nodo del runtime di integrazione self-hosted. Il payload di output può essere reindirizzato a un altro file JSON (in questo caso, encryptedLinkedService.json) che contiene le credenziali crittografate.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Eseguire questo comando usando il file JSON del passaggio precedente per creare **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure).

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
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata `fromonprem` nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file **dbo.emp.txt**. Usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che l'output sia stato creato. 

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure. 
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Per un elenco degli archivi dati supportati da Azure Data Factory, gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati in blocco da un'origine a una destinazione:

> [!div class="nextstepaction"]
>[Copiare dati in blocco](tutorial-bulk-copy.md)
