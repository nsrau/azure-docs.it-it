<properties 
    pageTitle="Esercitazione: Creare una pipeline con l'attività di copia usando l'API REST | Microsoft Azure" 
    description="In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando l'API REST." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>


# <a name="tutorial:-create-a-pipeline-with-copy-activity-using-rest-api"></a>Esercitazione: Creare una pipeline con l'attività di copia usando l'API REST
> [AZURE.SELECTOR]
- [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
- [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Questa esercitazione mostra come creare e monitorare una data factory di Azure con l'API REST. La pipeline nella data factory usa un'attività di copia per copiare i dati dall'archivio BLOB di Azure al database SQL di Azure.

> [AZURE.NOTE] 
> Questo articolo non illustra tutte le API REST di Data Factory. Per la documentazione completa sui cmdlet di Data Factory, vedere [Informazioni di riferimento sull'API REST di Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) .
  

## <a name="prerequisites"></a>Prerequisiti

- Vedere [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ed eseguire i passaggi relativi ai **prerequisiti** .
- Installare [Curl](https://curl.haxx.se/dlwiz/) nel computer. Lo strumento Curl viene usato insieme ai comandi REST per creare una data factory. 
- Seguire le istruzioni disponibili in [questo articolo](../resource-group-create-service-principal-portal.md) per: 
    1. Creare un'applicazione Web denominata **ADFCopyTutorialApp** in Azure Active Directory.
    2. Ottenere i valori per l'**ID client** e la **chiave privata**. 
    3. Ottenere l' **ID tenant**. 
    4. Assegnare l'applicazione **ADFCopyTutorialApp** al ruolo **Collaboratore Data Factory**.  
- Installare [Azure PowerShell](../powershell-install-configure.md).  
- Avviare **Azure PowerShell** ed eseguire il comando seguente. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
    1. Eseguire il comando seguente e immettere il nome utente e la password usati per accedere al portale di Azure.
    
            Login-AzureRmAccount   
    2. Eseguire il comando seguente per visualizzare tutte le sottoscrizioni per l'account.

            Get-AzureRmSubscription 
    3. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **&lt;NameOfAzureSubscription**&gt; con il nome della sottoscrizione di Azure. 

            Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    1. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente in PowerShell.  

            New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

        Se il gruppo di risorse esiste già, specificare se deve essere aggiornato (Y) o mantenuto invariato (N). 

        Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato ADFTutorialResourceGroup. Se si usa un gruppo di risorse diverso, è necessario usare il nome del gruppo di risorse invece di ADFTutorialResourceGroup in questa esercitazione.
  
## <a name="create-json-definitions"></a>Creare le definizioni JSON
Creare i file JSON seguenti nella cartella che include curl.exe. 

### <a name="datafactory.json"></a>datafactory.json 
> [AZURE.IMPORTANT] Il nome deve essere univoco a livello globale, quindi è consigliabile aggiungere un prefisso/suffisso ad ADFCopyTutorialDF per renderlo univoco. 

    {  
        "name": "ADFCopyTutorialDF",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservice.json"></a>azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso nelle risorse di archiviazione](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

### <a name="azuersqllinkedservice.json"></a>azuersqllinkedservice.json
> [AZURE.IMPORTANT] Sostituire **servername**, **databasename**, **username** e **password** con il nome del server di Azure SQL, il nome del database SQL, l'account utente e la password per l'account.  

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }


### <a name="inputdataset.json"></a>inputdataset.json

    {
      "name": "AzureBlobInput",
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
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
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

La definizione JSON precedente definisce un set di dati denominato **AzureBlobInput**, che rappresenta dati di input per un'attività nella pipeline. Specifica anche che i dati di input si trovano nel file **emp.txt**, incluso nel contenitore BLOB **adftutorial**. 

 Tenere presente quanto segue: 

- Il set di dati **type** è impostato su **AzureBlob**.
- L'oggetto **linkedServiceName** è impostato su **AzureStorageLinkedService**. 
- **folderPath** è impostato sul contenitore **adftutorial** e **fileName** è impostato su **emp.txt**.  
- L'oggetto **type** di format è impostato su **TextFormat**.
- Nel file di testo sono presenti due campi, **FirstName** e **LastName**, separati da una virgola (**columnDelimiter**). 
- L'oggetto **availability** è impostato su **hourly**. L'oggetto frequency è impostato su hour e l'oggetto interval è impostato su 1. Quindi, il servizio Data Factory cerca i dati di input ogni ora nella cartella radice del contenitore BLOB**adftutorial**specificato. 

Se non si specifica **fileName** per un set di dati di input, tutti i file e i BLOB della cartella di input **folderPath** vengono considerati input. Se si specifica un oggetto fileName in JSON, solo il file/BLOB specificato viene considerato un input.

Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.&lt;Guid&gt;.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà **partitionedBy**. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv. 

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],


### <a name="outputdataset.json"></a>outputdataset.json
    
    {
      "name": "AzureSqlOutput",
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


La definizione JSON precedente definisce un set di dati denominato **AzureSqlOutput**, che rappresenta dati di output data per un'attività nella pipeline. Specifica anche che i risultati vengono archiviati nella tabella **emp** nel database rappresentato da AzureSqlLinkedService. La sezione **availability** specifica che il set di dati di output viene prodotto ogni ora (frequency: hour e interval: 1).

Tenere presente quanto segue: 

- Il set di dati **type** è impostato su **AzureSQLTable**.
- L'oggetto **linkedServiceName** è impostato su **AzureSqlLinkedService**.
- L'oggetto **tablename** è impostato su **emp**.
- La tabella emp del database include tre colonne: **ID**, **FirstName** e **LastName**. ID è una colonna Identity, quindi in questo caso è necessario specificare solo **FirstName** e **LastName**.
- L'oggetto **availability** è impostato su **hourly**. L'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**.  Il servizio Data Factory genera una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.

### <a name="pipeline.json"></a>pipeline.json

    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
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
        "start": "2016-08-12T00:00:00Z",
        "end": "2016-08-13T00:00:00Z"
      }
    }


Tenere presente quanto segue:

- Nella sezione activities esiste una sola attività con l'oggetto **type** impostato su **CopyActivity**.
- L'input per l'attività è impostato su **AzureBlobInput** e l'output per l'attività è impostato su **AzureSqlOutput**.
- Nella sezione **transformation** **BlobSource** viene specificato come tipo di origine e **SqlSink** come tipo di sink.

Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. È possibile specificare solo la parte relativa alla data e ignorare la parte relativa all'ora, ad esempio "2015-02-03", che equivale a "2015-02-03T00:00:00Z".

Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. 

Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.

Nell'esempio sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
    
> [AZURE.NOTE] Per informazioni dettagliate sulle proprietà JSON usate nell'esempio precedente, vedere [Anatomia di una pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) .

## <a name="set-global-variables"></a>Configurare le variabili globali

In Azure PowerShell eseguire i comandi seguenti dopo avere sostituito i valori con quelli personalizzati:

> [AZURE.IMPORTANT] Per istruzioni su come ottenere i valori per ID client, segreto client, ID tenant e ID sottoscrizione, vedere la sezione [Prerequisiti](#prerequisites) .   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "ADFCopyTutorialDF"

## <a name="authenticate-with-aad"></a>Eseguire l'autenticazione con AAD 
Eseguire il comando seguente per l'autenticazione con Azure Active Directory (AAD). 

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 

## <a name="create-data-factory"></a>Creare un'istanza di Data Factory

In questo passaggio si crea un'istanza di Azure Data Factory denominata **ADFCopyTutorialDF**. Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, un'attività di copia per copiare dati da un'origine a un archivio dati di destinazione. Un'attività Hive di HDInsight per eseguire script Hive per trasformare i dati di input e produrre dati di output. Eseguire i comandi seguenti per creare la data factory: 

1. Assegnare il comando alla variabile denominata **cmd**. 

    Verificare che il nome della data factory specificato qui (ADFCopyTutorialDF) corrisponda al nome specificato in **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Eseguire il comando usando **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se la data factory è stata creata correttamente, in **results** viene visualizzato il codice JSON per la data factory. In caso contrario, viene visualizzato un messaggio di errore.  

        Write-Host $results

Tenere presente quanto segue:
 
- È necessario specificare un nome univoco globale per la Data factory di Azure. Se nei risultati viene visualizzato l'errore **Il nome "ADFCopyTutorialDF" per la data factory non è disponibile**, seguire questa procedura:  
    1. Cambiare il nome, ad esempio nomeutenteADFCopyTutorialDF, nel file **datafactory.json** .
    2. Nel primo comando in cui viene assegnato un valore alla variabile **$cmd** sostituire ADFCopyTutorialDF con il nuovo nome ed eseguire il comando. 
    3. Eseguire i due comandi seguenti per richiamare l'API REST per creare la data factory e stampare i risultati dell'operazione. 
    
    Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md) .
- Per creare istanze di data factory, è necessario essere un collaboratore/amministratore della sottoscrizione di Azure.
- Il nome della data factory può essere registrato come nome DNS in futuro e quindi divenire visibile pubblicamente.
- Se viene visualizzato l'errore "**La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**", eseguire una di queste operazioni e provare a ripetere la pubblicazione: 

    - In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        È possibile eseguire questo comando per verificare che il provider di Data Factory sia registrato. 
    
            Get-AzureRmResourceProvider
    - Accedere usando la sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) e passare al pannello Data Factory oppure creare un'istanza di Data Factory nel portale di Azure. Questa azione registra automaticamente il provider.

Prima di creare una pipeline è necessario creare alcune entità di Data factory. Prima di tutto, creare i servizi collegati per collegare gli archivi dati di origine e di destinazione al proprio archivio dati. Quindi, definire i set di dati di input e di output per rappresentare i dati in archivi dati collegati. Infine, creare la pipeline con un'attività che usa questi set di dati.

## <a name="create-linked-services"></a>Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere una risorsa di archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale che include dati di input o archivia dati di output per una pipeline di Data Factory. Un servizio di calcolo è un servizio che elabora dati di input e produce dati di output. 

In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService** e **AzureSqlLinkedService**. Il servizio collegato AzureStorageLinkedService collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure alla data factory **ADFCopyTutorialDF**. Più avanti in questa esercitazione viene creata una pipeline che copia i dati da un contenitore BLOB di AzureStorageLinkedService e li inserisce in una tabella SQL di AzureSqlLinkedService.

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. In questa esercitazione si usa l'account di archiviazione di Azure per archiviare i dati di input. 

1. Assegnare il comando alla variabile denominata **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Eseguire il comando usando **Invoke-Command**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il servizio collegato è stato creato correttamente, in **results** viene visualizzato il codice JSON per il servizio collegato. In caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results

### <a name="create-azure-sql-linked-service"></a>Creare un servizio collegato di Azure SQL
In questo passaggio il database SQL di Azure viene collegato alla data factory. Con questa esercitazione viene usato lo stesso database SQL di Azure per archiviare i dati di output.

1. Assegnare il comando alla variabile denominata **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Eseguire il comando usando **Invoke-Command**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il servizio collegato è stato creato correttamente, in **results** viene visualizzato il codice JSON per il servizio collegato. In caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results

## <a name="create-datasets"></a>Creare set di dati

Nel passaggio precedente sono stati creati i servizi collegati **AzureStorageLinkedService** e **AzureSqlLinkedService** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory **ADFCopyTutorialDF**. In questo passaggio vengono creati set di dati che rappresentano i dati di input e di output per l'attività di copia nella pipeline che viene creata nel passaggio successivo. 

Il set di dati di input in questa esercitazione fa riferimento a un contenitore BLOB in Archiviazione di Azure a cui punta AzureStorageLinkedService. Il set di dati di output fa riferimento a una tabella SQL nel database SQL di Azure a cui punta AzureSqlLinkedService.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Preparare l'archiviazione BLOB di Azure e il database SQL Azure per l'esercitazione
Eseguire i passaggi seguenti per preparare l'archivio BLOB di Azure e il database SQL di Azure per questa esercitazione. 
 
* Creare un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure a cui fa riferimento **AzureStorageLinkedService**. 
* Creare e caricare un file di testo **emp.txt** come BLOB per il contenitore **adftutorial**. 
* Creare una tabella denominata **emp** nel database SQL di Azure a cui fa riferimento **AzureSqlLinkedService**.


1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\ADFGetStartedPSH** sul disco rigido. 

        John, Doe
        Jane, Doe
                
2. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Azure Storage Explorer](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure.  


        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    Se nel computer è installato SQL Server 2014, seguire le istruzioni disponibili nel [passaggio 2 per la connessione al database SQL dell'articolo Gestione di database SQL di Azure tramite SQL Server Management Studio][sql-management-studio] per connettersi al server SQL di Azure ed eseguire lo script SQL.

    Se il client non è autorizzato ad accedere al server di Azure SQL, è necessario configurare il firewall per il server di Azure SQL in modo da consentire l'accesso dal computer (indirizzo IP). Per informazioni sulla procedura per configurare il firewall per il server Azure SQL, vedere [questo articolo](../sql-database/sql-database-configure-firewall-settings.md) .
        
### <a name="create-input-dataset"></a>Creare set di dati di input 
In questo passaggio viene creato un set di dati denominato **AzureBlobInput** che fa riferimento a un contenitore BLOB in Archiviazione di Azure rappresentato dal servizio collegato **AzureStorageLinkedService**. Questo contenitore BLOB (**adftutorial**) contiene i dati di input nel file: **emp.txt**. 

1. Assegnare il comando alla variabile denominata **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Eseguire il comando usando **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, in **results** viene visualizzato il codice JSON per il set di dati. In caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results

### <a name="create-output-dataset"></a>Creare il set di dati di output
In questo passaggio si crea una tabella di output denominata **AzureSqlOutput**. Questo set di dati punta a una tabella SQL (**emp**) nel database SQL di Azure rappresentato da **AzureSqlLinkedService**. La pipeline copia i dati dal BLOB di input e li inserisce nella tabella **emp** . 

1. Assegnare il comando alla variabile denominata **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Eseguire il comando usando **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, in **results** viene visualizzato il codice JSON per il set di dati. In caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'**attività di copia** che usa **AzureBlobInput** come input e **AzureSqlOutput** come output.

1. Assegnare il comando alla variabile denominata **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Eseguire il comando usando **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, in **results** viene visualizzato il codice JSON per il set di dati. In caso contrario, viene visualizzato un messaggio di errore.  

        Write-Host $results

**Congratulazioni.**  È stata creata una data factory di Azure con una pipeline che copia dati da un archivio BLOB di Azure al database SQL di Azure.

## <a name="monitor-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usata l'API REST di Azure Data Factory per monitorare le sezioni prodotte dalla pipeline.

    $ds ="AzureSqlOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;


    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }

Eseguire questi comandi fino a quando non viene visualizzata una sezione con stato **Pronta** o **Non riuscita**. Quando lo stato della sezione è Pronta, cercare dati di output nella tabella **emp** del database SQL di Azure. 

Per ogni sezione vengono copiate due righe di dati dal file di origine nella tabella emp del database SQL di Azure. Nella tabella emp vengono quindi visualizzati 24 nuovi record quando tutte le sezioni sono state elaborate correttamente e hanno stato Pronta. 


## <a name="summary"></a>Riepilogo
In questa esercitazione è stata usata l'API REST per creare una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. Ecco i passaggi generali eseguiti in questa esercitazione:  

1.  Creare un'istanza di Azure **Data Factory**.
2.  Creare **servizi collegati**:
    1. Un servizio collegato di Archiviazione di Azure per collegare l'account di archiviazione di Azure che include i dati di input.    
    2. Un servizio collegato di Azure SQL per collegare il database SQL di Azure che contiene i dati di output. 
3.  Creare **set di dati**che descrivono dati di input e dati di output per le pipeline.
4.  Creare una **pipeline** con un'attività di copia con BlobSource come origine e SqlSink come sink. 

## <a name="see-also"></a>Vedere anche
| Argomento | Descrizione |
| :---- | :---- |
| [Attività di spostamento dei dati](data-factory-data-movement-activities.md) | Questo articolo fornisce informazioni dettagliate sull'attività di copia usata nell'esercitazione. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo fornisce informazioni sui set di dati in Azure Data Factory.
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. 



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
 


<!--HONumber=Oct16_HO2-->


