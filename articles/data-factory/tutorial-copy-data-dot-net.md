---
title: Copiare dati da un archivio BLOB di Azure al database SQL | Microsoft Docs
description: Questa esercitazione offre istruzioni dettagliate per copiare dati da un archivio BLOB di Azure al database SQL di Azure.
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
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 6f1a93c2906eaab82dcfb9bae1ee4a54dce300bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiare dati da un BLOB di Azure al database SQL di Azure con Azure Data Factory
Azure Data Factory è un servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi. Usando Azure Data Factory è possibile creare e pianificare flussi di lavoro (denominati pipeline) basati sui dati che possono inserire dati da archivi diversi, elaborarli e trasformarli tramite servizi di calcolo come Hadoop di Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e pubblicare l'output in archivi come Azure SQL Data Warehouse per l'uso da parte di applicazioni di business intelligence (BI). 

In questa esercitazione si crea una pipeline di Data Factory che copia i dati da un archivio BLOB di Azure al database SQL di Azure. Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati basato su file a un archivio dati relazionale. Per un elenco degli archivi dati supportati come origini e sink, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare servizi collegati Archiviazione di Azure e Database SQL di Azure.
> * Creare i set di dati del BLOB di Azure e del database SQL di Azure.
> * Creare una pipeline contenente un'attività di copia.
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

In questa esercitazione viene usato .NET SDK. È possibile usare altri meccanismi per interagire con Azure Data Factory. Vedere gli esempi in "Guide introduttive".

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
* **Database SQL di Azure**. Usare il database come archivio dati **sink**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* **Visual Studio** 2015 o 2017. Nella procedura guidata illustrata in questo articolo viene usato Visual Studio 2017.
* **Scaricare e installare [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Creare un'applicazione in Azure Active Directory** seguendo [queste istruzioni](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Annotare i valori seguenti, da usare nei passaggi successivi: **ID applicazione**, **chiave di autenticazione** e **ID tenant**. Assegnare l'applicazione al ruolo "**Collaboratore**" seguendo le istruzioni disponibili nello stesso articolo.

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora il BLOB di Azure e il database SQL di Azure per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **inputEmp.txt** sul disco.

    ```
    John|Doe
    Jane|Doe
    ```

2. Usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per creare il contenitore **adfv2tutorial** e per caricare il file **inputEmp.txt** nel contenitore.

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

1. Usare lo script SQL seguente per creare la tabella **dbo.emp** nel database SQL di Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'opzione **Consenti l'accesso a Servizi di Azure** sia impostata su **SÌ** per il server SQL di Azure in modo che il servizio Data Factory possa scrivere dati nel server. Per verificare e attivare l'impostazione, seguire questa procedura:

    1. Fare clic sull'hub **Altri servizi** a sinistra e selezionare **Server SQL**.
    2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**.
    3. Nella pagina **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso a Servizi Azure**.


## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Creare un'applicazione console .NET in C# con Visual Studio 2015 o 2017.

1. Avviare **Visual Studio**.
2. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**.
3. Selezionare **Visual C#** -> **App console (.NET Framework)** nell'elenco dei tipi di progetto a destra. È necessario .NET versione 4.5.2 o successiva.
4. Immettere **ADFv2Tutorial** come nome.
5. Fare clic su **OK** per creare il progetto.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

1. Fare clic su **Strumenti** -> **Gestione pacchetti NuGet** -> **Console di Gestione pacchetti**.
2. In **Console di Gestione pacchetti** eseguire questi comandi per installare i pacchetti:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Creare un client di data factory

1. Aprire **Program.cs** e includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;

2. Add the following code to the **Main** method that sets variables. Replace place-holders with your own values.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by a data factory can be in other regions.
    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Aggiungere il codice seguente al metodo **Main** per creare un'istanza della classe **DataFactoryManagementClient**. Usare questo oggetto per creare una data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

Aggiungere il codice seguente al metodo **Main** per creare una **data factory**.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Creare servizi collegati

In questa esercitazione si creano due servizi collegati, rispettivamente per l'origine e il sink.

### <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure

Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato di Archiviazione di Azure**. Per informazioni sulle proprietà supportate e altri dettagli, vedere le [proprietà del servizio collegato BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure

Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato Database SQL di Azure**. Per informazioni sulle proprietà supportate e altri dettagli, vedere le [proprietà del servizio collegato Database SQL di Azure](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Creare set di dati

In questa sezione vengono creati due set di dati: uno per l'origine e l'altro per il sink. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Creare un set di dati per il BLOB di Azure di origine

Aggiungere il codice seguente al metodo **Main** per creare un **set di dati del BLOB di Azure**. Per informazioni sulle proprietà supportate e altri dettagli, vedere le [proprietà dei set di dati dei BLOB di Azure](connector-azure-blob-storage.md#dataset-properties).

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente e descrive quanto segue.

- Posizione del BLOB da cui eseguire la copia: **FolderPath** e **FileName**.
- Formato BLOB che indica come analizzare il contenuto: **TextFormat** e relative impostazioni (ad esempio, il delimitatore di colonna).
- Struttura dei dati, inclusi i nomi di colonna e i tipi di dati mappati in questo caso alla tabella SQL sink.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Creare un set di dati per il database SQL di Azure sink

Aggiungere il codice seguente al metodo **Main** per creare un **set di dati del database SQL di Azure**. Per informazioni sulle proprietà supportate e altri dettagli, vedere le [proprietà dei set di dati del database SQL di Azure](connector-azure-sql-database.md#dataset-properties).

Definire un set di dati che rappresenta i dati sink nel database SQL di Azure. Questo set di dati fa riferimento al servizio collegato Database SQL di Azure creato nel passaggio precedente e specifica anche la tabella SQL contenente i dati copiati. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Aggiungere il codice seguente al metodo **Main** per creare una **pipeline con un'attività di copia**. In questa esercitazione, la pipeline contiene soltanto un'attività di copia che usa il set di dati del BLOB come origine e il set di dati SQL come sink. Per informazioni dettagliate sull'attività, vedere la [panoramica dell'attività di copia](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo **Main** per **attivare un'esecuzione della pipeline**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

1. Aggiungere il codice seguente al metodo **Main** per controllare continuamente lo stato dell'esecuzione della pipeline fino al termine della copia dei dati.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Aggiungere il codice seguente al metodo **Main** per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

La console stampa lo stato di avanzamento della creazione di una data factory, del servizio collegato, dei set di dati, della pipeline e della relativa esecuzione. Controlla quindi lo stato di esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come SSMS (SQL Server Management Studio) o Visual Studio per connettersi al database SQL di Azure di destinazione e verificare la presenza dei dati copiati nella tabella specificata.

### <a name="sample-output"></a>Output di esempio

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
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
Creating linked service AzureSqlDbLinkedService...
{
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
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
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
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare servizi collegati Archiviazione di Azure e Database SQL di Azure.
> * Creare i set di dati del BLOB di Azure e del database SQL di Azure.
> * Creare una pipeline contenente un'attività di copia.
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.


Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-powershell.md)
