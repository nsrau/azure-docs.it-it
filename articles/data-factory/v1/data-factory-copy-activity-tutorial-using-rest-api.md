---
title: 'Esercitazione: Usare l''API REST per creare una pipeline di Azure Data Factory | Microsoft Docs'
description: "In questa esercitazione si usa l'API REST per creare una pipeline di Azure Data Factory con un'attività di copia per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: fbf538383a96fc1789f54994c1c4a1c1d9f96bec
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Esercitazione: Usare l'API REST per creare una pipeline di Azure Data Factory per copiare dati 
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

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere l'[esercitazione sull'attività di copia nella documentazione sulla versione 2](../quickstart-create-data-factory-rest-api.md). 

Questo articolo illustra l'uso dell'API REST per creare una data factory con una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL di Azure. Se non si ha familiarità con Azure Data Factory, prima di eseguire questa esercitazione vedere l'articolo [Introduzione ad Azure Data Factory](data-factory-introduction.md).   

In questa esercitazione si crea una pipeline contenente una sola attività: un'attività di copia che copia i dati da un archivio dati supportato a un archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md).

Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [Attività multiple in una pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Questo articolo non illustra tutte le API REST di Data Factory. Per la documentazione completa sui cmdlet di Data Factory, vedere [Informazioni di riferimento sull'API REST di Data Factory](/rest/api/datafactory/) .
>  
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare una pipeline per trasformare i dati usando un cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti
* Vedere [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ed eseguire i passaggi relativi ai **prerequisiti** .
* Installare [Curl](https://curl.haxx.se/dlwiz/) nel computer. Lo strumento Curl viene usato insieme ai comandi REST per creare una data factory. 
* Seguire le istruzioni disponibili in [questo articolo](../../azure-resource-manager/resource-group-create-service-principal-portal.md) per: 
  1. Creare un'applicazione Web denominata **ADFCopyTutorialApp** in Azure Active Directory.
  2. Ottenere i valori per l'**ID client** e la **chiave privata**. 
  3. Ottenere l' **ID tenant**. 
  4. Assegnare l'applicazione **ADFCopyTutorialApp** al ruolo **Collaboratore Data Factory**.  
* Installare [Azure PowerShell](/powershell/azure/overview).  
* Avviare **PowerShell** e seguire questa procedura. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
  
  1. Eseguire questo comando e immettere il nome utente e la password usati per accedere al portale di Azure:
    
    ```PowerShell 
    Login-AzureRmAccount
    ```   
  2. Eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```PowerShell     
    Get-AzureRmSubscription
    ``` 
  3. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **&lt;NameOfAzureSubscription**&gt; con il nome della sottoscrizione di Azure. 
     
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
  4. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo questo comando in PowerShell:  

    ```PowerShell     
      New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
     
      Se il gruppo di risorse esiste già, specificare se deve essere aggiornato (Y) o mantenuto invariato (N). 
     
      Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato ADFTutorialResourceGroup. Se si usa un gruppo di risorse diverso, è necessario usare il nome del gruppo di risorse invece di ADFTutorialResourceGroup in questa esercitazione.

## <a name="create-json-definitions"></a>Creare le definizioni JSON
Creare i file JSON seguenti nella cartella che include curl.exe. 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Il nome deve essere univoco a livello globale, quindi è consigliabile aggiungere un prefisso/suffisso ad ADFCopyTutorialDF per renderlo univoco. 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso nelle risorse di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).

```JSON
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

Per informazioni dettagliate sulle proprietà JSON, vedere [Servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### <a name="azuersqllinkedservicejson"></a>azuersqllinkedservice.json
> [!IMPORTANT]
> Sostituire **servername**, **databasename**, **username** e **password** con il nome del server di Azure SQL, il nome del database SQL, l'account utente e la password per l'account.  
> 
>

```JSON
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
```

Per informazioni dettagliate sulle proprietà JSON, vedere la sezione relativa al [servizio collegato SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
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

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
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

### <a name="pipelinejson"></a>pipeline.json

```JSON
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
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Tenere presente quanto segue:

- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Nelle soluzioni Data Factory è anche possibile usare le [attività di trasformazione dei dati](data-factory-data-transformation-activities.md).
- L'input per l'attività è impostato su **AzureBlobInput** e l'output per l'attività è impostato su **AzureSqlOutput**. 
- Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Per un elenco completo degli archivi dati supportati dall'attività di copia come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Per informazioni su come usare uno specifico archivio dati supportato come origine/sink, fare clic sul collegamento nella tabella.  
 
Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. È possibile specificare solo la parte relativa alla data e ignorare la parte relativa all'ora, ad esempio "2017-02-03", che equivale a "2017-02-03T00:00:00Z".
 
Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2016-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. 
 
Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.
 
Nell'esempio precedente sono visualizzate 24 sezioni di dati, perché viene generata una sezione di dati ogni ora.

Per le descrizioni delle proprietà JSON nella definizione di una pipeline, vedere l'articolo su come [creare pipeline](data-factory-create-pipelines.md). Per le descrizioni delle proprietà JSON nella definizione di un'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Per le descrizioni delle proprietà JSON supportate da BlobSource, vedere l'articolo relativo al [connettore BLOB di Azure](data-factory-azure-blob-connector.md). Per le descrizioni delle proprietà JSON supportate da SqlSink, vedere l'articolo relativo al [connettore del database SQL di Azure](data-factory-azure-sql-connector.md).

## <a name="set-global-variables"></a>Configurare le variabili globali
In Azure PowerShell eseguire i comandi seguenti dopo avere sostituito i valori con quelli personalizzati:

> [!IMPORTANT]
> Per istruzioni su come ottenere i valori per ID client, segreto client, ID tenant e ID sottoscrizione, vedere la sezione [Prerequisiti](#prerequisites) .   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Dopo aver aggiornato il nome della data factory usata, eseguire questo comando: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Eseguire l'autenticazione con AAD
Eseguire questo comando per l'autenticazione con Azure Active Directory (AAD): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Creare un'istanza di Data Factory
In questo passaggio si crea un'istanza di Azure Data Factory denominata **ADFCopyTutorialDF**. Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, un'attività di copia per copiare dati da un'origine a un archivio dati di destinazione. Un'attività Hive HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. Eseguire i comandi seguenti per creare la data factory: 

1. Assegnare il comando alla variabile denominata **cmd**. 
   
    > [!IMPORTANT]
    > Verificare che il nome della data factory specificato qui (ADFCopyTutorialDF) corrisponda al nome specificato in **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Eseguire il comando usando **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Visualizzare i risultati. Se la data factory è stata creata correttamente, in **results** viene visualizzato il codice JSON per la data factory. In caso contrario, viene visualizzato un messaggio di errore.  
   
    ```
    Write-Host $results
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per la Data factory di Azure. Se nei risultati viene visualizzato l'errore **Il nome "ADFCopyTutorialDF" per la data factory non è disponibile**, seguire questa procedura:  
  
  1. Cambiare il nome, ad esempio nomeutenteADFCopyTutorialDF, nel file **datafactory.json** .
  2. Nel primo comando in cui viene assegnato un valore alla variabile **$cmd** sostituire ADFCopyTutorialDF con il nuovo nome ed eseguire il comando. 
  3. Eseguire i due comandi seguenti per richiamare l'API REST per creare la data factory e stampare i risultati dell'operazione. 
     
     Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md) .
* Per creare istanze di data factory, è necessario essere un collaboratore/amministratore della sottoscrizione di Azure.
* Il nome della data factory può essere registrato come nome DNS in futuro e quindi divenire visibile pubblicamente.
* Se viene visualizzato l'errore "**La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**", eseguire una di queste operazioni e provare a ripetere la pubblicazione: 
  
  * In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory: 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    È possibile eseguire questo comando per verificare che il provider di Data Factory sia registrato. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Accedere usando la sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) e passare al pannello Data Factory oppure creare un'istanza di Data Factory nel portale di Azure. Questa azione registra automaticamente il provider.

Prima di creare una pipeline è necessario creare alcune entità di Data factory. Prima di tutto, creare i servizi collegati per collegare gli archivi dati di origine e di destinazione al proprio archivio dati. Quindi, definire i set di dati di input e di output per rappresentare i dati in archivi dati collegati. Infine, creare la pipeline con un'attività che usa questi set di dati.

## <a name="create-linked-services"></a>Creazione di servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione non si usano servizi di calcolo come Azure HDInsight o Azure Data Lake Analytics, ma due archivi dati di tipo Archiviazione di Azure (origine) e database SQL di Azure (destinazione). Si creano quindi due servizi collegati denominati AzureStorageLinkedService e AzureSqlLinkedService di tipo AzureStorage e AzureSqlDatabase.  

AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. L'account di archiviazione è quello in cui, come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), è stato creato un contenitore e sono stati caricati i dati.   

AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata la tabella emp in questo database.  

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. In questa sezione si specificano il nome e la chiave dell'account di archiviazione di Azure. Per informazioni dettagliate sulle proprietà JSON usate per definire un servizio collegato di Archiviazione di Azure, vedere [Servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).  

1. Assegnare il comando alla variabile denominata **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Eseguire il comando usando **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Visualizzare i risultati. Se il servizio collegato è stato creato correttamente, in **results** viene visualizzato il codice JSON per il servizio collegato. In caso contrario, viene visualizzato un messaggio di errore.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Creare un servizio collegato di Azure SQL
In questo passaggio il database SQL di Azure viene collegato alla data factory. In questa sezione si specificano il nome del server di Azure SQL, il nome del database, il nome utente e la password utente. Per informazioni dettagliate sulle proprietà JSON usate per definire un servizio collegato di Azure SQL, vedere [Servizio collegato Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

1. Assegnare il comando alla variabile denominata **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Eseguire il comando usando **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Visualizzare i risultati. Se il servizio collegato è stato creato correttamente, in **results** viene visualizzato il codice JSON per il servizio collegato. In caso contrario, viene visualizzato un messaggio di errore.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Creare set di dati
Nel passaggio precedente sono stati creati servizi collegati per collegare l'account di archiviazione di Azure e un database SQL di Azure alla data factory. In questo passaggio vengono definiti due set di dati denominati AzureBlobInput e AzureSqlOutput, che rappresentano i dati di input e di output memorizzati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService e AzureSqlLinkedService.

Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input (AzureBlobInput) specifica il contenitore e la cartella che contiene i dati di input.  

Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output (OututDataset) specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB. 

### <a name="create-input-dataset"></a>Creare set di dati di input
In questo passaggio viene creato un set di dati denominato AzureBlobInput che punta a un file BLOB (emp.txt) nella cartella radice di un contenitore BLOB (adftutorial) nella risorsa di archiviazione di Azure rappresentata dal servizio collegato AzureStorageLinkedService. Se non si specifica un valore per fileName (o lo si ignora), i dati di tutti i BLOB della cartella di input vengono copiati nella destinazione. In questa esercitazione si specifica un valore per fileName. 

1. Assegnare il comando alla variabile denominata **cmd**. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Eseguire il comando usando **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, in **results** viene visualizzato il codice JSON per il set di dati. In caso contrario, viene visualizzato un messaggio di errore.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Creare il set di dati di output
Il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure. Il set di dati della tabella SQL di output (OututDataset) creato in questo passaggio specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB.

1. Assegnare il comando alla variabile denominata **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Eseguire il comando usando **Invoke-Command**.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, in **results** viene visualizzato il codice JSON per il set di dati. In caso contrario, viene visualizzato un messaggio di errore.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'**attività di copia** che usa **AzureBlobInput** come input e **AzureSqlOutput** come output.

Attualmente, è il set di dati di output a determinare la pianificazione. In questa esercitazione il set di dati di output viene configurato per generare una sezione una volta ogni ora. La pipeline ha un'ora di inizio e un'ora di fine intervallate da un giorno, ovvero 24 ore. Vengono quindi generate dalla pipeline 24 sezioni di set di dati di output. 

1. Assegnare il comando alla variabile denominata **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Eseguire il comando usando **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, in **results** viene visualizzato il codice JSON per il set di dati. In caso contrario, viene visualizzato un messaggio di errore.  

    ```PowerShell   
    Write-Host $results
    ```

**Congratulazioni.** È stata creata una data factory di Azure con una pipeline che copia dati da un archivio BLOB di Azure al database SQL di Azure.

## <a name="monitor-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usata l'API REST di Azure Data Factory per monitorare le sezioni prodotte dalla pipeline.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Assicurarsi che l'ora di inizio e l'ora di fine specificate nel comando seguente corrispondano a quelle della pipeline. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Eseguire Invoke-Command e il comando successivo fino a quando lo stato di una sezione non sarà **Pronto** o **Non riuscito**. Quando lo stato della sezione è Pronta, cercare dati di output nella tabella **emp** del database SQL di Azure. 

Per ogni sezione vengono copiate due righe di dati dal file di origine nella tabella emp del database SQL di Azure. Nella tabella emp vengono quindi visualizzati 24 nuovi record quando tutte le sezioni sono state elaborate correttamente e hanno stato Pronta. 

## <a name="summary"></a>Riepilogo
In questa esercitazione è stata usata l'API REST per creare una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. Ecco i passaggi generali eseguiti in questa esercitazione:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare **servizi collegati**:
   1. Un servizio collegato di Archiviazione di Azure per collegare l'account di archiviazione di Azure che include i dati di input.     
   2. Un servizio collegato di Azure SQL per collegare il database SQL di Azure che contiene i dati di output. 
3. Creare **set di dati**che descrivono dati di input e dati di output per le pipeline.
4. Creare una **pipeline** con un'attività di copia con BlobSource come origine e SqlSink come sink. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati usati l'archivio BLOB di Azure come archivio dati di origine e un database SQL di Azure come archivio dati di destinazione in un'operazione di copia. La tabella seguente contiene un elenco degli archivi dati supportati come origini e come destinazioni dall'attività di copia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Per informazioni su come copiare dati da/in un archivio dati, fare clic sul collegamento relativo all'archivio dati nella tabella.
